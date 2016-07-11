## 流程
- 注册 163 邮箱后，开启 POP3/SMTP/IMAP 功能；
- 163 强制开启，客户端授权密码；
- 密码要使用客户端授权密码，而不是登录密码。
```java
public class SendmailUtil {

    // 设置服务器
    private static String KEY_SMTP = "mail.smtp.host";
    private static String VALUE_SMTP = "smtp.163.com";
    // 服务器验证
    private static String KEY_PROPS = "mail.smtp.auth";
    // 发件人用户名、密码
    private String SEND_USER = "shuichengchuxing@163.com";
    private String SEND_UNAME = "shuichengchuxing";
    private String SEND_PWD = "******";
    // 建立会话
    private MimeMessage mMessage;
    private Session mSession;

    /*
     * 初始化方法
     */
    public SendmailUtil() {
        Properties props = System.getProperties();
        props.setProperty(KEY_SMTP, VALUE_SMTP);
        props.put(KEY_PROPS, "true");
        mSession =  Session.getDefaultInstance(props, new Authenticator(){
              protected PasswordAuthentication getPasswordAuthentication() {
                  return new PasswordAuthentication(SEND_UNAME, SEND_PWD);
              }});
        mSession.setDebug(true);
        mMessage = new MimeMessage(mSession);
    }

    /**
     * 发送邮件
     *
     * @param headName
     *            邮件头文件名
     * @param sendHtml
     *            邮件内容
     * @param receiveUser
     *            收件人地址
     */
    public void doSendHtmlEmail(String headName, String sendHtml,
            String receiveUser) {
        try {
            // 发件人
            InternetAddress from = new InternetAddress(SEND_USER);
            mMessage.setFrom(from);
            // 收件人
            InternetAddress to = new InternetAddress(receiveUser);
            mMessage.setRecipient(Message.RecipientType.TO, to);
            // 邮件标题
            mMessage.setSubject(headName);
            String content = sendHtml.toString();
            // 邮件内容,也可以使纯文本"text/plain"
            mMessage.setContent(content, "text/html;charset=utf-8");
            mMessage.saveChanges();
            Transport transport = mSession.getTransport("smtp");
            // smtp验证，就是你用来发邮件的邮箱用户名密码
            transport.connect(VALUE_SMTP, SEND_UNAME, SEND_PWD);
            // 发送
            transport.sendMessage(mMessage, mMessage.getAllRecipients());
            transport.close();
            System.out.println("send success!");
        } catch (AddressException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (MessagingException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        SendmailUtil se = new SendmailUtil();
        se.doSendHtmlEmail("2016-7-8 水城出行 用户反馈 ", "反馈消息： 你做的是个毛线啊！", "shuichengchuxing@163.com");
    }
}
```
## 参考链接
- [java发送邮件完整实例 java邮件工具类](http://yuncode.net/code/c_552a2e2dc593894)
- [ javamail 发送邮件](http://blog.csdn.net/hjxyshell/article/details/50480071)
- [java mail Api 下载地址](http://www.oracle.com/technetwork/java/javasebusiness/downloads/java-archive-downloads-eeplat-419426.html#javamail-1.4.7-oth-JPR)
