---
layout: article
title: Tensorflow-OOM错误
tags: Tensorflow
mode: immersive
key: post20191130
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

<!--more-->

<br/>
被OOM报错困扰了很多天，batch调小、设置session的config等等方法都尝试了，依然会显示GPU显存不足。并且运行时间随batch的增加明显增大，但GPU利用率在运行时都比较高。

<br/>
最后被[这个帖子](https://blog.csdn.net/a362682954/article/details/83743961)提示，排查了一边代码中是否有一直append的变量。最后在加载batch数据的函数中找到：

<br/>

<div class="snippet" markdown="1">

```python
def get_batch_data(x, label, y_cause, batch_size, test=False):
    docs = []
    labels = []
    for index in batch_index(len(y_cause), batch_size, test):
        for i in index:
            docs.append(x[i])
            labels.append(label[i])
        yield docs, labels

```
</div>

<br>
把docs和labels挪到第一层for循环内就完美解决了。

<br/>
