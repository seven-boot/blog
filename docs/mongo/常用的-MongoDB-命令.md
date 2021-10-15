## 数据库操作

### 查看数据库

```
show dbs
```

### 切换数据库

```
use mydatabase
```

### 删除当前数据库

```
db.dropDatabase()
```

## 集合操作

### 查看集合

```
show collections
```

### 删除集合

```
db.users.drop()
```

## 文档操作

### 插入文档

```
db.users.insert({
    name:'harttle',
    url:'http://harttle.com'
})
```

### 查询文档

#### 查询所有

```
db.users.find()
```

#### 条件查询

```
db.users.find({
    name:'harttle'
})
```

#### 有缩进的输出

```sql
db.users.find().pretty()
```

### 更新文档

```
db.users.update({
    name:'harttle'
}, {
    url:'http://harttle.com'    
})
```

### 删除文档

#### 删除所有

```
db.users.remove({})
```

#### 条件删除

```sql
db.users.remove({
    url:'http://harttle.com'
})
```