# MAVEN下载安装

下载压缩包

解压压缩包

在解压文件中创建仓库

修改仓库地址

```xml
<localRepository>F:\FHL\Install\apache-maven-3.5.4\mvn_repository</localRepository>
```

修改仓库镜像

```xml
    <mirror>
      <id>alimaven</id>  
      <name>aliyun maven</name>  
      <url>https://maven.aliyun.com/repository/central</url>
      <!-- <url>https://maven.aliyun.com/nexus/content/repositories/central</url> -->
      <mirrorOf>central</mirrorOf>
    </mirror>
```



# IDEA集成maven

![20211009151655389](G:\Knowledge Base\note\JAVA\WEB\MAVEN\20211009151655389.png)

![20211009151912007](G:\Knowledge Base\note\JAVA\WEB\MAVEN\20211009151912007.png)



```bash
-Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Dmaven.wagon.http.ssl.ignore.validity.dates=true
```

