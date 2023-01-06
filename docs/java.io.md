title:: java.io

- 字符流与字节流转换
	- **编码**
- 字节流(Stream)
	- InputStream
		- ```
		  ByteArrayInputStream
		  BufferedInputStream
		  ObjectInputStream
		  FileInputStream
		  JarInputStream
		  ```
	- OutputStream
		- ```
		  FileOutputStream
		  ObjectOutputStream
		  ```
- 字符流(Reader/Writer)
	- Reader
		- ```
		  InputStreamReader
		  FileReader
		  BufferedReader
		  ```
	- Writer
		- ```
		  OutputStreamWriter
		  FileWriter
		  StringWriter
		  PrintWriter
		  ```
- io模式
	- Acceptor(经典模式)
		- 即每当一个client线程访问，服务器创建一个线程来回答。
	- Reactor(响应模式)
		- 单线程
		- 多线程
		- 单进程
		- 多进程
- io上下文切换
	- ```
	  read：磁盘->页缓存（内核空间）->用户空间  用户态-内核态-用户态
	  write：用户空间->Socket缓存(内核空间)->IO 用户态-内核态-用户态
	  传统的拷贝次数：4次
	  第一次：read ：DMA将数据从磁盘到内核缓冲区。
	  第二次：内核缓冲区的数据拷贝到用户缓冲区
	  第三次：write：用户缓冲区到 Socket 缓冲区
	  第四次： Socket 缓冲区到网络协议引擎
	  上下文切换次数：4次
	  read两次，write两次
	  
	  - mmap 优化：内存映射，将文件映射到内核缓冲区，用户空间可以共享内核空间的数据
	  引入了mmap
	  mmap：内存映射文件。将用户空间(文件)映射到内核空间中（具体来说是页缓存中）。在实现上也是可行的，因为从虚拟地址上0-3G是用户，3-4G是内核，只要有个映射就好。
	  所以mmap+write代替传统的是read+write方法，少了一次CPU拷贝
	  mmap+write：
	  mmap：磁盘->页缓存  用户态-内核态-用户态
	  write：页缓存 ->Socket缓存->IO 用户态-内核态-用户态
	  
	  mmp=页缓存 -> 映射到用户态,给地址你直接用.
	  
	  
	  sendFile：数据根本不经过用户态，直接从内核缓冲区进入到 Socket Buffer
	  拷贝次数：3次 同上（都是在第一次内核态中完成）
	  上下文切换次数：2次
	  SendFile代替mmap+write，减少一次用户内核切换
	  磁盘->页缓存-> Socket缓存->IO  用户态-内核态-用户态
	  
	  JVM可以使用的内存分外2种：堆内存和堆外内存(直接内存)
	  代码中能直接操作本地内存的方式有2种：使用未公开的Unsafe和NIO包下ByteBuffer。
	  Unsafe申请的内存不受GC控制(所以可能导致OOM)
	  Direct Memory是受GC控制的，例如ByteBuffer bb = ByteBuffer.allocateDirect(1024)
	  JDK中使用DirectByteBuffer对象来表示堆外内存，每个DirectByteBuffer对象在初始化时，都会创建一个对应的Cleaner对象，用于保存堆外内存的元信息（开始地址、大小和容量等）
	  主动回收：只要从DirectByteBuffer里取出那个sun.misc.Cleaner，然后调用它的clean()就行； 
	  GC回收：GC回收DirectByteBuffer时，会调用.Cleaner的clean()
	  
	  堆外内存是核心态，堆内内存是用户态
	  正常存储到磁盘或者从磁盘到堆时：
	  磁盘——内核态内存——堆内内存(Naive堆(本地方法堆)，因为是Naive函数控制读写流)
	  原因是操作系统把内存中的数据写入磁盘或网络时，要求数据所在的内存区域不能变动，但是JVM的GC机制会对内存进行整理，导致数据内存地址发生变化，所以无奈，JDK只能先拷贝到堆外内存（不受GC影响），然后把这个地址发给操作系统。==磁盘读取较慢==
	  
	  
	  非直接缓冲区(堆内缓冲区)和直接缓冲区别（堆外缓冲区）：
	  非直接：四次拷贝
	  直接：相当于mmap，三次拷贝
	  使用Native函数库直接分配堆外内存,然后通过一个存储在Java堆中的DirectByteBuffer对象作为这块内存的引用进行操作，在垃圾回收时并不能用程序区控制堆外内存的回收，因为不属于虚拟机，只能是垃圾回收机制按需对堆内的DirectByteBuffer对象进行回收，回收后将与直接缓存区失去联系，也就意味着直接缓冲区被回收。
	  
	  ```