# JVM内存模型





## java 内存模型，



一个线程存分配一个私有的线程栈， 在线程执行完成之后，栈空间回收

xss 栈空间配置

![](D:\学习笔记\java\jvm\images\JVM内存结构.jpg)



## 堆的回收

xms 最小堆

xmx 最大堆

![](D:\学习笔记\java\jvm\images\堆收集.png)





## jvm 调优的一些参数

### Jinfo

查看在在运行的java 应用程序的扩展参数

```shell
Jinfo -flags pid
```

### Jstat

* 类加载统计`Jstat -class pid`
* 垃圾回收器统计 `Jstat -gc pid`
* 堆内存统计
* 新生代垃圾回收统计
* 新生代内存统计
* 老年代垃圾回收统计
* 元数据空间统计

## Jmap

实例个数以及占用内存大小

`jmap -histo pid > file.txt`

堆信息

`jmap -heap pid`

堆内存

`jmap -dump:format=b,file=test.hprof pid`



## jstack

线程栈信息





**jstack找出占用cpu最高的堆栈信息**

1，使用命令top -p <pid> ，显示你的java进程的内存情况，pid是你的java进程号，比如4977

2，按H，获取每个线程的内存情况 

3，找到内存和cpu占用最高的线程tid，比如4977 

4，转为十六进制得到 0x1371 ,此为线程id的十六进制表示

5，执行 jstack 4977|grep -A 10 1371，得到线程堆栈信息中1371这个线程所在行的后面10行 

6，查看对应的堆栈信息找出可能存在问题的代码