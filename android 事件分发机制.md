# android的事件分发机制

## 事件的基础:
- 按下(ACTION_DOWN)
- 移动(ACTION_MOVE)
- 抬起(ACTION_UP)  

## 与事件分发相关的方法：
[View.java](#jump)

```
public boolean dispatchTouchEvent(MotionEvent event)
public boolean onTouchEvent(MotionEvent event)
```

ViewGroup.java

```
public boolean dispatchTouchEvent(MotionEvent event)
public boolean onTouchEvent(MotionEvent event)
public boolean onInterceptTouchEvent(MotionEvent event)
```

- dispatchTouchEvent方法用于事件的分发，Android中所有的事件都必须经过这个方法的分发，然后决定是自身消费当前事件还是继续往下分发给子控件处理。返回true表示不继续分发，事件没有被消费。返回false则继续往下分发，如果是ViewGroup则分发给onInterceptTouchEvent进行判断是否拦截该事件。
- onTouchEvent方法用于事件的处理，返回true表示消费处理当前事件，返回false则不处理，交给子控件进行继续分发。
- onInterceptTouchEvent是ViewGroup中才有的方法，View中没有，它的作用是负责事件的拦截，返回true的时候表示拦截当前事件，不继续往下分发，交给自身的onTouchEvent进行处理。返回false则不拦截，继续往下传。这是ViewGroup特有的方法，因为ViewGroup中可能还有子View。  

## 事件的分发

事件的分发从Activity的dispatchTouchEvent开始，由外层ViewGroup一层一层传递给能接收此事件的最小内层View。事件的处理由最内层view,依次向外传递，直到事件被消费掉。

**事件的分发也有消费能力，dispatchTouchEvent返回true，就表示事件被消费掉了，就不在继续向下分发了。**  

重写activity的dispatchTouchEvent返回true，则所有的点击事件都会被屏蔽掉，世界从此就安静了。  

### 参考文章：<span id="jump"/>
- [Android事件机制之一：事件传递和消费](http://www.cnblogs.com/lwbqqyumidi/p/3500997.html)
- [Android事件传递机制](http://www.infoq.com/cn/articles/android-event-delivery-mechanism/)  
