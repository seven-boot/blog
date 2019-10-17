TreeMap 是红黑二叉树的典型实现。

存储结构：

```java
private transient Entry<K,V> root;

static final class Entry<K,V> implements Map.Entry<K,V> {
        K key;
        V value;
        Entry<K,V> left;
        Entry<K,V> right;
        Entry<K,V> parent;
        boolean color = BLACK;
}
```

​	可以看到里面存储了 k/v ，左节点、右节点、父节点以及节点颜色。TreeMap 的 put()/remove() 方法大量使用了红黑树的理论。

​	TreeMap 和 HashMap 实现了同样的接口 Map，因此，用法对于调用者来说没有区别。HashMap 效率高于 TreeMap；在需要排序的 Map 时才选用 TreeMap。

### TreeMap 简单示例：

```java
@Test
public void treeMap() {
    Map<Emp, String> treeMap = new TreeMap<>();
    treeMap.put(new Emp(100, "张三", 50000), "张三是个工作积极的人");
    treeMap.put(new Emp(200, "李四", 6000), "李四还行");
    treeMap.put(new Emp(150, "王五", 6000), "王五不错");

    for (Map.Entry<Emp, String> emp :
         treeMap.entrySet()) {
        System.out.println(emp.getKey()+"----"+emp.getValue());
    }
}

class Emp implements Comparable<Emp>{
    int id;
    String name;
    double salary;

    @Override
    public String toString() {
        return "Emp{" +
            "id=" + id +
            ", name='" + name + '\'' +
            ", salary=" + salary +
            '}';
    }

    public Emp(int id, String name, double salary) {
        this.id = id;
        this.name = name;
        this.salary = salary;
    }

    @Override
    public int compareTo(Emp o) {
        if (this.salary > o.salary) {
            return 1;
        } else if (this.salary < o.salary) {
            return -1;
        } else {
            if (this.id > o.id) {
                return 1;
            } else if (this.id < o.id) {
                return -1;
            } else {
                return 0;
            }
        }
    }
}
```

结果打印：

```text
Emp{id=150, name='王五', salary=6000.0}----王五不错
Emp{id=200, name='李四', salary=6000.0}----李四还行
Emp{id=100, name='张三', salary=50000.0}----张三是个工作积极的人
```

