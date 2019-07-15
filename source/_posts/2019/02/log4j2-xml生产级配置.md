---
title: log4j2.xml生产级配置
date: 2019-02-28 13:59:25
categories: java
tags: 配置
---
log4j2依赖配置和xml配置文件配置记录
<!-- more -->
## log4j2依赖
```
<!--slf4j + log4j2-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-slf4j-impl</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-web</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-api</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
        </dependency>
        <!--exclude spring other log framework-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>*</groupId>
                    <artifactId>*</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```

### log4j2.xml配置（含详细备注）
```
<?xml version="1.0" encoding="UTF-8"?>
<!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
<!--Configuration后面的status,这个用于设置log4j2自身内部的信息输出,可以不设置,当设置成trace时,你会看到log4j2内部各种详细输出-->
<!--monitorInterval：Log4j能够自动检测修改配置 文件和重新配置本身,设置间隔秒数-->
<!--此份配置文件默认最多保存7天的日志， 每种级别的日志文件最多10个，每个文件最大50MB-->
<!--文件名按日志级别-日期（精确到日）-下标，一天产生的日志最多50MB*10=500MB,7天最大500MB*7=3500MB,所以需要保证磁盘至少有3500MB的空间-->
<configuration status="WARN" monitorInterval="1800">
    <Properties>
        <!-- 日志默认存放的位置,这里设置为项目根路径下,也可指定绝对路径 -->
        <!-- ${front:rootDir}是web项目根路径,java项目没有这个变量,需要删掉,否则会报异常 -->
        <!--<property name="appName">redis</property>-->
        <!--<property name="basePath">${sys:user.home}/logs/${appName}</property>-->
        <property name="basePath">logs</property>

        <!-- 控制台显示的日志最低级别 -->
        <property name="console_print_level">INFO</property>
        <!-- 日志默认输出级别 -->
        <property name="output_log_level">INFO</property>
        <!-- 日志默认切割的最小单位 -->
        <property name="every_file_size">50MB</property>
        <!--最多保存几天内的文件，用delete标签实现，保存7天的文件-->
        <property name="max_days">8d</property>
        <!--文件最大下标-->
        <property name="rolling_max">10</property>

        <!-- 控制台默认输出格式,"%-5level":日志级别,"%l":输出完整的错误位置,是小写的L,因为有行号显示,所以影响日志输出的性能 -->
        <property name="console_log_pattern">%d{yyyy-MM-dd HH:mm:ss} %highlight{[%-5level]} %l - %m%n</property>
        <!-- 日志文件默认输出格式,不带行号输出(行号显示会影响日志输出性能);%C:大写,类名;%M:方法名;%m:错误信息;%n:换行 -->
        <property name="file_log_pattern">%d{yyyy-MM-dd HH:mm:ss} [%-5level] %C.%M - %m%n</property>
    </Properties>

    <!--定义appender -->
    <appenders>
        <!-- 用来定义输出到控制台的配置 -->
        <Console name="Console" target="SYSTEM_OUT">
            <!-- 设置控制台只输出level及以上级别的信息(onMatch),其他的直接拒绝(onMismatch)-->
            <ThresholdFilter level="${console_print_level}" onMatch="ACCEPT" onMismatch="DENY"/>
            <!-- 设置输出格式,不设置默认为:%m%n -->
            <PatternLayout pattern="${console_log_pattern}"/>
        </Console>

        <!-- 打印root中指定的level级别以上的日志到文件 -->
        <RollingFile name="RollingFile" fileName="${basePath}/app.log" filePattern="${basePath}/app/app-%d{yyyy-MM-dd}-%i.log">
            <PatternLayout pattern="${file_log_pattern}"/>
            <Policies>
                <!--每天产生一个文件, interval 的单位取决于pattern精确到的最小时间-->
                <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
                <!--如果文件大于了file_size也会直接产生一个文件-->
                <SizeBasedTriggeringPolicy size="${every_file_size}"/>
            </Policies>
            <!--下标i最大max,文件超过age时间就会被删除,注意age间隔最好>2d, 不然可能会导致删除的时候日志文件还在被占用-->
            <DefaultRolloverStrategy max="${rolling_max}">
                <Delete basePath="${basePath}/app" maxDepth="2">
                    <IfFileName glob="*.log"/>
                    <IfLastModified age="${max_days}"/>
                </Delete>
            </DefaultRolloverStrategy>
            <!-- 匹配INFO以及以上级别 -->
            <Filters>
                <ThresholdFilter level="INFO" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
        </RollingFile>

        <!-- 打印INFO级别的日志到文件 -->
        <RollingFile name="InfoFile" fileName="${basePath}/info.log" filePattern="${basePath}/info/info-%d{yyyy-MM-dd}-%i.log">
            <PatternLayout pattern="${file_log_pattern}"/>
            <Policies>
                <!--每天产生一个文件, interval 的单位取决于pattern精确到的最小时间-->
                <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
                <!--如果文件大于了file_size也会直接产生一个文件-->
                <SizeBasedTriggeringPolicy size="${every_file_size}"/>
            </Policies>
            <!--下标i最大max,文件超过age时间就会被删除,注意age间隔最好>2d, 不然可能会导致删除的时候日志文件还在被占用-->
            <DefaultRolloverStrategy max="${rolling_max}">
                <Delete basePath="${basePath}/info" maxDepth="2">
                    <IfFileName glob="*.log"/>
                    <IfLastModified age="${max_days}"/>
                </Delete>
            </DefaultRolloverStrategy>
            <!-- 匹配INFO级别 -->
            <Filters>
                <ThresholdFilter level="WARN" onMatch="DENY" onMismatch="NEUTRAL"/>
                <ThresholdFilter level="INFO" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
        </RollingFile>

        <!-- 打印WARN级别的日志到文件 -->
        <RollingFile name="WarnFile" fileName="${basePath}/warn.log" filePattern="${basePath}/warn/warn-%d{yyyy-MM-dd}-%i.log">
            <PatternLayout pattern="${file_log_pattern}"/>
            <Policies>
                <!--每天产生一个文件, interval 的单位取决于pattern精确到的最小时间-->
                <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
                <!--如果文件大于了file_size也会直接产生一个文件-->
                <SizeBasedTriggeringPolicy size="${every_file_size}"/>
            </Policies>
            <!--下标i最大max,文件超过age时间就会被删除,注意age间隔最好>2d, 不然可能会导致删除的时候日志文件还在被占用-->
            <DefaultRolloverStrategy max="${rolling_max}">
                <Delete basePath="${basePath}/warn" maxDepth="2">
                    <IfFileName glob="*.log"/>
                    <IfLastModified age="${max_days}"/>
                </Delete>
            </DefaultRolloverStrategy>
            <!-- 匹配WARN级别 -->
            <Filters>
                <ThresholdFilter level="ERROR" onMatch="DENY" onMismatch="NEUTRAL"/>
                <ThresholdFilter level="WARN" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
        </RollingFile>

        <!-- 打印ERROR级别的日志到文件 -->
        <RollingFile name="ErrorFile" fileName="${basePath}/error.log" filePattern="${basePath}/error/error-%d{yyyy-MM-dd}-%i.log">
            <PatternLayout pattern="${file_log_pattern}"/>
            <Policies>
                <!--每天产生一个文件, interval 的单位取决于pattern精确到的最小时间-->
                <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
                <!--如果文件大于了file_size也会直接产生一个文件-->
                <SizeBasedTriggeringPolicy size="${every_file_size}"/>
            </Policies>
            <!--下标i最大max,文件超过age时间就会被删除,注意age间隔最好>2d, 不然可能会导致删除的时候日志文件还在被占用-->
            <DefaultRolloverStrategy max="${rolling_max}">
                <Delete basePath="${basePath}/error" maxDepth="2">
                    <IfFileName glob="*.log"/>
                    <IfLastModified age="${max_days}"/>
                </Delete>
            </DefaultRolloverStrategy>
            <!-- 匹配ERROR级别 -->
            <Filters>
                <ThresholdFilter level="FATAL" onMatch="DENY" onMismatch="NEUTRAL"/>
                <ThresholdFilter level="ERROR" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
        </RollingFile>
    </appenders>

    <!--然后定义logger,只有定义了logger并引入的appender,appender才会生效-->
    <loggers>
        <!-- 设置对打印sql语句的支持 -->
        <!--<logger name="java.sql" level="debug" additivity="false">-->
        <!--<appender-ref ref="Console"/>-->
        <!--</logger>-->

        <!--建立一个默认的root的logger-->
        <root level="${output_log_level}" >
            <appender-ref ref="Console"/>
            <appender-ref ref="InfoFile"/>
            <appender-ref ref="WarnFile"/>
            <appender-ref ref="ErrorFile"/>
            <!--<appender-ref ref="RollingFile"/>-->
        </root>
    </loggers>
</configuration>
```
