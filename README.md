# gcloud-logging json log appender for sl4j + logback
[![Build Status](https://travis-ci.org/ankurcha/gcloud-logging-slf4j-logback.svg?branch=master)](https://travis-ci.org/ankurcha/gcloud-logging-slf4j-logback)
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.github.ankurcha/google-cloud-logging-logback-slf4j/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.github.ankurcha/google-cloud-logging-logback-slf4j)


This library provides a simple json based layout that can be used to send structured logs to gcloud logging.

This appender is particularly useful in [Google Container Engine](https://cloud.google.com/container-engine/)
where logs should be emitted to stdout.

## Is this production ready?

Yes.

## Usage

Add dependency to `build.gradle`:

```groovy
dependencies {
    // refer to badge for latest version
    compile 'com.github.ankurcha:gcloud-logging-slf4j-logback:LATEST'
}
```

or to maven `pom.xml`:

```xml
<dependency>
    <groupId>com.github.ankurcha</groupId>
	<artifactId>gcloud-logging-slf4j-logback</artifactId>
	<version>LATEST</version>
</dependency>
```

Add entry to `logback.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <appender name="KUBE_CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <layout class="com.google.cloud.logging.GoogleCloudLoggingV2Layout">
                <appendLineSeparator>true</appendLineSeparator>
                <serviceName>@service-name@</serviceName>
                <serviceVersion>@git.sha@</serviceVersion>
                <jsonFormatter class="com.google.cloud.logging.GSONJsonFormatter"/>
            </layout>
        </encoder>
    </appender>

    <root level="info">
        <appender-ref ref="KUBE_CONSOLE"/>
    </root>

</configuration>

```

Example log line (prettified here):

```json
{
   "severity":"INFO",
   "context":{
      "reportLocation":{
         "functionName":"org.springframework.web.servlet.FrameworkServlet.initServletBean",
         "filePath":"org/springframework/web/servlet/FrameworkServlet.class",
         "logger":"org.springframework.web.servlet.DispatcherServlet",
         "thread":"http-nio-8080-exec-1",
         "lineNumber":508
      }
   },
   "serviceContext":{
      "version":"dc23b8d342e95b48a80219a2af67388b1c1a52d0",
      "service":"ferric"
   },
   "message":"FrameworkServlet \u0027dispatcherServlet\u0027: initialization completed in 27 ms",
   "timestamp":{
      "seconds":1478201184,
      "nanos":753000000
   }
}
```

Directly reporting to the stackdriver API

```groovy
appender("CLOUD", com.google.cloud.logging.logback.JsonLoggingAppender) {
    flushLevel = WARN
}

root(loggerLevel, ['CLOUD'])
```

## TODO

* [x] Update in readme
* [ ] Add some tests for the formatter
