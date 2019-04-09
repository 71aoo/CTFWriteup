### AceBear Store Image Service --Playwin

![xGOAciU](C:\Users\Playwin\Desktop\xGOAciU.png)
在upload页面可以上传图片，支持url，有点暗示ssrf的意思，经过测试，不止支持ssrf，还支持文件包含，用file协议读取文件。

payload：file:///var/www/html/s3rv1c3/Fl4g.php（注意一下目录就行）

查看源码

![xH5xNiu](C:\Users\Playwin\Desktop\xH5xNiu.png)
ps：一开始出题人没给出flags所在文件名，直到给了hint才做出

------

#### 还有个payload很有意思

file:///proc/self/cwd/Fl4g.php.



/proc/self 是一个有趣的子目录，它使得程序可以方便地使用/proc 查找本进程地信息。/proc/self 是一个链接到/proc 中访问/proc 的进程所对应的PID 的目录的符号链接，"cwd"是指向进程当前工作目录的符号链接