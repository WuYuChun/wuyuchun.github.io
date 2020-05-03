---
layout: post
title:  "log4Cpp学习（三）—ConsoleAppender类"
date:   2015-04-14 10:23 PM
excerpt:
tag:
- windows
---

listi

[10:23 PM]

# 前言
学习别人的代码也是一种提高。

# 摘要
在使用log4Cpp的过程中，Appender是一个非常重要的类，因为**其解决把日记输出到哪里的问题**，输出的地方当然很多了（也不算很多），例如控制台、文件等。而ConsoleAppender就是一个把log输出到控制台的类。见下图（比较粗糙）。
![这里写图片描述](http://img.blog.csdn.net/20150414222247535)

# ConsoleAppender类
ConsoleAppender是把日记信息输出到控制台，是向std::cout或者std::cerr。

借用前面的例子，来讲解ConsoleAppender的内部实现

```
    //第一步
    SharedObjectPtr<Appender> _append(new ConsoleAppender());
    _append->setName("append for test");
     
    //第二步
    std::string pattern = "";
    std::auto_ptr<Layout> _layout(new PatternLayout(pattern));

     //第三步
    _append->setLayout(_layout);

     //第四步
    Logger _logger = Logger::getInstance("test");

     //第五步
    _logger.addAppender(_append);

    _logger.setLogLevel(ALL_LOG_LEVEL);

      //第六步
    LOG4CPLUS_DEBUG(_logger,"This is the First Log Message...");
    
    Sleep(1);

    LOG4CPLUS_DEBUG(_logger,"This is the Second Log Message...");

    getchar();
```
声明一个SharedObjectPtr的Appender指针，可以看上面图中的继承关系。
看其的构造函数（含义非常清楚）：

```
ConsoleAppender(bool logToStdErr = false, 
                bool immediateFlush = false);
ConsoleAppender::ConsoleAppender(bool logToStdErr_,
    bool immediateFlush_)
: logToStdErr(logToStdErr_),
  immediateFlush(immediateFlush_)
{
}               
```

它的数据成员：

```
        bool logToStdErr; //用于控制想哪个输出流输出
        bool immediateFlush; //用于控制是否每一次输出都刷新
```
每一个Appender都有自己名字，用于其他组件识别是那个Appender，
看看SetName的函数定义，其实SetName在Appender中定义为一个虚函数。

```
 /**
 * Set the name of this appender. The name is used by other
 * components to identify this appender.
 */
  virtual void setName(const log4cplus::tstring& name);

void
Appender::setName(const log4cplus::tstring& n)
{
    this->name = n; //name 为Appender的一个属性
}
```

第二步涉及布局类，等以后再学习这个类。
看第三步

```
/**
* Set the layout for this appender. Note that some *appenders have
* their own (fixed) layouts or do not use one. For example, *the SocketAppender ignores the layout set here.
*/
 virtual void setLayout(std::auto_ptr<Layout> layout);
 void
Appender::setLayout(std::auto_ptr<Layout> lo)
{
    thread::MutexGuard guard (access_mutex); //防止多线程
    this->layout = lo;
}
```
看看第五步，至于Logger类，以后再学习

```
// AppenderAttachable Methods
 virtual void addAppender(SharedAppenderPtr newAppender);
 void 
Logger::addAppender (SharedAppenderPtr newAppender)
{
    value->addAppender(newAppender);
}
```
看看宏LOG4CPLUS_DEBUG，其的定义如下：

```
#define LOG4CPLUS_DEBUG(logger, logEvent)                               \
    LOG4CPLUS_MACRO_BODY (logger, logEvent, DEBUG_LOG_LEVEL)

#define LOG4CPLUS_MACRO_BODY(logger, logEvent, logLevel)                \
    LOG4CPLUS_SUPPRESS_DOWHILE_WARNING()                                \
    do {                                                                \
        log4cplus::Logger const & _l                                    \
            = log4cplus::detail::macros_get_logger (logger);            \
        if (LOG4CPLUS_MACRO_LOGLEVEL_PRED (                             \
                _l.isEnabledFor (log4cplus::logLevel), logLevel)) {     \
            LOG4CPLUS_MACRO_INSTANTIATE_OSTRINGSTREAM (_log4cplus_buf); \
            _log4cplus_buf << logEvent;                                 \
            log4cplus::detail::macro_forced_log (_l,                    \
                log4cplus::logLevel, _log4cplus_buf.str(),              \
                __FILE__, __LINE__, LOG4CPLUS_MACRO_FUNCTION ());       \
        }                                                               \
    } while (0)                                                         \
    LOG4CPLUS_RESTORE_DOWHILE_WARNING()

```

其实关键是下面的语句

```
 _log4cplus_buf << logEvent;                                 \
            log4cplus::detail::macro_forced_log (_l,                    \
                log4cplus::logLevel, _log4cplus_buf.str(),              \
                __FILE__, __LINE__, LOG4CPLUS_MACRO_FUNCTION ());  
```
**若是自己觉得log4Cpp很大的话，其实可以根据这个的仿写自己的一个简单的日记宏**
