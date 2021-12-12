title: CTF初探——记中科大hackergame 2019（非二进制部分）
author: Emon Wang
tags:
  - 杂文
  - C
  - JavaScript
  - Python
  - Web
categories: 技术研究
date: 2019-10-23 00:03:00
---
# 前言
[Hackergame 2019](https://hack.lug.ustc.edu.cn/) 是我参与的第一个信息安全竞赛。超长的比赛时长（7天）、题目覆盖了从网络到密码学到视频处理到逆向等等计算机领域的知识、极有竞技性的排行榜，这些特点让我这样的萌新也感受到了CTF的乐趣。期间我玩的乐不思蜀，一天查询资料10h+而且连续5天肝到凌晨2点。差点耽误正事。。。

说回正题，这次比赛中我还是学到挺多东西的，下面就按照题目给每道非二进制非数学题做一个总结吧。至于二进制，我正在用《程序员的自我修养》这本书来学习。

P.S. 少用百度多用 bing 用 google 用 youtube ，CSDN 的信息噪音在冷门问题前真的很大。
# 已完成题目

## 签到题
这是我的CTF处女题，一来就看到一个不能点的按钮，我之前的审查元素经验让我摁下F12，果然看到了这样的代码:
```html
<button data-v-52fd0931="" disabled="disabled">点击此处，获取 flag</button>
```
另外还有这种，这个比赛果然是面对像我这种萌新的:p
```html
<p hidden="">其实只要把那个 button 的属性修改一下就可以了（</p>
<p hidden="">无论如何，欢迎来到第六届科大信息安全大赛！希望各位玩得开心。</p>
```

## 白与夜
题目之前给的提示特别明显，我一下子就发现这可能是需要把图片反色找文字，果然是这样！

## 信息安全2077

![http状态码](../../../../img/paste/pasted-1.png)

题干提示HTTP/1.0。访问题目网页后发现是一个倒计时，看网络发现flag.txt显示了前提不满足的HTTP状态码。根据题目名字（2077）猜测可能要让服务器知道我在2077年访问这个网站，但是看到倒计时我看了看网页源码。
```javascript
(function () {
      var now = new Date().toUTCString()
...
      fetch('flag.txt', {method: 'POST', headers: {'If-Unmodified-Since': now, 'User-Agent': ...}}).then(function (res) {...})

```
代码已经提示了用If-Unmodified来判断，于是我去研究了一下发送HTTP自定义请求的方法，略微学习了python的request库后写出发送http的程序并获得服务器发送内容，确实有意思。

## 宇宙终极问题

第一问很简单，问你哪3个数字的立方和等于42，这是前几月的爆炸性新闻，随手**百度**到了。（没想到百度成为了我死掉的原因）

第二问让我找某8个数字的立方和等于某个指数，我又尝试**百度**并没有搜索到，于是放弃了。但是之后看题解发现原来可以随手**Google**到...

第三问因为第二问没做出所以没看到

## 网页读写器 

让我尝试访问一个服务器不让访问的网站 http://web1/flag ，并友好的给出了服务端的源代码。重点就放在了检查服务器判断非法url的函数。

```python
def check_hostname(url):
    for i in whitelist_scheme:
        if url.startswith(i):
            url = url[len(i):]  # strip scheme
            url = url[url.find("@") + 1:]  # strip userinfo
            if not url.find("/") == -1:
                url = url[:url.find("/")]  # strip parts after authority
            if not url.find(":") == -1:
                url = url[:url.find(":")]  # strip port
            if url not in whitelist_hostname:
                return (False, "hostname {} not in whitelist".format(url))
            return (True, "ok")
    return (False, "scheme not in whitelist, only {} allowed".format(whitelist_scheme))


```

根据函数，这个服务器会把url的@符号前的东西弄掉再判断url是否合法。哈哈，那我构造个参数里有个@符号的url不就行了。最终写出答案：http://web1/flag?=@www.example.com

BTW，这个服务器是一个 flask app，我正好复习了一下flask路由的写法。

## 达拉崩吧大冒险

这个题使用到了 websocket，于是我去学习了一下，发现这是支持一种服务器可以主动向客户端发送信息的协议，而且和 HTTP 的兼容性很好，很有意思，而且 js 里自带 Websocket 对象，很好用，经过简单学习之后我感觉这道题应该是通过使用 websocket 来做一些事情操作服务器。后来根据检索到的某个 CTF writeup 获知 JavaScript 的整型有溢出的问题并且可以用这个方法弄服务器（ Python 也是动态类型语言，没这个问题啊，JavaScript 真救世语言）。于是我直接 ws.send() 狂发送负值给服务器，然后果然突然变成了正数，瞬间打爆大魔王。

## Happy Lug

这道题我又被**百度**坑了，一开始已经找到了emoji域名，但是死活找不到DNS信息，我真的不太了解域名除了指向ip地址之外还能包含其他信息甚至一串**TXT**。所以做这个题既可以用一个好的搜索引擎获得好的DNS信息网站，也可以用nslookup来查询DNS。

## 正则验证器

这道题我的奇怪思路是主函数的信号会在30s后触发信号导致进入打印flag的函数，但是事实上这是个利用**正则表达式拒绝攻击****(regular expression denial of service)的题目，特定的正则表达式会需要指数级别运行时间。

Cloudflare有一次宕机就是因为这个，可以看他们写的这篇[博客](https://blog.cloudflare.com/details-of-the-cloudflare-outage-on-july-2-2019/#appendix-about-regular-expression-backtracking)。


## 三教奇妙夜

用某种方法得到视频中的一些纯黑的帧即可。我这里使用的是ffmpeg的一个滤镜。这个滤镜会在命令行打印出静止画面的开始和结束实践，那么两个静止画面之间的就是 flag 画面了。我使用的命令如下：
`ffmpeg -i output.mp4 -vf "freezedetect=n=-60dB:d=2" -f null NUL`
这里最后的 NUL 是 Windows 上特殊的文件名，类似 Unix 中的 /dev/null，会将输出的文件丢弃。

命令行输出结果如下：

![命令行输出的结果](../../../../img/paste/pasted-0.png)

或者使用以下命令将静止画面的开始和结束时间输出到文件:
`ffmpeg -i output.mp4 -vf "freezedetect=n=-60dB:d=2,metadata=mode=print:file=freeze.txt" -map 0:v:0 -f null NUL`
