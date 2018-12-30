---
title: Python 爬虫的基本流程
date: 2018-12-30 22:07:32
tags: 爬虫
categories: 必备技术
---

原创不易！！！禁止未经授权私自转载。在[我的简书](https://www.jianshu.com/u/3925a970afc3)同步更新。

---

> 事先声明: 本文介绍编写一个 Python 爬虫的完整的基本流程，重点在于学习「如何找到并分析响应报文」，而不在于爬虫代码与框架的学习。故此仅适合于爬虫的初学者。

首先，把整个爬虫的流程概述一遍。**简单概括一下流程：***（针对 Chrome 浏览器）*

**1. 通过控制台找到包含内容的响应报文，**
**2. 分析响应报文的请求头部，**
**3. 设置请求头部启动爬虫并解析报文。**

下面通过实例「给定待查询的公司名，爬取他在企查查的网址」来详细地介绍每个流程。

## 一、通过控制台找到包含内容的响应报文

选取一个公司「中导光电设备有限公司」去企查查网站上手动查询公司的过程。

**Step1：** 按 F12 打开控制台后，选到 Network 选项卡中。在查询框输入公司名称。点击查询

![Step1：按 F12 打开控制台后，选到 Network 选项卡中。在查询框输入公司名称。](https://upload-images.jianshu.io/upload_images/11455432-eeec92ca127b6b6f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Step2：** 然后 Network 中输出发起查询请求后所产生的一堆响应报文。我们先从返回文件类型为 doc 的响应报文，开始入手。

首先，通过 Preview 可视化响应报文，更方便查看是否包含所需的内容。

![Step2.1：查看 Preview 选项卡，查看是否包含所需的内容](https://upload-images.jianshu.io/upload_images/11455432-c03d055b1f7a378c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其次，找到后，再在 Response 选项卡中，从代码层锁定内容的位置。

![Step2.2：在 Response 选项卡中，从代码层锁定内容的位置。](https://upload-images.jianshu.io/upload_images/11455432-70db6971f991c8aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图发现，在代码层也找到了内容的位置。此时，我们就能确定这是我们所要的响应报文了。

> 注意事项：有些数据是通过 ajax 异步加载的，故此不在 Doc 报文中。此时，需要选择 XHR 报文中，找出所需的数据。

## 二、分析响应报文的请求头部

**Step3：** 在锁定了响应报文后，我们通过 Headers 选项卡，获得请求该响应报文的头部。在图中，我已圈出一般重点观察的字段。

![Step3：获得请求该响应报文的头部](https://upload-images.jianshu.io/upload_images/11455432-725105a578afdf05.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面讲一下一些常用字段的在爬虫中的作用。

**Request URL：**决定了爬虫发送的请求 url，得到请求 url 是 原本的官网 url 后面 +  `/search?key=<公司名>`。其中在图中看到是一串代码，其实就是公司名进行了特殊的编码后的结果。这可以通过浏览器地址栏确认或者下面的  Query String Parameters 确认。

**Request Method：**决定了爬虫发送请求的方法。

**user-agent：**简单来说，指出浏览器类型、操作系统及版本等等标识信息。填写这个是让爬虫伪造成浏览器。无需改动，直接引用。

**referer：**HTTP 来源地址，用来表示从哪儿链接到目前的网页。在 step2.2 图中 `href` 是一个不完整的 url，此时就需要用到这个字段。即该公司的完整 URL = `https://www.qichacha.com/firm_297af5b193c59e616090ec348836d810.html`

**Query String Parameters：**发送请求报文时携带的请求数据。由于请求方法是 get，所以直接显示在请求 url 中；如果是 post 方法，程序中就要封装成 json 形式。 

**cookie：**如果爬取的网站需要登陆时，需要这个字段。可以看到数据很长，其中有些是必要的，有些是不必要的，这是需要分析的。如果懒得分析，就全部复制粘贴。

至于其他一些字段比较不常用，就不介绍了。*（我也没用过，让我介绍我也不会）*

## 三、设置请求头部启动爬虫并解析报文

通过上面的分析，我们已经摸清数据的位置，请求报文的头部。接下来就是设置请求头部启动爬虫。我就直接上代码，通过注释一点点来讲解。语言：Python3

```python
# 设置请求头部
company_name = '中导光电设备有限公司'
request_url = 'https://www.qichacha.com/search?key={}'.format(company_name)        # 将公司名直接链接上去
headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 '
                  '(KHTML, like Gecko) Chrome/70.0.3538.102 Safari/537.36',        # 直接复制过来
}
response = requests.get(url=request_url, headers=headers)                          #发送请求
# print(response.text)  #输出响应报文，用于验证请求是否成功。被封了或者headers设置错误，就访问失败

# 利用 BeautifulSoup 解析报文,锁定数据的位置并提取出来
# 自行学习 bs4,css 选择器的语法
soup = BeautifulSoup(response.text, "lxml")
# 其中，搜索返回是名称相近的公司列表，我们只取第一条，因为第一条是匹配度最高的
href = soup.select('.m_srchList tbody tr:nth-of-type(1) td:nth-of-type(2) a')[0].get('href')
print('https://www.qichacha.com' + href)

=================
output : https://www.qichacha.com/firm_297af5b193c59e616090ec348836d810.html
```
上面代码就是最基本的爬虫代码：首先设置请求头部，然后用 BeautifulSoup 解析报文。

## 总结

本文目的是「学会如何去找到对应的响应报文、找到数据的位置和分析响应报文请求头部」。关于爬虫库、框架使用网上一堆资料，在这就不赘述。

## 注意事项

由于实例是一个最简单的爬虫，有很多实际问题没有遇到。但还是得说一说注意的细节：

1. 爬虫速度太快，容易被发现而封 ip。解决方法有很多：1）每次爬虫完适当 `time.sleep(x)` 休息一下；2）设置代理
1. 找出只要不被封一定会存在的元素作为基准，来判断爬虫是否被封（有些封并不是访问不了，而是提示需要验证）
2. 写代码的时候，最好及时输出每一步的结果，验证每步操作是否成功
4. 针对 ajax 异步加载的报文（即 XHR 报文），最好先将它的 Request URL 输入到地址栏，验证是否能访问。如果不能，意味着在设置 headers 时需要明确注明是异步请求。具体参考：[爬虫问题 | 记录与总结](https://www.jianshu.com/p/5b45b8784ffd) 中的 Q2.