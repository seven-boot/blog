> 项目想下载一个依赖，在 idea 中怎么都下载不下来，省略网上的修改远程仓库地址方案，出绝招，手动下载 jar包，然后导入到本地仓库，命令如下:

```shell
mvn install:install-file -Dfile=d:\extenrnalResp\dubbo-2.7.5.jar -DgroupId=org.apache.dubbo -DartifactId=dubbo -Dversion=2.7.5 -Dpackaging=jar
```

- 首先下载需要的 jar 包

  [阿里云仓库地址]( https://maven.aliyun.com/mvn/search )

  这里举例 dubbo：

  ```xml
  <dependency>
      <groupId>org.apache.dubbo</groupId>
      <artifactId>dubbo</artifactId>
      <version>2.7.5</version>
  </dependency>
  ```

  下载 jar包到目录：d:\extenrnalResp\dubbo-2.7.5.jar

- 运行 cmd，输入上边的命令，刷新 idea->maven，即可看到依赖被导入了