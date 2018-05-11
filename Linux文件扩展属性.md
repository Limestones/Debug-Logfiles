
```
#TODO: complete synopsis
#doc：
#是什么、做什么、到底是什么、怎么用
```
##  基本内容  
###  文件扩展属性    
*[文件系统](http://blog.51cto.com/13570227/2082908)提供了一套适当的管理方式：权限、特殊权限、属性、隐藏属性、扩展属性（如：访问控制列表ACL）*
> 扩展属性（Extended Attributes,EA或xattrs）:[1](https://blog.csdn.net/ganggexiongqi/article/details/7661024)[2]( https://blog.csdn.net/u013525455/article/details/52795025)[3](http://wiki.linuxquestions.org/wiki/Extended_attributes)提供了一个机制用来将键/值对永久地关联到文件，让现有的文件系统得以“自定义”在原始设计即[基本文件权限](http://wiki.linuxquestions.org/wiki/Permissions)中未提供的功能。
  
（从能干什么——>常用来做什么：Linux扩展属性的具体应用场景和例子）  
> 丰富文件的描述信息，保留有用的元数据。  
  
```
e.g.
筛选（分类）：用户d修改过的所有文件（元数据），涉及各个文件类型、题目、创建时间等等  
```  

###  具体设置  

1. 4种用于扩展属性的命名空间：  

> system：用于实现利用扩展属性的内核功能，例如访问控制表。  
> security：用于实现安全模块。  
> trusted：把受限制的信息存入用户空间。  
> user：一般进程所使用的标准命名空间，经过一般文件权限位来控制此命名空间的访问。  
  
```  
e.g.
system.posix_acl_access便是位于此用户空间的扩展属性，  
用户是否可以读取或写入这些属性取决于所使用的安全模块。  
```
2. 不同文件系统对每个目录/文件的键值对数量、字符串最大长度、字符范围规范进行了不同的限定。
键值对数量：  
字符串最大长度：  
字符范围规范：    

3. [扩展属性命令(ext3等遵循Portable Operating System Interface of UNIX)](http://www.drupal001.com/2013/02/linux-extended-attributes/)  
三个命令：
- setfattr
- getfattr
- attr

###  测试用例（举例）  

*挂载私有客户端之前要在节点上打开文件扩展属性选项*  
```
pscli --command=update_param --section=oApp --name=xattr_enable --current=1
```
*查看当前parastor的扩展属性使能情况*  
```

```
*版本：Parastor-3.0.0.55fa7a1_20180509_103142-Built on Wed May 9 01:52:42 EDT 201*  

``` 
#测试用例 3.0-39715 :: 版本: 1 :: 23-0-1-2 测试获取user.名称的扩展属性 
步骤：
1、挂载私有客户端（10.2.41.186）
2、在10.2.41.185（非主节点）上开启attr
pscli --command=update_param --section=oApp --name=xattr_enable --current=1
3、客户端上创建touch f_3.0-39715_userE
4、setfattr -n user.name0 -v enable f_3.0-39715_userE
5、gerfattr -d f_3.0-39715_userE
返回属性 

补充：
在节点上关闭attr支持
pscli --command=update_param --section=oApp --name=xattr_enable --current=0，即时生效，仍然可以设置attr，但是无法获取
```  
```  
#测试用例 3.0-39716 :: 版本: 1 :: 23-0-1-3 测试获取security.名称的扩展属性   
步骤：
1、挂载私有客户端（10.2.41.186）
2、在被挂载节点10.2.41.185（非主节点）保证上开启attr
pscli --command=update_param --section=oApp --name=xattr_enable --current=1
3、客户端上创建touch f0
4、setfattr -n security.name0 -v enable f0
5、gerfattr -d -m - f0
返回属性

补充：需打开-m -
``` 
##  相关概念  
- [DAC](http://blog.51cto.com/zhaotianyu/1795178)  

> 基于账户体系对文档对象进行权限设置，控制r,w,x权限，当某个进程想对文件进行访问时，系统是通过该进程的用户或者用户组，比较文件的权限，进行判定是否访问。这种方式成为自主访问控制DAC  
> 每个主体拥有一个用户名并属于一个组或具有一个角色
  每个客体都拥有一个限定主体对其访问权限的访问控制列表（ACL）
  每次访问发生时都会基于访问控制列表检查用户标志以实现对其访问权限的控制

- 关于三种基本权限：r，w，x问题回顾：  
  一文件夹的r是什么操作？
  二文件夹的x是什么操作？   
  三问文件能不能够复制要看的权限是？  

*通过mode的方式管理权限*
chgrp：改变文件的用户组；
chown：改变文件的所有者；
chmod：改变文件的权限（owner，group_but not owner，other）（r,w,x)；

```

#八进制umask，421=7：100+010+001=111
chmod 764 f0
chmod u=rwx,g=rw,o=r f0
chmod u+x,g-x,o-x f0  
```
```
#SUID 4 SGID 2 SBIT 1 /root/有本事你就改.log   
#-rwsr-xr-x. 1 root root 25980 2月 22 2012 /root/bin/看你这么可怜帮帮你.sh    
#这就添加了SUID和SGID权限,就是在原有权限的前面加了一个4  

chmod 4777 /root/bin/看你这么可怜帮帮你.sh
```
访问机制细化权限控制——ACL    
```
e.g.  
lxy，wzj，zx等用户同属普通用户组LXY，LXY组对qingshu没有w权限，  
但是要求在root用户登录下，设置：lxy对文qingshu件有w权限，
同时用户和用户组的关系不能变，以保证lxy不能修改其他不属于LXY用户组的文件  
```  

- [ACL](https://www.cnblogs.com/Jimmy1988/p/7249844.html)  

> 可访问控制列表  
  
*对单一用户，单一文件或者目录进行r,w,x的权限设置,完整了访问机制，灵活性、自由度；ACL中使用的键值为：system.posix_acl_access*   
对于某个文件和目录，用户组基本权限分类：文件拥有者、文件拥有者所在组、其他组，这样分就有一个很大的局限性，不利于设置某个用户的权限。  

> getfacl和setfacl   

```
#对于上例
#其中，mask的作用是这样的：它规定了开放权限的范围
touch qingshu
getfacl qingshu 
setfacl -m u:lxy:rw- qingshu
getfacl qingshu   

```



  
- **ADV ACL** 
  
*统一Windows文件系统和Linux文件系统的文件访问管理方式*  

```
/cliparastor/dac_advacl

```
###  更多
1. [Linux文件管理](http://blog.51cto.com/9434538/1698231)   
*上文：文件系统提供了一套适当的管理方式：[权限](http://blog.51cto.com/13570227/2082908)、特殊权限、属性、隐藏属性、扩展属性（如：访问控制列表）*

- [用户/用户组](http://www.runoob.com/linux/linux-user-manage.html)

对应的信息存放在系统文件中，包括/etc/passwd，/etc/shadow（密码明文）, /etc/group；
/etc/passwd中记录内容——

```
e.g.
#环境：CentOs7.4
#head /etc/passd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
parastor:x:137:137::/home/parastor:/bin/bash

```
> 用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell  

用户标识号范围：0~65535,0是root，1-99由系统保留，同一用户组;
主目录：用户的起始工作目录，拥有读写执行权限；
登录shell：运行的命令解释器（或者某个特定程序）

其中，除了常见的*root用户*，*普通用户*外，还有一类*伪用户（psuedo users）*   




2. 文件属性

- 基本属性  

> 名称大小、所属属性、时间属性  
  
*mtime：最近一次文件内容变化时间，是具体内容(数据)变化，不是属性、权限（等元数据）变化,ls显示的结果就是mtime*
*ctime：最近一次文件状态变化时间，这里就是属性、权限（元数据）变化时的时间*
*atime：最近一次读取文件时间，如用cat去读取文件的时候*

```  

stat f0   
```  

- 隐藏（底层）属性   

> chattr和lsattr指令可设置和查看基于ext2/ext3文件系统的底层属性[。](https://blog.csdn.net/BabyFish13/article/details/51182670)设定特殊的标记使其达到某种需求，ext3格式下有13个隐藏属性。   
  
*i代表不能对这个文件进行删除、改名、链接、写入资料等;*
*a代表这个文件只能增加数据不能删除和修改数据*  
```
lsattr f0
chattr +a f0

```


3. 进程和用户（有缘再见，误导了就爆炸了）

![绿色：进程 橙色：文件](http://blog.chinaunix.net/attachment/201405/7/28541347_1399475829ZxxQ.jpg)  









*二级扩展文件系统，ext2（和文件系统扩展属性俩事，但是百度搜索可能会关连； caution：轻易勿用命令e2fsck检查扩展文件系统（ext2）的完整性须umount相关设备文件）* 
