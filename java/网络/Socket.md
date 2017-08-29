# java Socket 通信

## Socket 使用的一般流程

### 初始化 Socket 对象

- 客户端

  ```java
  Socket socket = new Socket("127.0.0.1", 4700);
  ```

  直接新建一个 Socket 对象

- 服务端

  ```java
  try {
    mServerSocket = new ServerSocket(4700);
    mSocket = mServerSocket.accept();
  } catch (IOException | NullPointerException e) {
    e.printStackTrace();
  }
  ```

  新建 ServerSocket 监听本地端口，获取 ServerSocket.accept() 方法获取 Socket 对象。

### 通过 Socket 获取输入

- 获取输入流

  ```java
  mSocket.getInputStream();
  ```

- 包装输入流

  ```java
  mIn = new BufferedReader(new InputStreamReader(mSocket.getInputStream()));
  ```

- 从包装后的输入流可以直接获取字符串

  ```java
  String line = mIn.readLine();
  ```

- 循环监听输入，并处理

### 通过 Socket 输出

- 获取输出流

  ```java
  mSocket.getOutputStream();
  ```

- 包装输出流

  ```java
  mOut = new PrintWriter(mSocket.getOutputStream());
  ```

- 包装后的输出流可以直接打印输出字符串

  ```java
  // 发送消息
  mOut.println(line);
  mOut.flush();
  ```

## java Socket Server

- 新建 ServerSocket
- 从 SocketServer 获取 Socket
- 从 Socket 获取输入、输出对象
- 新建线程监听 Socket 输入
- 监听键盘输入，发送消息

```java
public class SocketServer {
    private ServerSocket mServerSocket;
    private Socket mSocket;
    private BufferedReader mIn;
    private PrintWriter mOut;
    private Thread mListenerThread;
    private boolean mStop = false;

    private SocketServer() {
        // 初始化
        try {
            mServerSocket = new ServerSocket(4700);
            mSocket = mServerSocket.accept();
            mIn = new BufferedReader(new InputStreamReader(mSocket.getInputStream()));
            mOut = new PrintWriter(mSocket.getOutputStream());
        } catch (IOException | NullPointerException e) {
            e.printStackTrace();
        }
    }

    private void start() {
        if (mIn == null) {
            System.out.println("input is null");
            return;
        }

        // 新建线程监听输入
        mListenerThread = new Thread(new Runnable() {
            public void run() {
                try {
                    System.out.println("socket 连接已建立");
                    while (!mStop) {
                        String line = mIn.readLine();
                        if (line != null ) {
                            System.out.println("Client : "+line);
                        }
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        });
        mListenerThread.start();

        // 初始化键盘输入监听
        BufferedReader sin = new BufferedReader(new InputStreamReader(System.in));
        try {
            String line = sin.readLine();
            while (line!= null && !line.equals("bye")&& !line.isEmpty() && !mStop){
                // 发送消息
                mOut.println(line);
                mOut.flush();

                // 打印发送的消息
                System.out.println("Server : "+line);
                 line = sin.readLine();
            }
            mStop = true;
            mIn.close();
            mOut.close();
            mSocket.close();
            mServerSocket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        SocketServer server = new SocketServer();
        server.start();
    }

}
```

### 已知 Bug

输入 bye 后，不能立即结束 Server ，需要在接收到一次客户端发送的消息才会结束。和 Thread 的结束方式相关。

## java Socket Client

```java
public class SocketClient {
    public static void main(String[] args) {
        try {
            Socket socket = new Socket("127.0.0.1", 4700);
            BufferedReader sin = new BufferedReader(new InputStreamReader(System.in));
            PrintWriter os = new PrintWriter(socket.getOutputStream());
            BufferedReader is = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            String readline;
            readline = sin.readLine();
            while (!readline.equals("bye")) {
                os.println(readline);
                os.flush();
                System.out.println("Client:" + readline);
                System.out.println("Server:" + is.readLine());
                readline = sin.readLine();
            }
            os.close();
            is.close();
            socket.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## androdi socket client

- EditText 输入
- 点击 Button 发送

```java
public class SocketActivity extends Activity {
    public static final String HOST = "192.168.1.70";
    public static final int PORT = 4700;

    private TextView mTvShow;
    private EditText mEdtMsg;

    private Socket mSocket;
    private BufferedReader mIn = null;
    private PrintWriter mOut = null;
    private String mContent = "";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_socket);

        initSocket();

        mTvShow = (TextView) findViewById(R.id.tvShow);
        mEdtMsg = (EditText) findViewById(R.id.edtMsg);
        Button button = (Button) findViewById(R.id.btnSend);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                sendMsg();
            }
        });
    }

    private void sendMsg() {
        final String msg = mEdtMsg.getText().toString();
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                if (mSocket.isConnected()) {
                    if (!mSocket.isOutputShutdown()) {
                        mOut.println(msg);
                    }
                }
            }
        });
        thread.start();
    }

    private void initSocket() {

        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    mSocket = new Socket(HOST, PORT);
                    mIn = new BufferedReader(new InputStreamReader(mSocket
                            .getInputStream()));
                    mOut = new PrintWriter(new BufferedWriter(new OutputStreamWriter(
                            mSocket.getOutputStream())), true);
                    while (true) {
                        if (!mSocket.isClosed()) {
                            if (mSocket.isConnected()) {
                                if (!mSocket.isInputShutdown()) {
                                    String content = mIn.readLine();
                                    if (content != null) {
                                        content += "\n";
                                        mContent += content;
                                        runOnUiThread(new Runnable() {
                                            @Override
                                            public void run() {
                                                mTvShow.setText(mContent);
                                            }
                                        });
                                    } else {

                                    }
                                }
                            }
                        }
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
        thread.start();
    }
}
```
