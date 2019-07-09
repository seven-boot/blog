```java
public ResponseModel listDoctorDynamic(DoctorDynamicListRequest dynamicListRequest) {
        ResponseModel result = new ResponseModel();
        if (dynamicListRequest.getPageIndex() < 1) {
            result.setResultID(ResponseCode.Try);
            result.setMessage("页码不能小于1");
            return result;
        }
        List<DoctorDynamicResponse> dynamicList = new ArrayList<>();
        List<DoctorDynamicResponse> listDynamic = dynamicRepository.listDynamic(dynamicListRequest.getUserId());
        listDynamic.forEach(doctorDynamic -> {
            doctorDynamic.setType(1);
        });
        List<DoctorDynamicResponse> listCase = dynamicRepository.listCase(dynamicListRequest.getUserId());
        listCase.forEach(doctorCase -> {
            doctorCase.setType(2);
        });

        dynamicList.addAll(listDynamic);
        dynamicList.addAll(listCase);
        // 如果动态为空，直接返回，跳过排序、分页、点赞、动态等
        if (dynamicList.isEmpty()) {
            result.setData(dynamicList);
            return result;
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
        // 分页
        int totalCount = dynamicList.size();    // 总记录数
        int pageCount = 0;      // 总页数
        int m = totalCount % dynamicListRequest.getPageSize();
        if (m > 0) {
            pageCount = totalCount / dynamicListRequest.getPageSize() + 1;
        } else {
            pageCount = totalCount / dynamicListRequest.getPageSize();
        }

        if (m == 0) {   // 刚好分够整数页，没有余数
            dynamicList = dynamicList.subList((dynamicListRequest.getPageIndex() - 1) * dynamicListRequest.getPageSize(), dynamicListRequest.getPageIndex() * dynamicListRequest.getPageSize());
        } else {
            if (dynamicListRequest.getPageIndex() == pageCount) {      // 查询最后一页
                dynamicList = dynamicList.subList((dynamicListRequest.getPageIndex() - 1) * dynamicListRequest.getPageSize(), totalCount);
            } else {
                dynamicList = dynamicList.subList((dynamicListRequest.getPageIndex() - 1) * dynamicListRequest.getPageSize(), dynamicListRequest.getPageIndex() * dynamicListRequest.getPageSize());
            }
        }

        result.setData(dynamicList);
        return result;
    }
```

