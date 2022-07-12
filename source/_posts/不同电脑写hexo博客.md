---
title: 用不同电脑写hexo博客
date: 2022-07-11 23:37:43
categories: hexo
---


# 基于GitHub双分支方法

<br><br><br><br><br><br>

## 1. 在每次编辑之前
---

需要先 ` git pull ` 拉取hexo分支的内容到本地，即同步更新，然后再进行上述操作。

---

<br><br><br><br><br><br>

## 2. 修改本地文件和编辑博客内容
---

Windows下用 git bash 打开本地文件夹


然后就是 `hexo new [layout] <title>` 创建新文件

{% asset_img 1.png %}


> [相关hexo操作](https://zhuanlan.zhihu.com/p/156915260)

>  [markdown语法](https://markdown.com.cn/basic-syntax/)

> 图片插入格式 `{% asset_img example.jpg This is an example image %}`


---


<br><br><br><br><br><br>

## 3. 编辑和修改完成之后

---

先归并到hexo分支

```
    git add .
    git commit -m "xxx"'
    git push
```

再用 

 `hexo d -g`

 部署到master分支中的网页上。


---