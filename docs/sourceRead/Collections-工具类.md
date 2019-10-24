类 java.util.Collections 提供了对 Set、List、Map 进行排序、填充、查找元素的辅助方法。

- void sort(List<T>) 
  - 对 List 容器内的元素排序，排序的规则是按照升序进行排序
- void shuffle(List)
  - 对 List 容器内的元素进行随机排序
- void reverse(List)
  - 对 List 容器内的元素进行逆序排序
- void fill(List, Object)
  - 用一个特定的对象重写整个 List 容器
- int binarySearch(List, Object)
  - 对于顺序的 List 容器，采用 二分法 查找特定对象