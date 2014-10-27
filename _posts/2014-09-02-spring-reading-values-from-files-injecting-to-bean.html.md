---
layout: post
title: Spring - Reading values from file and injecting to bean property using SPEL
date: 2014-08-20
draft: false
tags: [spring,java]
---

Sometimes you would need to inject value for spring bean propery by reading values from a file. Good use case for this would be injecting passwords into bean property by reading value from a file rather than hard coding password into application context xml.

This can be done by creating property file with values we want to inject and use spring util properties to read values into application context as properties. Then, this property value can be injected using [Spring SpEL](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/expressions.html).

To demonstrate this, lets pick example of initializing Jetty `SslContextFactory` with keystore file and key store password.

First, Create a property file say `keyStorePwd.properties` with values

```sh
passwd=mypass1234

```

Then, we can use following code in Spring Application Context to read that property file and inject value into `keyStorePassword` property of `org.eclipse.jetty.util.ssl.SslContextFactory` class.


```xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:util="http://www.springframework.org/schema/util"
             xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
           http://www.springframework.org/schema/util
           http://www.springframework.org/schema/util/spring-util.xsd">

    <util:properties id="keyStorePwdProperties" location="keyStorePwd.properties" />
    
    <bean id="sslContextFactory" class="org.eclipse.jetty.util.ssl.SslContextFactory">
        <property name="keyManagerPassword" value="#{keyStorePwdProperties.passwd}"/> 
        <constructor-arg value="file:keyStoreFile.jks"/>
    </bean>

</beans>

```

The Spring SpEL expression `#{keyStorePwdProperties.passwd}` takes value of `passwd` property read from `keyStorePwd.properties` and injects into spring bean property.

