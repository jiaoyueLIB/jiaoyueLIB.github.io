---
title: 部署博客问题记录
date: 2024-03-19 01:20:34
tags: hexo
---







小目标：

​	搞个图床贴图

​	评论区(done)

​	修改代码块样式

​	修改字体样式（好像电脑看有点小啊）

​	写一堆！！！乱七八糟的！

记录帖

## 1. 两台电脑同步部署

参考链接：[真的很清晰，我一个小白都懂了](https://blog.csdn.net/K1052176873/article/details/122879462)

之后的日常工作流程

开始时：

```
git pull
```

快乐的写写写！！！！



哈～



终于写完了！！！



hexo 部署到main branch

```bash
hexo g
hexo s
hexo d
```



Git 来同步我们的修改

```
git add .
git commit -m (some comments)
git push
```

ps: 经常用到的git命令

```bash
git branch #查看当前分支
```



## 2. 用MobaXterm使用hexo d时，一直提示要密码

参考链接：[git pull/push每次都要输入用户名密码的解决办法 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/344314336)

`git config --global credential.helper store`

## 3. 评论区

参考链接：[使用 Utterances 为静态博客添加评论 | ROIFE BLOG](https://roife.github.io/posts/use-utterances-for-blog-comment/)

修改cactus的`.\themes\_config.yml`

## 4. 图床测试

![pics](./edit_blog_on_2devices/4)

