## 模拟数据

### 建表

```mysql
CREATE TABLE TEST_NULL (
	ID VARCHAR(100),
	NAME VARCHAR(100)
);

```

### 插入数据：

```mysql
INSERT INTO TEST_NULL (ID, NAME) VALUES('001', '张三');
INSERT INTO TEST_NULL (ID, NAME) VALUES('', '');
INSERT INTO TEST_NULL (ID, NAME) VALUES(NULL, NULL);
```

## 查询

### 所有数据

```mysql
SELECT * FROM  TEST_NULL 
```

### 为 NULL 的数据

```mysql
SELECT * FROM TEST_NULL WHERE NAME IS NULL
```

### 为 '' 的数据

```mysql
SELECT * FROM TEST_NULL WHERE NAME = ''
```

### 既不为空也不为 NULL 的数据

```mysql
SELECT * FROM TEST_NULL WHERE NAME != ''
```

### 不为 NULL 的数据

```mysql
SELECT * FROM TEST_NULL WHERE NAME IS NOT NULL
```

## 总结

mysql 需要过滤空数据的时候要使用 `xxx = '' or xxx is null` 和 `xxx != ''`