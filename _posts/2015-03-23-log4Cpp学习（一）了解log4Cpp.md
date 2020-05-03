---
layout: post
title:  "log4Cpp学习（一）了解log4Cpp"
date:   2015-03-23 10:57 PM
excerpt:
tag:
- log4cpp
---

{% include toc.html %}

[10:57 PM]

# 前言
在项目中需要用到日记系统，上网查了一下，感觉log4cpp比较好，就学习一下，顺便看看其的代码架构，提高自己。

# 介绍
log4cplus 是 C++编写的开源日志系统， 前身是 java 编写的 log4j 日志系统。 log4cplus 具有**线程安全、灵活、以及多粒度控制**的特点，通过将信息划分优先级使其可以面向程序调试、运行、测试、和维护等全生命周期； 可以选择将信息输出到屏幕、文件、NT event log、甚至是远程服务器；通过指定策略对日志进行定期备份等等。

> 下载地址：http://log4cplus.sourceforge.net，下载log4cplus-x.x.x，我下载是log4cplus-1.2.0-rc3。

# 安装
本人主要是在Windows+VS2010上开发的，所以主要说该平台下的安装，若是Linux安装，可以参考log4cpp帮助手册。
*解压你下载的文件，打开文件夹，可以看到如下的文件，（注意msvc10文件夹，需要用VS2010编译出动态库）。
![这里写图片描述](http://img.blog.csdn.net/20150323221115468)
*打开msvc10文件夹，并用VS2010打开其的解决方案。
![这里写图片描述](http://img.blog.csdn.net/20150323221652086)
*注意，其中含有log4cplus(用于动态链接）、log4cpluS（用于静态链接）、loggingserver(用于服务器)。
编译！！！
*把生成dll以及头文件加入VS目录中。
![这里写图片描述](http://img.blog.csdn.net/20150323222838440)
这里建立一个文件夹，放置在此，然后工程中增加相应的搜索路径。
![这里写图片描述](http://img.blog.csdn.net/20150323223043614)

# 运行一个小例子

```
#include <log4cplus/logger.h>
#include <log4cplus/fileappender.h>
#include <log4cplus/loggingmacros.h>

using namespace log4cplus;

int main()
{
	/* step 1: Instantiate an appender object */
	SharedAppenderPtr _append(new FileAppender("Test.log"));
	_append->setName("file log test");

	/* step 4: Instantiate a logger object */
	Logger _logger = Logger::getInstance("test.subtestof_filelog");

	/* step 5: Attach the appender object to the logger  */
	_logger.addAppender(_append);

	/* log activity */
	int i;
	for( i = 0; i < 5; ++i )
	{
		LOG4CPLUS_DEBUG(_logger, "Entering loop #" << i << "End line #");
	}
	return 0;
}
```
运行结果：
![这里写图片描述](http://img.blog.csdn.net/20150324210926565)
DEBUG - Entering loop #0End line #
DEBUG - Entering loop #1End line #
DEBUG - Entering loop #2End line #
DEBUG - Entering loop #3End line #
DEBUG - Entering loop #4End line #

# 结论
其实log4cpp还是很好用的嘛~~

