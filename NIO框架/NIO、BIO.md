# BIO/NIO/AIO结合Socket编程实现

2018年11月10日 18:20:51  更多



版权声明：本文为博主原创文章，遵循[ CC 4.0 BY-SA ](http://creativecommons.org/licenses/by-sa/4.0/)版权协议，转载请附上原文出处链接和本声明。本文链接：https://blog.csdn.net/shuduti/article/details/83931890

## 基本概念

Socket又称“套接字”，应用程序通过“套接字”向网络发出请求或者应答网络请求。Socket和SocketServer类库位于java.net包中，ServerSocket用于服务器端，Socket是建立网络链接使用的。在连接成功时，应用程序两端会产生一个Socket实例，操作这个实例，完成所需的会话。对于一个网络连接来说，套接字是平等的，不因为在服务器端或在客户端而产生不同的级别。不管是Socket还是ServerSocket它们的工作都是通过SocketImpl类及其子类完成的。套接字之间的连接过程可以分为四个步骤：

- （1）服务器监听：是服务器端套接字并不定位具体的客户端套接字，而是出于平等连接的状态，实时监控网络状态。
- （2）客户端请求：指由客户端的套接字提出连接请求，要连接的目标是服务器端的套接字。为此，客户端的套接字必须首先描述它要连接的服务器套接字，指出服务器套接字的地址和端口号，然后向服务器端套接字提出连接请求。
- （3）服务器连接确认：当服务器端套接字接收到客户端的套接字的连接请求，它就响应客户端套接字的请求，建立一个新的线程，把服务器端套接字的描述发给客户端。
- （4）客户端连接确认：一旦客户端确认了此描述，连接就建立好了，双方开始进行通信。而服务器端套接字出于监听状态，继续接受其他客户端套接字的连接请求。

IO（BIO）和NIO的区别，其本质就是阻塞和非阻塞的区别。

- 阻塞：应用程序在获取网络数据的时候，如果网络传输数据很慢，那么程序就一直等待，直到传输完毕为止。
- 非阻塞：应用程序直接可以获取已经准备就绪的数据，无须等待。

IO为同步阻塞形式，NIO为同步非阻塞形式。NIO并没有实现异步，在JDK1.7之后，升级了NIO库包，支持异步非阻塞通信模型即NIO2.0（AIO）。同步和异步一般是面向操作系统与应用程序对IO操作的层面来区别。

- 同步时：应用程序会直接参与IO读写操作，并且我们的应用程序会直接阻塞到某一个方法上，直到数据准备就绪；或者采用轮询的策略实时检查数据的就绪状态，如果就绪则获取数据。
- 异步时：则所有的IO读写操作交给操作系统处理，与我们的应用程序没有直接关系，我们程序不需要关系IO读写，当操作系统完成IO读写操作时，会给我们的应用程序发送通知，我们的应用程序直接拿走数据即可。

同步说的是server服务端的执行方式，阻塞说的是具体的技术，接收数据的方式、状态（io,nio）。

```
public class Server {

	final static int PORT = 8888;
	
	public static void main(String[] args) {
		ServerSocket server = null;
		try{
			server = new ServerSocket(PORT);
			System.out.println("server start...");
			//进行阻塞
			Socket socket = server.accept();
			//新建一个线程执行客户端的任务
			new Thread(new ServerHandler(socket)).start();
		} catch (Exception e){
			e.printStackTrace();
		} finally{
			if(server != null){
				try {
					server.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
			server = null;
		}
	}

}
12345678910111213141516171819202122232425262728
public class ServerHandler implements Runnable {

	private Socket socket;
	
	public ServerHandler(Socket socket){
		this.socket = socket;
	}
	
	@Override
	public void run() {
		BufferedReader in = null;
		PrintWriter out = null;
		
		try {
			in = new BufferedReader(new InputStreamReader(this.socket.getInputStream()));
			out = new PrintWriter(this.socket.getOutputStream());
			String body = null;
			while(true){
				body = in.readLine();
				if(null == body) break;
				System.out.println("Server: " + body);
				out.println(body);
			}
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			try {
				in.close();
				out.close();
				socket.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		
	}

}
1234567891011121314151617181920212223242526272829303132333435363738
public class Client {

	final static String ADDRESS = "127.0.0.1";
	final static int PORT = 8888;
	
	public static void main(String[] args) {
		Socket socket = null;
		BufferedReader in = null;
		PrintWriter out = null;
		
		try {
			socket = new Socket(ADDRESS, PORT);
			in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
			out = new PrintWriter(socket.getOutputStream(),true);
			
			//向服务器端发送数据
			out.println("接受到客户端的请求数据...");
			String response = in.readLine();
			System.out.println("Client: " + response);
		} catch (UnknownHostException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally{
			try {
				in.close();
				out.close();
				socket.close();
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}

}
1234567891011121314151617181920212223242526272829303132333435363738
```

## 传统的BIO（Blocking IO）编程

网络编程的基本模型是Client/Sever模型，也就是两个进程直接进行相互通信，其中服务端提供配置信息（绑定端口的ip地址和监听端口），客户端通过连接操作箱服务器端监听的地址发起连接请求，通过三次握手建立连接，如果连接成功，则双方即可以进行通信（网络套接字socket）。

## 伪异步IO

采用线程池和任务队列可以实现一种伪异步IO通信框架。在学习过连接池和队列的使用，我们将客户端的socket封装成一个task任务（实现Runnable接口的类）然后投递到线程池中去，配置相应的队列进行实现。

```
/**
 * 修改Server，增加HandlerExecutorPool处理数据
 * */
public class Server {

	final static int PORT = 8888;
	
	public static void main(String[] args) {
		ServerSocket server = null;
		Socket socket = null;
		try{
			server = new ServerSocket(PORT);
			System.out.println("server start...");
			HandlerExecutorPool executorPool = new HandlerExecutorPool(50,1000);
			while(true){
				socket = server.accept();
				executorPool.execute(new ServerHandler(socket));
			}
		} catch (Exception e){
			e.printStackTrace();
		} finally{
			if(server != null){
				try {
					server.close();
					socket.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
			server = null;
			socket = null;
		}
	}

}
1234567891011121314151617181920212223242526272829303132333435
public class HandlerExecutorPool {

	private ExecutorService executor;
	
	public HandlerExecutorPool(int maxPoolSize,int queueSize){
		this.executor = new ThreadPoolExecutor(
				Runtime.getRuntime().availableProcessors(),
				maxPoolSize,
				120L, 
				TimeUnit.SECONDS,
				new ArrayBlockingQueue<Runnable>(queueSize));
	}
	
	public void execute(Runnable task){
		this.executor.execute(task);
	}
}
1234567891011121314151617
```

## NIO编程

NIO（Non-Block IO），即非阻塞IO。它包含以下几个概念：

- Buffer（缓冲区）
- Channel（管道、通道）
- Selector（选择器、多路复用器）

![img](https://github.com/Dawn-JT/blog_util/blob/master/NIO.PNG?raw=true)

### Buffer

Buffer是一个对象，它包含一些要写入或者读取的数据。在NIO类库中加入Buffer对夏宁，体现了新库与原IO的一个重要区别。在面向流的IO中，可以将数据直接写入或读取到Stream对象中。在NIO库中，所有的数据都是用缓冲区处理的。缓冲区实质上是一个数组，通常它是一个字节数组（ByteBuffer），也可以使用其他类型的数组。这个数组为缓冲区提供了数据的访问读写等操作属性，如位置、容量、上限等概念。我们常用的就是ByteBuffer，实际上每一种java基本类型都对应了一种缓冲区（除了Boolean类型）。

- ByteBuffer
- CharBuffer
- ShortBuffer
- IntBuffer
- LongBuffer
- FloatBuffer
- DoubleBuffer

```
public class TestBuffer {
	
	public static void main(String[] args) {
		
		// 1 基本操作
		
		//创建指定长度的缓冲区
		IntBuffer buf = IntBuffer.allocate(10);
		buf.put(13);// position位置：0 - > 1
		buf.put(21);// position位置：1 - > 2
		buf.put(35);// position位置：2 - > 3
		//把位置复位为0，也就是position位置：3 - > 0
		buf.flip();
		System.out.println("使用flip复位：" + buf);
		System.out.println("容量为: " + buf.capacity());	//容量一旦初始化后不允许改变（warp方法包裹数组除外）
		System.out.println("限制为: " + buf.limit());		//由于只装载了三个元素,所以可读取或者操作的元素为3 则limit=3
		
		
		System.out.println("获取下标为1的元素：" + buf.get(1));
		System.out.println("get(index)方法，position位置不改变：" + buf);
		buf.put(1, 4);
		System.out.println("put(index, change)方法，position位置不变：" + buf);;
		
		for (int i = 0; i < buf.limit(); i++) {
			//调用get方法会使其缓冲区位置（position）向后递增一位
			System.out.print(buf.get() + "\t");
		}
		System.out.println("buf对象遍历之后为: " + buf);
		
		
		// 2 wrap方法使用
		/**
		//  wrap方法会包裹一个数组: 一般这种用法不会先初始化缓存对象的长度，因为没有意义，最后还会被wrap所包裹的数组覆盖掉。 
		//  并且wrap方法修改缓冲区对象的时候，数组本身也会跟着发生变化。                     
		int[] arr = new int[]{1,2,5};
		IntBuffer buf1 = IntBuffer.wrap(arr);
		System.out.println(buf1);
		
		IntBuffer buf2 = IntBuffer.wrap(arr, 0 , 2);
		//这样使用表示容量为数组arr的长度，但是可操作的元素只有实际进入缓存区的元素长度
		System.out.println(buf2);
		*/
		
		
		// 3 其他方法
		/**
		IntBuffer buf1 = IntBuffer.allocate(10);
		int[] arr = new int[]{1,2,5};
		buf1.put(arr);
		System.out.println(buf1);
		//一种复制方法
		IntBuffer buf3 = buf1.duplicate();
		System.out.println(buf3);
		
		//设置buf1的位置属性
		//buf1.position(0);
		buf1.flip();
		System.out.println(buf1);
		
		System.out.println("可读数据为：" + buf1.remaining());
		
		int[] arr2 = new int[buf1.remaining()];
		//将缓冲区数据放入arr2数组中去
		buf1.get(arr2);
		for(int i : arr2){
			System.out.print(Integer.toString(i) + ",");
		}
		*/
		
	}
}
1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162636465666768697071
```

### Channel

通道（Channel），他就像自来水管道一样，网络数据通过Channel读取和写入，通道与流不同之处在于通道是双向的，而流只是一个方向上流动（一个流必须是InputStream或者OutputStream的子类），而通道可以用于读、写或者二者同时进行，最关键的是可以与多路复用器结合起来，有多种状态为，方便多路复用器去识别。事实上通道分为两大类，一类是网络读写的（SelectableChannel），一类是用于文件操作的（FileChannel），我们使用的SocketChannel和ServerSocketChannel都是SelectableChannel的子类。

### Selector

多路复用器（Selector），它是NIO编程的基础，非常重要。多路复用器提供选择已经就绪的任务的能力。简单说，就是Selector会不断地轮询注册在其上的通道（Channel）,如果某个通道发生了读写操作，这个通道就出于就绪状态，会被Selector轮询出来，然后通过SelectionKey可以取得就绪的Channel集合，从而进行后续的IO操作。一个多路复用器（Selector）可以辅助成千上万的Channel通道，没有上限，这也是JDK使用了epoll代替了传统的select实现，获得连接句柄没有限制。这也就意味着我们只要一个线程负责Selector的轮询，就可以接入成千上万个客户端，这是JDK NIO库的巨大进步。
Selector线程就类似一个管理者（Master），管理了成千上万个管道，然后轮询哪个管道的数据已经准备好，通知cpu执行IO的读取或写入操作。Selector模式：当IO事件（管道）注册到选择器以后，Selector会分配个每个管道一个key值，相当于标签。Selector选择器是以轮询的方式进行查找注册所有IO事件（管道），当我们的IO事件（管道）准备就绪后，Selector就会识别，会通过key值来找到对应的管道，进行相关的数据处理操作（从管道里读或写数据，写到我们的数据缓冲区中）。
每个管道都会对选择器进行注册不同的时间状态，以便于选择器查找：

- SelectionKey.OP_CONNECT
- SelectionKey.OP_ACCEPT
- SelectionKey.OP_READ
- SelectionKey.OP_WRITE

```
/**
 * 实现Runnable接口是为了注册到Selector中一直处于轮询的状态
 * */
public class Server implements Runnable {

	//1.多路复用器（管理所有的通道）
	private Selector selector;
	//2.建立缓冲区
	private ByteBuffer readBuf = ByteBuffer.allocate(1024);
	
	public Server(int port){
		
		try {
			//1.开启多路复用器
			this.selector = Selector.open();
			//2.打开服务端通道
			ServerSocketChannel ssc = ServerSocketChannel.open();
			//3.设置服务端通道为非阻塞模式
			ssc.configureBlocking(false);
			//4.绑定地址
			ssc.bind(new InetSocketAddress(port));
			//5.把服务端通道注册到多路复用器上，并且监听阻塞事件
			ssc.register(selector, SelectionKey.OP_ACCEPT);
			
			System.out.println("Server start, prot: " + port);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	
	@Override
	public void run() {
		while(true){
			try {
				//1.让多路复用器开始监听
				this.selector.select();
				//2.返回多路复用器已经选择的结果集
				Iterator<SelectionKey> keys = this.selector.selectedKeys().iterator();
				//3.进行响应
				while(keys.hasNext()){
					//4.获取一个选择元素
					SelectionKey key = keys.next();
					//5.直接从容器中移除
					keys.remove();
					//6.如果key有效
					if(key.isValid()){
						//7.如果为阻塞状态
						if(key.isAcceptable()){
							this.accept(key);//这里的key就是服务器端的Channel的key
						}
						//8.如果为可读状态
						if(key.isReadable()){
							this.read(key);
						}
					}
				}
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}

	private void read(SelectionKey key) {
		try {
			//1.清空缓冲区旧的数据
			this.readBuf.clear();
			//2.获取之前注册的socket通道对象
			SocketChannel sc = (SocketChannel) key.channel();
			//3.读取数据
			int count = sc.read(this.readBuf);
			//4.如果没有数据
			if(count == -1){
				key.channel().close();
				key.cancel();
				return;
			}
			//5.有数据则进行读取，读取之前需要进行复位方法（把position和limit进行复位）
			this.readBuf.flip();
			//6.根据缓冲区的数据长度创建相应大小的byte数组，接收缓冲区的数据
			byte[] bytes = new byte[this.readBuf.remaining()];
			//7.接收缓冲区数据
			this.readBuf.get(bytes);
			//8.打印结果
			String body = new String(bytes).trim();
			System.out.println("Server: " + body);
			//9.写回给客户端
			//...
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	
	private void accept(SelectionKey key) {
		try {
			//1.获取服务端通道
			ServerSocketChannel ssc = (ServerSocketChannel) key.channel();
			//2.执行客户端Channel的阻塞方法
			SocketChannel sc = ssc.accept();
			//3.设置阻塞模式
			sc.configureBlocking(false);
			//4.注册到多路复用器上，并设置读取标识
			sc.register(this.selector, SelectionKey.OP_READ);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	
	public static void main(String[] args) {
		new Thread(new Server(8765)).start();
	}
}
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990919293949596979899100101102103104105106107108109110111112
public class Client {

	public static void main(String[] args) {
		//创建连接地址
		InetSocketAddress address = new InetSocketAddress("127.0.0.1", 8765);
		
		//声明连接通道
		SocketChannel sc = null;
		
		//建立缓冲区
		ByteBuffer buf = ByteBuffer.allocate(1024);
		
		try {
			//打开通道
			sc = SocketChannel.open();
			//进行连接
			sc.connect(address);
			while(true){
				//定义一个字节数组，然后使用系统录入的功能
				byte[] bytes = new byte[1024];
				System.in.read(bytes);
				
				//把数据放到缓冲区
				buf.put(bytes);
				//复位
				buf.flip();
				//写出数据
				sc.write(buf);
				//清空缓冲区数据
				buf.clear();
			}
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			try {
				sc.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		
	}
}
12345678910111213141516171819202122232425262728293031323334353637383940414243
```

## AIO

AIO编程，在NIO基础之上引入了异步通信的概念，并提供了异步文件和异步套接字通道的实现，从而在真正意义上实现了异步非阻塞，之前我们学习的NIO只是非阻塞而非异步的。而AIO不需要通过多路复用器对注册的管道进行轮询操作即可实现异步读写，从而简化了NIO编程模型。也可以称之为NIO2.0，这种模式才真正的属于我们异步非阻塞模型。

- AsynchronousServerSocketChannel
- AsynchronousSocketChannel

```java
public class Server {

	//线程池
	private ExecutorService executorService;
	//线程组
	private AsynchronousChannelGroup threadGroup;
	//服务器通道
	public AsynchronousServerSocketChannel assc;
	
	public Server(int port){
		try {
			//创建一个缓存池
			executorService = Executors.newCachedThreadPool();
			//创建线程组
			threadGroup = AsynchronousChannelGroup.withCachedThreadPool(executorService, 1);
			//创建服务端通道
			assc = AsynchronousServerSocketChannel.open(threadGroup);
			//进行绑定
			assc.bind(new InetSocketAddress(port));
			
			System.out.println("Server start,port: " + port);
			//进行阻塞
			assc.accept(this, new ServerCompletionHandler());
			//阻塞不让服务器停止
			Thread.sleep(Integer.MAX_VALUE);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	public static void main(String[] args) {
		Server server = new Server(8765);
	}
}
123456789101112131415161718192021222324252627282930313233
public class ServerCompletionHandler implements CompletionHandler<AsynchronousSocketChannel, Server> {

	@Override
	public void completed(AsynchronousSocketChannel asc, Server attachment) {
		//当有下一个客户端接入的时候，直接调用Server的accept方法，这样反复执行下去，保证多个客户端都可以阻塞
		attachment.assc.accept(attachment, this);
		read(asc);
	}

	@Override
	public void failed(Throwable exc, Server attachment) {
		
	}

	private void read(final AsynchronousSocketChannel asc) {
		//读取数据
		ByteBuffer buf = ByteBuffer.allocate(1024);
		asc.read(buf, buf, new CompletionHandler<Integer, ByteBuffer>(){
			@Override
			public void completed(Integer resultSize, ByteBuffer attachment) {
				//进行读取之后，重置标识
				attachment.flip();
				//获取读取的字节数
				System.out.println("Server -> 收到客户端的数据长度为： " + resultSize);
				//获取读取的数据
				String resultData = new String(attachment.array()).trim();
				System.out.println("Server -> 收到客户端发过来的数据： " + resultData);
				String response = "服务器响应，收到客户端发过来的数据： " + resultData;
				write(asc, response);
			}
			@Override
			public void failed(Throwable exc, ByteBuffer attachments) {
				exc.printStackTrace();
			}
		});
	}
	private void write(AsynchronousSocketChannel asc, String response) {
		try {
			ByteBuffer buf = ByteBuffer.allocate(1024);
			buf.put(response.getBytes());
			buf.flip();
			asc.write(buf).get();
		} catch (InterruptedException e) {
			e.printStackTrace();
		} catch (ExecutionException e) {
			e.printStackTrace();
		}
	}
	
}
1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950
public class Client implements Runnable{

	private AsynchronousSocketChannel asc;
	
	public Client() throws IOException{
		asc = AsynchronousSocketChannel.open();
	}
	
	public void connet(){
		asc.connect(new InetSocketAddress("127.0.0.1", 8765));
	}

	public void write(String request){
		try {
			asc.write(ByteBuffer.wrap(request.getBytes())).get();
			read();
		} catch (InterruptedException e) {
			e.printStackTrace();
		} catch (ExecutionException e) {
			e.printStackTrace();
		}
	}
	
	private void read() {
		ByteBuffer buf = ByteBuffer.allocate(1024);
		try {
			asc.read(buf).get();
			buf.flip();
			byte[] respByte = new byte[buf.remaining()];
			buf.get();
			System.out.println(new String(respByte, "utf-8").trim());
		} catch (InterruptedException e) {
			e.printStackTrace();
		} catch (ExecutionException e) {
			e.printStackTrace();
		} catch (UnsupportedEncodingException e) {
			e.printStackTrace();
		}
	}

	@Override
	public void run() {
		while(true){}
	}

	public static void main(String[] args) throws IOException, InterruptedException {
		Client c1 = new Client();
		c1.connet();
		
		Client c2 = new Client();
		c2.connet();
		
		Client c3 = new Client();
		c3.connet();
		
		new Thread(c1, "c1").start();
		new Thread(c2, "c2").start();
		new Thread(c3, "c3").start();
		
		Thread.sleep(1000);
		
		c1.write("c1 request");
		c2.write("c2 request");
		c3.write("c3 request");
	}
}
/*
打印结果：

Server start,port: 8765
Server -> 收到客户端的数据长度为： 10
Server -> 收到客户端发过来的数据： c1 request
Server -> 收到客户端的数据长度为： 10
Server -> 收到客户端发过来的数据： c2 request
Server -> 收到客户端的数据长度为： 10
Server -> 收到客户端发过来的数据： c3 request
*/
```

