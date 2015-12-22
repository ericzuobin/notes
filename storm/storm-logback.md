##Storm日志配置logback

默认的Storm日志配置不是很好用,这里改成按照天生成日志，
并且每天产生的日志再进行大小切分。

~~~XML
<?xml version="1.0"?>

<configuration scan="true" scanPeriod="60 seconds">

 <!--按照日期,以及文件大小来配置日志级别-->
<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${storm.log.dir}/${logfile.name}</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <fileNamePattern>${storm.log.dir}/${logfile.name}-%d{yyyy-MM-dd}.%i</fileNamePattern>

       <maxHistory>120</maxHistory>

      <timeBasedFileNamingAndTriggeringPolicy
            class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
        <!-- 大于50MB就自动切分 -->
        <maxFileSize>50MB</maxFileSize>
      </timeBasedFileNamingAndTriggeringPolicy>
    </rollingPolicy>

    <encoder>
      <pattern>%d{yyyy-MM-dd'T'HH:mm:ss.SSSZZ} %c{1} [%p] %m%n</pattern>
    </encoder>
 </appender>

 <appender name="ACCESS" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${storm.log.dir}/access.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
      <fileNamePattern>${storm.log.dir}/access.log-%d{yyyy-MM-dd}.%i</fileNamePattern>
      <maxHistory>120</maxHistory>

      <timeBasedFileNamingAndTriggeringPolicy
            class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
        <maxFileSize>100MB</maxFileSize>
      </timeBasedFileNamingAndTriggeringPolicy>
    </rollingPolicy>

    <encoder>
      <pattern>%d{yyyy-MM-dd'T'HH:mm:ss.SSSZZ} %c{1} [%p] %m%n</pattern>
    </encoder>
  </appender>

  <appender name="METRICS" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${storm.log.dir}/metrics.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
      <fileNamePattern>${storm.log.dir}/logs/metrics.log-%d{yyyy-MM-dd}.%i</fileNamePattern>
      <maxHistory>120</maxHistory>

      <timeBasedFileNamingAndTriggeringPolicy
            class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
        <maxFileSize>10MB</maxFileSize>
      </timeBasedFileNamingAndTriggeringPolicy>
   </rollingPolicy>


    <encoder>
      <pattern>%d %-8r %m%n</pattern>
    </encoder>
  </appender>

  <root level="INFO">
    <appender-ref ref="FILE"/>
  </root>

  <logger name="backtype.storm.security.auth.authorizer" additivity="false">
    <level value="INFO" />
    <appender-ref ref="ACCESS" />
  </logger>

  <logger name="backtype.storm.metric.LoggingMetricsConsumer" additivity="false" >
    <level value="INFO"/>
    <appender-ref ref="METRICS"/>
  </logger>

</configuration>
~~~
