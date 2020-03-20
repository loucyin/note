# matplotlib 基本用法

## 折线图

## Ubuntu 中文乱码

通过代码设置：

```python
import matplotlib
matplotlib.rcParams['font.sans-serif'] = ['SimHei']
matplotlib.rcParams['axes.unicode_minus'] = False
```

修改配置/usr/local/lib/python*/dist-packages/matplotlib/mpl-data/matplotlibrc文件：

```bash
# 搜索font.family配置项，将其#注释去掉，并将：号后面的值改为字段对应的名字。
font.family         : SimHei
# 搜索axes.unicode_minus配置项，将其#注释去掉，并将：号后面的值改为False
axes.unicode_minus  : False
```