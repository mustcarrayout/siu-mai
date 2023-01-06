- ## 参数配置
- ```shell
  -Xms #最小堆（初始堆大小）
  -Xmx #最大堆（上限，超过直接OOM）
  -Xss # 指定每个线程的堆大小；
  # -Xms，默认是物理内存的1/64；
  # -Xmx，默认是物理内存的1/4；
  
  # 下面两个结合启来就是，OOM的时候导出hprof并且指定存方的位置
  -XX:+HeapDumpOnOutOfMemoryError
  -XX:HeapDumpPath=
  # 打印gc详细信息
  -XX:+PrintGCDetails
  ```
- ## 配置gc日志输出
	- ```shell
	  -XX:+PrintGC 输出GC日志
	  -XX:+PrintGCDetails 输出GC的详细日志
	  -XX:+PrintGCTimeStamps 输出GC的时间戳（以基准时间的形式）
	  -XX:+PrintGCDateStamps 输出GC的时间戳（以日期的形式，如 2013-05-04T21:53:59.234+0800）
	  -XX:+PrintHeapAtGC 在进行GC的前后打印出堆的信息
	  -Xloggc:../logs/gc.log 日志文件的输出路径
	  
	  # 实时查看gc情况 https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jstat.html
	  jstat -gcutil 1 1000
	  
	  S0: 新生代中 Survivor space 0 区已使用空间的百分比
	  S1: 新生代中 Survivor space 1 区已使用空间的百分比
	  E: 新生代已使用空间的百分比
	  O: 老年代已使用空间的百分比
	  M: 元空间已使用空间的百分比
	  CCS: Compressed class space utilization as a percentage
	  YGC: 从应用程序启动到当前，发生 Yang GC 的次数
	  YGCT: 从应用程序启动到当前，Yang GC 所用的时间【单位秒】
	  FGC: 从应用程序启动到当前，发生 Full GC 的次数
	  FGCT: 从应用程序启动到当前，Full GC 所用的时间
	  GCT: 从应用程序启动到当前，用于垃圾回收的总时间【单位秒】
	  
	  ```
-
-
- ## 参考
	- [Elasticsearch GC入门讲解](https://segmentfault.com/a/1190000016494488)
	- [jstat使用文档](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jstat.html)
-
-