---
layout: post
title: Log4j - Separating log lines to multiple log files.
date: 2014-08-27
draft: false
tags: [log4j,java, tutorial]
---

It's generally good practise to partition the log lines of your java application into different log files based on functionality/module etc. 

For eg. In a web application, we would want application log lines to be logged in to `server.log`,  whereas information about remote user who made the requests to be logged into `request.log`.

This can be done by defining two appenders in log4j properties and configuring them to output to two different log files. 

Here is the example log4j properties.

```ruby
log4j.rootLogger=INFO,fileAppender

log4j.logger.requestLogger=INFO, requestAppender

##Don't propogate things logged to requestLogger to other appenders
log4j.additivity.requestLogger=false

log4j.appender.fileAppender=org.apache.log4j.RollingFileAppender
log4j.appender.fileAppender.MaxFileSize=500MB
log4j.appender.fileAppender.MaxBackupIndex=10
log4j.appender.fileAppender.Append=true
log4j.appender.fileAppender.File=server.log
log4j.appender.fileAppender.layout=org.apache.log4j.PatternLayout
log4j.appender.fileAppender.layout.ConversionPattern=%d [%t] %-5p (%c %M:%L) %x - %m%n

log4j.appender.requestAppender=org.apache.log4j.RollingFileAppender
log4j.appender.requestAppender.MaxFileSize=1024MB
log4j.appender.requestAppender.MaxBackupIndex=10
log4j.appender.requestAppender.Append=true
log4j.appender.requestAppender.File=requests.log
log4j.appender.requestAppender.layout=org.apache.log4j.PatternLayout
log4j.appender.requestAppender.layout.ConversionPattern=%d [%t] %-5p (%C %M:%L) %x - %m%n
```

We have defined two appenders, ***fileAppender*** to output to `server.log` and ***requestAppender*** to output to `requests.log`. 
Only ***fileAppender*** is added to ***rootLogger***, so any `Logger` instances created by passing java class will be logged to `server.log`.

Example code snippets showing how to log to `server.log`.

```java
public class RequestProcessor {

    private static final Logger logger = Logger.getLogger(RequestProcessor.class);
    
    private void handleRequest(HttpServletRequest request){
        //This message will be logged to server.log
        logger.info("Processing Request");
    }
}
```

To log to `requests.log` the `Logger` instance should be created passing `requestLogger` as logger name.

Example code snippets showing how to log to `request.log`.

```java
public class RequestInterceptor {

    private static final Logger logger = Logger.getLogger("requestLogger"); //Should match name of logger 
    
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //This will be logged to requests.log
        logger.info("Received request from user: " + request.getRemoteUser());
        return true;
    }
}
```


 
