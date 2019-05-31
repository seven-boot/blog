## URI、URL、URN

URI (Uniform Resource Identifier) 是统一资源`标识符` ，而 URL (Uniform Resource Location) 是统一资源 `定位符` 。因此，笼统的说，每一个 URL 都是 URI，但不一定每一个 URI 都是 URL。这是因为 URI 还包括一个子类，即 URN (Uniform Resource Name) 统一资源 `名称` ，它命名资源但不指定如何定位资源。

## URI

URI全称是Uniform Resource Identifier，也就是统一资源标识符，它是一种采用特定的语法标识一个资源的字符串表示。URI所标识的资源可能是服务器上的一个文件，也可能是一个邮件地址、图书、主机名等。简单记为：URI是标识一个资源的字符串(这里先不必纠结标识的目标资源到底是什么，因为使用者一般不会见到资源的实体)，从服务器接收到的只是资源的一种字节表示(二进制序列，从网络流中读取)。URI的语法构成是：一个模式和一个模式特定部分。表示形式如下：

```java
模式:模式特定部分

scheme:scheme specific part 
```

**注意：模式特定部分的表示形式取决于所使用的模式**。URI当前的常用模式包括：

- data：链接中直接包含经过BASE64编码的数据。
- file：本地磁盘上的文件。
- ftp：FTP服务器。
- http：使用超文本传输协议。
- mailto：电子邮件的地址。
- magnet：可以通过对等网络(端对端P2P，如BitTorrent)下载的资源。
- telnet：基于Telnet的服务的连接。
- urn：统一资源名(Uniform Resource Name)。

除此之外，Java中还大量使用了一些非标准的定制模式，如rmi、jar、jndi、doc、jdbc等，这些非标准的模式分别用于实现各种不同的用途。

URI中的模式特定部分没有固定的语法，不过，很多时候都是采用一种层次结构形式，如：

```java
//授权机构/路径?查询参数

//authority/path?query
```

URI的authority部分指定了负责解析该URI其他部分的授权机构(authority)，很多时候，URI都是使用Internet主机作为授权机构。例如http://www.baidu.com/s?ie=utf-8，授权机构是www.baidu.com(在URL角度来看，主机名是www.baidu.com)。

URI的路径(path)是授权机构用来确定所标识资源的字符串。不同的授权机构可能会把相同的路径解析后指向不同的资源。其实这一点很明显，试下你写两个不同的项目，主页的路径都是/index.html，它们一定是完全相同的html文档。另外，路径是可以分层的，分层的各个部分使用斜线"/"进行分隔，而"."和".."操作符用于在分层层次结构中的导航(后面这两个操作符可能很少见到，了解即可)。