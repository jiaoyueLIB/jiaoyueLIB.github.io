---
title: 在本地打开服务器的jupyter_notebook
date: 2024-03-28 10:52:11
tags: 
- server
- Python

---



参考链接：[2021.10.29-mobaXterm远程连接jupyter notebook - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/427234430)



因为近期服务器从账号密码登录，改为了ssh密钥登陆，之前远程打开jupyter notebook的流程不管用liao

现在找到的最佳方案是 建立一个local SSH Tunnel

先把这个Tunnel连接上，再去服务器上面打开jupyter 就可以在本地浏览器看到页面啦~~~

昨天捣鼓了半天github的学生优惠，因为mac端的那个termius会员好贵……

termius上一样的操作。
