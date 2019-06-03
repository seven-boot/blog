## 什么是 nth-tabs ?

基于 Bootstrap 的多功能选项卡插件

## 其他依赖

- 滚动条：jquery.scrollbar
- 字体图标：font-awesome

## 使用说明

### CSS

```text
<link href="https://cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap.min.css" rel="stylesheet">
<link href="https://cdn.bootcss.com/font-awesome/4.4.0/css/font-awesome.min.css" rel="stylesheet">
<link href="https://cdn.bootcss.com/jquery.scrollbar/0.2.11/jquery.scrollbar.min.css" rel="stylesheet">
<link href="css/nth.tabs.min.css" rel="stylesheet">
```

### JS

```text
<script src="https://cdn.bootcss.com/jquery/2.1.4/jquery.min.js"></script>
<script src="https://cdn.bootcss.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
<script src="https://cdn.bootcss.com/jquery.scrollbar/0.2.11/jquery.scrollbar.min.js"></script>
<script src="js/nth.tabs.min.js"></script>
```

### html

```text
<div class="nth-tabs" id="custom-id"></div>
```

### 初始化

```text
nthTabs = $("#custom-id").nthTabs();
```

### 添加一个选项卡

```text
nthTabs.addTab({
 id:'a',
 title:'孙悟空',
 content:'看我七十二变',
});
```

### 添加一个不可关闭的选项卡

```text
nthTabs.addTab({
  id:'a',
  title:'孙悟空',
  content:'看我七十二变',
  allowClose:false,
});
```

### 添加多个选项卡

```text
nthTabs.addTab({
        id:'a',
        title:'孙悟空',
        content:'看我七十二变',
}).addTab({
        id:'b',
        title:'孙悟空二号',
        content:'看我七十三变',
});
```

### 删除一个选项卡

```text
nthTabs.delTab('id');
```

### 删除其他选项卡

```text
nthTabs.delOtherTab();
```

### 删除所有选项卡

```text
nthTabs.delAllTab();
```

### 切换到指定选项卡

```text
nthTabs.setActTab(id);
```

### 定位到当前选项卡

```text
nthTabs.locationTab();
```

### 左滑动

```text
$('.roll-nav-left').click();
```

### 右滑动

```text
$('.roll-nav-right').click();
```

### 获取左右滑动步值

```text
nthTabs.getMarginStep();
```

### 获取当前选项卡ID

```text
nthTabs.getActiveId();
```

### 获取所有选项卡宽度

```text
nthTabs.getAllTabWidth();
```

### 获取所有选项卡

```text
nthTabs.nthTabs.getTabList();
```

## 附：群里提供的版本

群里提供的版本是为了适应 AdminLTE 而修改的版本，使用方式略有不同

### CSS

```text
<link href="https://cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap.min.css" rel="stylesheet">
<link href="https://cdn.bootcss.com/font-awesome/4.4.0/css/font-awesome.min.css" rel="stylesheet">
<link href="https://cdn.bootcss.com/jquery.scrollbar/0.2.11/jquery.scrollbar.min.css" rel="stylesheet">
<link href="css/nth.tabs.css" rel="stylesheet">
```

主要修改了 `nth.tabs.css` 部分样式以适应 AdminLTE

### JS

```text
<script src="https://cdn.bootcss.com/jquery/2.1.4/jquery.min.js"></script>
<script src="https://cdn.bootcss.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
<script src="https://cdn.bootcss.com/jquery.scrollbar/0.2.11/jquery.scrollbar.min.js"></script>
<script src="js/nth.tabs.min.js"></script>
<script src="js/nth-tabs.js"></script>
```

主要增加了 `nth-tabs.js` 这个二次封装的自定义工具类

### html

```text
<div class="nth-tabs common-bg" id="editor-tabs"></div>
```

### 初始化

```text
$(function () {
    NthTabs.home("/path");
});
```

### 添加一个选项卡

```text
NthTabs.addTab('id', 'name', '/path');
```