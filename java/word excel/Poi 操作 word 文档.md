# Poi 操作 word 文档

## 依赖
```groovy
compile group: 'org.apache.poi', name: 'poi', version: '3.15'
compile group: 'org.apache.poi', name: 'poi-ooxml', version: '3.15'
compile group: 'org.apache.poi', name: 'ooxml-schemas', version: '1.3'
```

## 分页
- 分页后会带格式
```java
XWPFParagraph paragraph = document.createParagraph();
paragraph.setPageBreak(true);
```

- 分页后没格式
```java
XWPFParagraph paragraph = destDoc.createParagraph();
XWPFRun run = paragraph.createRun();
run.addBreak(BreakType.PAGE);
```

## 复制文档
```java
public static void copyDocument(XWPFDocument srcDoc, XWPFDocument destDoc) {
    if (srcDoc == null || destDoc == null) {
        return;
    }
    copyLayout(srcDoc, destDoc);
    for (IBodyElement bodyElement : srcDoc.getBodyElements()) {
        BodyElementType elementType = bodyElement.getElementType();
        if (elementType == BodyElementType.PARAGRAPH) {
            XWPFParagraph srcPr = (XWPFParagraph) bodyElement;
            XWPFParagraph dstPr = destDoc.createParagraph();

            CTPPr srcPPr = srcPr.getCTP().getPPr();
            CTPPr destPPr = dstPr.getCTP().addNewPPr();
            destPPr.set(srcPPr);
            for (XWPFRun srcRun : srcPr.getRuns()) {
                // 复制文字
                XWPFRun destRun = dstPr.createRun();
                CTRPr ctrPr = destRun.getCTR().addNewRPr();
                ctrPr.set(srcRun.getCTR().getRPr());
                destRun.setText(srcRun.getText(srcRun.getTextPosition()), srcRun.getTextPosition());

                // 复制图片
                if (srcRun.getEmbeddedPictures().size() > 0) {
                    for (XWPFPicture pic : srcRun.getEmbeddedPictures()) {
                        byte[] img = pic.getPictureData().getData();
                        int width = (int) pic.getCTPicture().getSpPr().getXfrm().getExt().getCx();
                        int height = (int) pic.getCTPicture().getSpPr().getXfrm().getExt().getCy();
                        int pictureType = pic.getPictureData().getPictureType();
                        String fileName = pic.getPictureData().getFileName();
                        InputStream inputStream = new ByteInputStream(img, img.length);
                        try {
                            destRun.addPicture(inputStream, pictureType, fileName, width, height);
                        } catch (InvalidFormatException | IOException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        } else if (elementType == BodyElementType.TABLE) {
            XWPFTable table = (XWPFTable) bodyElement;
            destDoc.createTable();
            int pos = destDoc.getTables().size() - 1;
            destDoc.setTable(pos, table);
        }
    }
}
public static void copyLayout(XWPFDocument srcDoc, XWPFDocument destDoc) {
    CTPageMar pgMar = srcDoc.getDocument().getBody().getSectPr().getPgMar();
    BigInteger bottom = pgMar.getBottom();
    BigInteger footer = pgMar.getFooter();
    BigInteger gutter = pgMar.getGutter();
    BigInteger header = pgMar.getHeader();
    BigInteger left = pgMar.getLeft();
    BigInteger right = pgMar.getRight();
    BigInteger top = pgMar.getTop();

    CTPageMar addNewPgMar = destDoc.getDocument().getBody().addNewSectPr().addNewPgMar();
    addNewPgMar.setBottom(bottom);
    addNewPgMar.setFooter(footer);
    addNewPgMar.setGutter(gutter);
    addNewPgMar.setHeader(header);
    addNewPgMar.setLeft(left);
    addNewPgMar.setRight(right);
    addNewPgMar.setTop(top);

    CTPageSz pgSzSrc = srcDoc.getDocument().getBody().getSectPr().getPgSz();
    if(pgSzSrc != null) {
        BigInteger code = pgSzSrc.getCode();
        BigInteger h = pgSzSrc.getH();
        STPageOrientation.Enum orient = pgSzSrc.getOrient();
        BigInteger w = pgSzSrc.getW();

        CTPageSz addNewPgSz = destDoc.getDocument().getBody().addNewSectPr().addNewPgSz();
        addNewPgSz.setCode(code);
        addNewPgSz.setH(h);
        addNewPgSz.setOrient(orient);
        addNewPgSz.setW(w);
    }
}
```

## 参考链接
-[How to copy a paragraph of .docx to another .docx withJava and retain the style](https://stackoverflow.com/questions/10208097/how-to-copy-a-paragraph-of-docx-to-another-docx-withjava-and-retain-the-style)
