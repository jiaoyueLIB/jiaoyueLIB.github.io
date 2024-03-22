---
title: generate ssh key to login server
date: 2024-03-22 13:44:06
tags: server
---

# 一、本地PC端操作流程：

**推荐使用MobaXterm登陆；下载地址：**[MobaXterm Xserver with SSH, telnet, RDP, VNC and X11 - Download (mobatek.net)](https://mobaxterm.mobatek.net/download.html)

1. 生成证书口令

![image-20240322134618408](https://cdn.jsdelivr.net/gh/jiaoyueLIB/images@main/img/image-20240322134618408.png)

![image-20240322134643908](https://cdn.jsdelivr.net/gh/jiaoyueLIB/images@main/img/image-20240322134643908.png)

记得用鼠标在空白处滑动，好像它的随机数是由鼠标轨迹加密的。

2. 复制添加public key的内容（第一个红框）到各自home目录下的/.ssh/authorized_keys文件（此目录和文件夹需要自己创建）里，注意每个key是一整行，key的内容不要有换行；不同的key要在不同行内。

（建议先存储在txt里 方便粘贴）

**再将这个txt文件复制一个副本， 将整个文件改名为authorized_keys （之后直接将这个文件拖进去就好）**

Key comment备注一下信息便于自己分辨用途或来源

![image-20240322134823065](https://cdn.jsdelivr.net/gh/jiaoyueLIB/images@main/img/image-20240322134823065.png)



3. Save private key到本地一个文件（私钥）

此步骤生成一个后缀名为.ppk的文件，请妥善保存，并知晓存储的位置。

4. Use private key选择该文件

![image-20240322135412652](https://cdn.jsdelivr.net/gh/jiaoyueLIB/images@main/img/image-20240322135412652.png)

#二 、 在服务器端的操作

1. 先用原来的账号密码登陆到自己的文件夹；注意端口更改为：*****

2. 生成和粘贴口令 

注意每个人要用到各自的目录下，不要放root目录，图片是范例

在自己的目录下，赋予文件权限
 ```bash
  mkdir -m 700 .ssh/
  cd .ssh/
 ```

![image-20240322135026411](https://cdn.jsdelivr.net/gh/jiaoyueLIB/images@main/img/image-20240322135026411.png)

**将authorized_keys文件 拖进这个 .ssh/文件夹里面**

![image-20240322135141386](https://cdn.jsdelivr.net/gh/jiaoyueLIB/images@main/img/image-20240322135141386.png)

再修改这个文件的权限 

```bash
chmod 600 authorized_keys
```

3. 修改自己原来的密码，要复杂且需要有大小写字母、数字及特殊符号。（命令：passwd）
4. 自这次修改密码之后，请大家不要再用账号密码登陆。要用证书登陆，切记切记。（在ssh页面里删除密码）

![image-20240322135311356](https://cdn.jsdelivr.net/gh/jiaoyueLIB/images@main/img/image-20240322135311356.png)
