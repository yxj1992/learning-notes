### jdk1.8 HotSpot的情况如下：
你可以在Linux下执行以下命令查看Xms和Xmx的默认值  
java -XX:+PrintFlagsFinal -version | grep HeapSize  

另外这是Java8的文档中关于Default Heap Size的描述：[点击这里](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/parallel.html#default_heap_size)  

hotspot虚拟机的默认堆大小如果未指定，他们是根据服务器物理内存计算而来的  

client模式下，JVM初始和最大堆大小为：  
在物理内存达到192MB之前，JVM最大堆大小为物理内存的一半，否则，在物理内存大于192MB，在到达1GB之前，JVM最大堆大小为物理内存的1/4，大于1GB的物理内存也按1GB计算，举个例子，如果你的电脑内存是128MB，那么最大堆大小就是64MB，如果你的物理内存大于或等于1GB，那么最大堆大小为256MB。  
Java初始堆大小是物理内存的1/64，但最小是8MB。  

server模式下：  
与client模式类似，区别就是默认值可以更大，比如在32位JVM下，如果物理内存在4G或更高，最大堆大小可以提升至1GB，，如果是在64位JVM下，如果物理内存在128GB或更高，最大堆大小可以提升至32GB。  

#### Server和Client模式的区别  
JVM Server模式与client模式启动，最主要的差别在于：-Server模式启动时，速度较慢，但是一旦运行起来后，性能将会有很大的提升.原因是:  

当虚拟机运行在-client模式的时候,使用的是一个代号为C1的轻量级编译器, 而-server模式启动的虚拟机采用相对重量级,代号为C2的编译器. C2比C1编译器编译的相对彻底,,服务起来之后,性能更高.  

java -version 可以直接查看出你使用的是client还是 server  