title: Chrome应用MaDe，Export不能使用的解决办法
date: 2014-04-19 11:23:32
tags: [Chrome,Hexo,MaDe]
---
　　推荐Chrome的扩展[MaDe](https://chrome.google.com/webstore/detail/oknndfeeopgpibecfjljjfanledpbkog)  
　　几个优点：
>1. 语法高亮
>2. 实时Preview
>3. 可导入本地md文件，或导出为md/html文件
>4. 跨平台

　　但是在使用过程中发现Export没有效果，发现Export功能不能用，版本也已经是0.8了，作者的Github地址[Lyric Wai](https://github.com/shellex/MaDe)
　　
　　**解决办法**
　　在```
　　C:\Users\**用户名**\AppData\Local\Google\Chrome\User Data\Default\Extensions\oknndfeeopgpibecfjljjfanledpbkog\0.8_0
　　```  
　　修改两个地方
> 1. **修改manifest.json**  
在`""version": "0.8"`下面新增一行  
```
　　"manifest_version": 2
```
> 2. **修改js\made.js**  
将`function export_raw`的实现替换为如下代码:
```
function export_raw(name, data) {
    var urlObject = window.URL || window.webkitURL || window;
 
    var export_blob = new Blob([data]);
 
    var save_link = document.createElementNS("http://www.w3.org/1999/xhtml", "a")
    save_link.href = urlObject.createObjectURL(export_blob);
    save_link.download = name;
    fake_click(save_link);
}
```  
这样就能完美解决了，大家试试吧！
