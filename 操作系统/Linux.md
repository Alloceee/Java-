**linux常用命令**

参考[Linux常用操作命令](https://link.zhihu.com/?target=http%3A//www.jianshu.com/p/03cfc1a721b8)

**91.如何查看内存使用情况**

参考[linux系统下查看CPU、内存负载情况 - JavaQ - 博客频道 - CSDN.NET](https://link.zhihu.com/?target=http%3A//blog.csdn.net/windrui/article/details/40046413)

**92.Linux下如何进行进程调度**

推荐阅读书籍复习，参考文章：
[Linux进程调度原理 - aitao - 博客园](https://link.zhihu.com/?target=http%3A//www.cnblogs.com/zhaoyl/archive/2012/09/04/2671156.html)
[Linux进程调度机制 - rainharder的专栏 - 博客频道 - CSDN.NET](https://link.zhihu.com/?target=http%3A//blog.csdn.net/rainharder/article/details/7975387)

Linux

退出vim编辑器

- Esc
- :wq!
- 回车

如何让命令在后台运行

- 在命令后加上“&”符号
- 例如：&find .-name abc -print&;

 假如你想计划让系统自动在每个月的第一天早上4点钟执行一个维护工作，cron是： 00 4 1 1-12 * /maintenance.pl

计划任务内容格式：分 时 日 月 周 命令/脚本

### 常见命令

ctrl + c :发送sigint信号给前台进程组中的所有进程，常用于终止正在运行的程序

Ctrl + Z：发送sigtstp信号给前台进程组中的所有进程。常用于挂起一个进程

cut ：从文本文件的每一行中截取指定内容的数据

具有很多c语言的功能，又称为过滤器的是awk