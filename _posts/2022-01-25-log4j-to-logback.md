---
title: "Log4j to Logback migration? Good idea or not?"
categories:
  - Posts
header:
  overlay_image: /assets/images/emotions-g8a980472b_1920.jpg
  caption: "Photo credit: [**Pixabay**](https://pixabay.com/)"
tags:
    - log4j
    - logger
    - logback
    - java
    - logging
    - ddos
    - vulnerabilities
classes: wide
author_profile: true
excerpt: ""
---

## New Buzz word Log4j

By end of 2021, there came a new buzz word "LOG4J". People who don't know what log4j is started to talk about it. Jokes a part, log4j teared the world apart because of the security vulnerability  that were exploited by Hackers. This security vulnerability was allowing attackers to execute malicious code remotely on a target computer. Which means hackers can easily steal data, plant malware, or take control of the target computer via the Internet.

## How to overcome this issue
### Solution 1

Update the library usage to the latest released version of log4j, where Apache team has fixed the "known" vulnerabilities.

### Solution 2

Switch to different logger e.g. Logback 

## What is Logback?
Logback is a logging framework for mostly Java based applications, and a successor to the popular log4j project. Logback has many improvements over log4j. Just for information, logback is very much like log4j as both the projects were founded by the same developers. Logback is very similar to log4j when it comes to usage.

### Why Logback?

- Its very much like log4j, no extra knowledge need to use.
- As its very much similar to log4j, its easy to replace.
- Logback uses slf4j [natively](https://logback.qos.ch/reasonsToSwitch.html).
- Auto compression of log files.
- Many more.. Have a look at: [Reasons to switch](https://logback.qos.ch/reasonsToSwitch.html).

## How to use Logback?

- Add the logback dependencies. 

```
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>${slf4j-version}</version>
</dependency>

<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-core</artifactId>
    <version>${logback-version}</version>
</dependency>

<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>${logback-version}</version>
</dependency>
```

If JAR files are needed locally then download them from logback [download](https://logback.qos.ch/download.html) page.

***If the application is based on Spring boot then, no additional dependencies are required as Spring boot provides log back support.***

- 
Add `logback.xml` file (logback-spring.xml in case of Spring boot) in `src\main\resources`. 
Sample `logback.xml` For more information about Logback configuration, check [Link](https://logback.qos.ch/manual/configuration.html).

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <Pattern>
                %date{"yyyy-MM-dd'T'HH:mm:ss,SSSXXX", UTC} - %yellow([tid:%t])[sid:%X{httpSessionId}][reqid:%X{reqId}] - %green(%level) %cyan([%c]) - %m%n
            </Pattern>
        </layout>
    </appender>

    <appender name="appServerRollingFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>applogs/shpi-api.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
            <fileNamePattern>applogs/$${date:yyyy-MMM}/shpi-api-%d{yyyy-MMM-dd}-%i.log.gz</fileNamePattern>
        </rollingPolicy>

        <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
            <maxFileSize>200MB</maxFileSize>
        </triggeringPolicy>
        <encoder>
            <pattern>%date{"yyyy-MM-dd'T'HH:mm:ss,SSSXXX", UTC} - [sid:%X{httpSessionId}][actor:%X{userId}][reqid:%X{reqId}] - %p [%c] - %m%n</pattern>
        </encoder>
    </appender>

    <root level="info">
        <appender-ref ref="STDOUT"/>
        <appender-ref ref="appServerRollingFile"/>
    </root>

</configuration>

```
- 
How to use LoggerFactory instance.
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
...
static final Logger LOG = LoggerFactory.getLogger(ClassName.class);
...
{
  LOG.warn("Warn Test");
}
```

-
If migrating from Log4j to Logback use this translator tool from logback developers [Translator](https://logback.qos.ch/translator/).

## Conclusion

Idea behind use of logback is the recent issues with log4j which gave everyone a reality check, that now there is definite need of log4j alternative.
May be now is the time to migrate!

## References

- [Spring boot and Logback](https://www.baeldung.com/spring-boot-logging)
- [Logback Project](https://logback.qos.ch/)
- [Apache Log4j](https://logging.apache.org/log4j/2.x/)


