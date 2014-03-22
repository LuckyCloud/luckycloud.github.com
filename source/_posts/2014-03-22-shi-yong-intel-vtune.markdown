---
layout: post
title: "使用intel vtune"
date: 2014-03-22 21:47:28 +0800
comments: true
categories: 
---
   最近我们的多核并行化引擎终于开发完成，性能也跟预期的有一定的线性度。
   但是性能我们觉得还是有优化的地方，加上最近在看一些cache，memory方面的东西。
   所以我们想看看我们程序的瓶颈点到底在哪地方，所以就找了一些profile工具，此前用过google 
   performance tools,oprofile 前几天看一篇文章，推荐了intel vtune 
   
    Performance profiler for serial and parallel performance analysis.

	Collect a rich set of data to tune CPU & GPU compute performance, multi-core scalability, bandwidth and more
	Sort, filter and visualize results for quick insight into performance bottlenecks
	Automate regression tests and collect data remotely using the powerful command line
   现在还在intel vtune 初步使用阶段，记录下使用的过程。
   intel vtune 的 profile 分为两种，一种是基于 application 的，一种是基于system wide 的 
   如果是多线程的程序，采用基于application的 方法比较好，能够比较好的分析一个process的
   各个线程种的各种性能数据，但是我们的程序是多进程的，目前还没发现intel vtune 能在application
   模式下对多个进程进行分析。今天试了 让他跟踪fork 后的子进程，发现还是有一些问题。还没有去细看。
   下面对 过程 进行简单记录。

   鉴于我们的设备里面的工具链不是 很全（几乎没有），所以我采用了在 target system 上分析结果，然后
   在我的pc 上 使用 windows 版本的进行 数据分析和查看。
   安装：
       在和设备有同样基础库，同样内核等的开发机器上，将下载的linux 版本安装，安装过程比较简单
	   解压后的安装包里面直接执行 ./install.sh 
	   后面按照提示进行即可。
	   但是这个安装后并没有安装支持hard event based 的 驱动，和内核，
	   需要先重新编译内核：
	      make menuconfig
		  vi ./.config  将 vsttp 置为 y 
      然后进到 安装包 的 sepdk 
	    编译内核模块
       
      最终，将编译好的内核，安装后在/opt/intel  下的所有东西拷贝到设备上。

      然后进到 安装包 的 sepdk 
	        ./insmodsep -g root 
		   这里可能会提示有些 模块加载 失败，直接找到 这些模块的 ko ，加载进去即可。

      另外会提示license的问题，将环境变量设置即可。 INTELXXX ＝ xxx/xxx.lic
      至此，设备里面的安装告一段落。
	  在bin 64目录下执行ampxle-cl 相关的命令，最后将数据取到 windows 下分析即可。

	  后面会对此文继续进行扩充。 

   [intel optimization manual](http://www.intel.com/content/dam/www/public/us/en/documents/manuals/64-ia-32-architectures-optimization-manual.pdf)
   [using intel amplifier on xeon e5 family](http://software.intel.com/sites/default/files/article/380498/using-intel-vtune-amplifier-xe-on-xeon-e5-family-1.0.pdf)




