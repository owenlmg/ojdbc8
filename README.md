## maven中使用ojdbc8

*ojdbc8，是oracle针对jdk8的驱动*

0. 注册oracle账号

​    `  http://maven.oracle.com`

1. 项目pom.xml中增加repositories

```xml
<repositories>
  <repository>
    <id>maven.oracle.com</id>
    <releases>
      <enabled>true</enabled>
    </releases>
    <snapshots>
      <enabled>false</enabled>
    </snapshots>
    <url>https://maven.oracle.com</url>
    <layout>default</layout>
  </repository>
</repositories>
<pluginRepositories>
  <pluginRepository>
    <id>maven.oracle.com</id>
    <url>https://maven.oracle.com</url>
  </pluginRepository>
</pluginRepositories>
```

1. 配置wagon-http

- 下载wagon-http jar包

  ` http://central.maven.org/maven2/org/apache/maven/wagon/wagon-http/2.8/wagon-http-2.8-shaded.jar `

- 把下载的jar包放入maven安装目录 ` MAVEN_HOME/lib/ext/ `

1. 在maven配置文件中增加HTTP Wagon

   ` MAVEN_HOME/conf/settings.xml ` server节点中增加

   *username是第一步中注册的oracle账号，密码官方建议使用加密的后的密码*

   *加密方式：命令行中执行：` mvn -ep password`*

   生成的密码格式：`{QGNRVlZdnLcHys9LmfPwVe2lZHQ43z7kiyOHPNHnQbg=}`

   ```xml
   <server>
       <id>maven.oracle.com</id>
       <username>username</username>
       <password>password</password>
       <configuration>
         <basicAuthScope>
           <host>ANY</host>
           <port>ANY</port>
           <realm>OAM 11g</realm>
         </basicAuthScope>
         <httpConfiguration>
           <all>
             <params>
               <property>
                 <name>http.protocol.allow-circular-redirects</name>
                 <value>%b,true</value>
               </property>
             </params>
           </all>
         </httpConfiguration>
       </configuration>
     </server>
   ```

1. pom.xml中增加profiles

   ```xml
   <profiles>
       <profile>
         <id>main</id>
         <activation>
           <activeByDefault>true</activeByDefault>
         </activation>
         <repositories>
           <repository>
             <id>maven.oracle.com</id>
             <url>https://maven.oracle.com</url>
             <layout>default</layout>
             <releases>
               <enabled>true</enabled>
             </releases>
           </repository>
         </repositories>
       </profile>
     </profiles>
   ```

   1. pom.xml中增加dependency，version是oracle 的版本

      ```xml
      <dependency>
          <groupId>maven.oracle.com</groupId>
          <artifactId>ojdbc8</artifactId>
          <version>12.2.0.1</version>
      </dependency>
      ```

   1. 在有些网站的说明中，还需要在.m2目录下增加settings-security.xml文件，但官方并没有说需要这个，所以可能是以前需要，但是现在不需要了，内容如下：

      其中的密码也是oracle注册的密码，生成方式：`mvn -emp password`

      ```xml
      <settingsSecurity>
        <master>{xKxjy/OYrcQHbLbJ0sfM9dMZF/YYh6qXyD23Pk31H8w=}</master>
      </settingsSecurity>
      ```

   *mvn 密码生成方式见：`http://maven.apache.org/guides/mini/guide-encryption.html`*
