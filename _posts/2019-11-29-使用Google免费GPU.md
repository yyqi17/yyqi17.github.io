---
layout: article
title: 深度学习-使用Google免费GPU 
tags: 环境配置 Tensorflow 深度学习
mode: immersive
key: post20191129
lightbox: true
header:
  theme: dark
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
    src: https://s2.ax1x.com/2019/10/06/ugGXLQ.jpg
---

利用Google Drive与Colaboration，用Google的免费GPU跑代码~（需科学上网）

<!--more-->

<br/>

## 一、准备工作

<br/>

#### 1. 在Google Drive上创建一个文件夹

<br/>
这里用来存放你的代码，包括数据，可以一起打包放进来。Colab从这里获取数据并运行。

<br/>

#### 2. 新建Colab笔记

<br/>

右键，添加Colab笔记。

<br/>
第一次使用时应该是没有的，这时选择“关联更多应用”，搜索Colaboration（一定要输入全名，不然搜不到），将它添加到关联应用里。

<br/>
我在这里遇到的一个问题：添加之后右键里仍然找不到。最后的解决办法是，在google drive的设置里

<br/>
勾选Colaboration的默认情况下使用

<br/>

#### 3. 设置GPU

<br/>
完成上述步骤后，就可以新建一个Colab文件了，我将其命名为ctlTrain.ipynb。

<br/>
在“修改”->“笔记本设置”中，选择python3（根据你需要的python版本选择）和GPU。


<br/>

<br/>

## 二、配置Colab

<br/>
第一次尝试时，一直显示 `google-drive-ocamlfuse: command not found`，因此这里直接介绍我实际使用时可行（但更复杂一些）的通过opam安装的方式。

<br/>

#### 1. 安装opam和google-drive-ocamlfuse

先安装opam，再安装google-drive-ocamlfuse

<div class="snippet" markdown="1">

```
!apt-get install opam
!opam init
!opam update
!opam install depext
!opam depext google-drive-ocamlfuse
!opam install google-drive-ocamlfuse
```

</div>

<br/>

#### 2. 寻找具体路径

<div class="snippet" markdown="1">


```
!find / -name google-drive-ocamlfuse
```
</div>

得到输出

<div class="snippet" markdown="1">

```
/root/.opam/system/share/google-drive-ocamlfuse
/root/.opam/system/lib/google-drive-ocamlfuse
/root/.opam/system/etc/google-drive-ocamlfuse
/root/.opam/system/doc/google-drive-ocamlfuse
/root/.opam/system/bin/google-drive-ocamlfuse
/root/.opam/repo/default/packages/google-drive-ocamlfuse
/root/.opam/packages/google-drive-ocamlfuse
```
</div>

<br/>

#### 3. 授权

<div class="snippet" markdown="1">

```
from google.colab import auth
auth.authenticate_user()
from oauth2client.client import GoogleCredentials
creds = GoogleCredentials.get_application_default()
import getpass
!/root/.opam/system/bin/google-drive-ocamlfuse -headless -id={creds.client_id} -secret={creds.client_secret} < /dev/null 2>&1 | grep URL
vcode = getpass.getpass()
!echo {vcode} | /root/.opam/system/bin/google-drive-ocamlfuse -headless -id={creds.client_id} -secret={creds.client_secret}
# 这里/root/.opam/system/bin/google-drive-ocamlfuse换成刚刚查到的路径，一般来说都是这个路径
```
</div>

<br/>
这里会有两次要求你进入链接，点击后登录你的Google账号并授权，将得到的验证码复制并粘贴到代码输出的文本框里。

<br/>

#### 4. 指定云盘根路径

<div class="snippet" markdown="1">

```
!mkdir -p drive
!/root/.opam/system/bin/google-drive-ocamlfuse drive
```
</div>

<br/>

但非第一次运行的时候，这里需要加上 `nonempty`，即

<div class="snippet" markdown="1">

```
!mkdir -p drive nonempty
!/root/.opam/system/bin/google-drive-ocamlfuse drive  nonempty
```
</div>

<br/>


#### 5. 运行代码

到这里就可以开始运行你的代码了。

```
!python3 drive/deeplearning/train_google.py 
```

<br/>

注意要在原本的train文件中更改数据读取路径。而且要注意，即使将.py文件和data文件夹放在同一个目录下面，也是不可以从相对路径读取的。data文件夹也要从drive/deeplearning/绝对路径获取。

<br/>

## 三、一些注意事项与待改进的问题

<br/>

#### 1. 挂载时间有限制

挂载只有12个小时，也就是说12小时之后需要重新挂载一次。因此<mark>需要在训练时加上checkpoint</mark>，不然如果训练超过12小时，Google断开挂载，前面的训练就都白费了。

<br/>

#### 2. 重新配置环境

Colab背后的服务器是动态分配的，因此每次重新连接之后，都需要再次运行上面的配置代码。包括重新安装包 & 重新授权。

<br/>

#### 3. PyTorch

Colab目前支持的是Tensorflow，如果需要PyTorch的话需要另行安装。

<br/>

#### 4. 另一种上传数据的方式

见第四条参考资料。

<br/>

综上，Colab的优势在于免费、与Google Drive同步。缺点在于操作复杂、连接会自动断开，并且从drive读数据的IO较慢，对于普通学生DL入门跑点小数据还是够用的，大数据集大项目不如氪金来得靠谱。

<br/>

## 参考资料

[薅资本主义羊毛，用Google免费GPU](https://zhuanlan.zhihu.com/p/33344222)

[使用colab时遇到的各种坑--加载云盘里的文件](https://www.jianshu.com/p/1c1f47748827)

[苦逼学生党的Google Colab使用心得](https://zhuanlan.zhihu.com/p/54389036)

[3 个相见恨晚的 Google Colaboratory 奇技淫巧！](https://zhuanlan.zhihu.com/p/56581879)

<br/>
