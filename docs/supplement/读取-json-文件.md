需要的依赖：

```
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-io</artifactId>
    <version>1.3.2</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.76</version>
</dependency>
```

读取数据：

```
File file = ResourceUtils.getFile("classpath:cities.json");
String str = FileUtils.readFileToString(file);
JSONArray jsonArray = JSON.parseArray(str);
for (Object obj : jsonArray) {
    JSONObject jsonObject = (JSONObject) obj;
    String name = jsonObject.getString("name");
    System.out.println(name);
}
```

[cities.json](assets\cities.json)

