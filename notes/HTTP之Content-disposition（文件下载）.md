# HTTP之Content-disposition（文件下载）

Content-disposition详细介绍地址：http://www.rfc-editor.org/rfc/rfc1806.txt

Content-disposition（内容-部署）是MIME协议的扩展，MIME协议指示MIME用户代理如何显示附加的文件。Content-disposition可以控制用户请求所得的内容存为一个文件的时候提供一个默认的文件名，文件直接在浏览器上显示或者在访问时弹出文件下载对话框。

------

Content-disposition具体定义：

```go
//content-disposition的定义
disposition := "Content-Disposition" ":"  
               disposition-type  
               *(";" disposition-parm)  
disposition-type := "inline"  
                  / "attachment"  
                  / extension-token  
                  ; values are not case-sensitive  
disposition-parm := filename-parm / parameter  
filename-parm := "filename" "=" value;  
```

Content-disposition属性有两种类型：inline 和 attachment

 inline ：将文件内容直接显示在页面 attachment：弹出对话框让用户下载

在页面内打开：

```java
 response.setHeader("Content-Disposition","inline;filename="+ URLEncoder.encode(fileName, "UTF-8"));  
```

弹出保存框：

```java
// 在响应头中设置content-disposition控制浏览器弹出保存框，若没有此句则浏览器会直接打开并显示文件。中文名要经过URLEncoder.encode编码，否则虽然客户端能下载但显示的名字是乱码
response.setHeader("Content-Disposition","attachment;filename="+ URLEncoder.encode(fileName, "UTF-8"));  
```

这样的话，浏览器在打开的时候回提示保存还是打开，即使选择打开，也会使用相关联的程序，比如记事本打开，而不是浏览器直接打开。``Content-Disposition就是当用户想把请求所得的内容存为一个文件的时候提供一个默认的文件名。``

filename参数可以包含路径信息，但User-Agnet会忽略这些信息，只会把路径信息的最后一部分作为文件名。

当你在内容类型为application/octet- stream情况下使用了这个头信息的话，那就意味着你不想直接显示内容，而是弹出一个"文件下载"的对话框，接下来就是由你来决定"打开"还是"保存" 了。

**IE浏览器下载可能存在乱码问题**