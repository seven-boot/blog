```java
Class A implements Comparator

@Override
    public int compare(Object o1, Object o2) {
        Map<String, String> map1 = (Map) o1;
        Map<String, String> map2 = (Map) o2;
        int flag = map1.get("createTime").toString().compareTo(map2.get("createTime").toString());
        return -flag;
    }


// 按时间倒叙排序
Collections.sort(dynamicList, new Comparator<DoctorDynamicResponse>() {
	@Override
    public int compare(DoctorDynamicResponse o1, DoctorDynamicResponse o2) {
    if (o1.getCreateTime().getTime() > o2.getCreateTime().getTime()) {
    	return -1;
        } else if (o1.getCreateTime().getTime() < o2.getCreateTime().getTime()) {
        	return 1;
        } else {
        	return 0;
            }
        }
    });
```

