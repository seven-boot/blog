> 解决相同版本号，修改时间不同，如何获取最新内容

maven 在 build 后从 maven 服务器 Downloading 最新的 maven-metadata.xml，这个文件可以看作版本信息，作为一个版本比对，和本地仓库（.m2/repository）中 jar 包文件夹下的 maven-metadata-local.xml（本地jar包 maven-metadata.xml 的副本）做比较，看 lastUpdated 时间戳值，哪个值更大，就以哪个文件为准。这里需要主义的是，若是 maven-metadata-local.xml 文件的值大，这时候就中止下载了，直接使用本地的 jar 包。

## 更新策略：

修改 maven 配置文件 settings.xml：

```xml
 <repository>
    <id>snapshots</id>
    <name>Snapshots</name>
    <url>url</url>
    <releases>
     <enabled>false</enabled>
    </releases>
    <snapshots>
     <enabled>true</enabled>
     <updatePolicy>always</updatePolicy>
    </snapshots>
 </repository>
```

找到 xml 中的 updatePolicy 标签，改为 never，就不会去远程下载 maven-metadata.xml 文件了。更新策略可选值有：always、never、daily