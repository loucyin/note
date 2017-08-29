# Java RMI

RMI(Remote Method Invoke) : 远程方法执行。

RMI server 将接口注册到 Registry ，客户端通过 Registry 获取到接口对象，调用接口的方法，完成远程调用。

## 接口
```java
public interface RemoteService extends Remote{
    public String sayHello(String name) throws RemoteException;
}
```

## 实现
```java
public class RemoteServiceImpl extends UnicastRemoteObject implements RemoteService{
    protected RemoteServiceImpl() throws RemoteException {
    }

    @Override
    public String sayHello(String name) throws RemoteException {
        String msg = String.format("hello %s",name);
        System.out.println(msg);
        return msg;
    }
}
```

## 服务端
```java
public static void main(String[] args){
    try {
        Registry registry = LocateRegistry.createRegistry(20036);
        RemoteServiceImpl service = new RemoteServiceImpl();
        registry.bind("demoService",service);
    } catch (RemoteException | AlreadyBoundException e) {
        e.printStackTrace();
    }
}
```

## 客户端
```java
public static void main(String[] args){
    try {
        Registry registry = LocateRegistry.getRegistry(20036);
        RemoteService service = (RemoteService) registry.lookup("demoService");
        if(service != null){
            String msg = service.sayHello("client");
            System.out.println(msg);
        }
    } catch (RemoteException | NotBoundException e) {
        e.printStackTrace();
    }
}
```

## 参考链接
- [JAVA中几种常用的RPC框架介绍](http://blog.csdn.net/zhaowen25/article/details/45443951)
