title: 分享Y-combinator（Y组合子）学习感想
author: emon100
tags:
  - scheme
  - SICP
categories:
  - 技术研究
date: 2020-01-26 23:07:00
---
# 什么是Y-combinator（Y组合子）
TL;DR：一个输入一个**匿名函数**，输出为一个**递归自己的匿名函数函数**的函数是Y-combinator.

![Lambda 演算骑士团的徽章](../../../../img/paste/pasted-4.png)

20世纪30年代，图灵的老师邱奇提出了lambda演算。由于数学对最简形式的要求，在这个系统中，所有的函数都没有名字（匿名函数），这就带来了一个问题：函数没有名字，怎么处理递归呢？

Y-combinator的出现就是为了解决这个问题，它的参数是一个因为需要**调用自己**的所以需要**接收自己**为参数的函数**A**，返回值是**A**的递归版本。

在理解这个的过程中，我对这种逻辑有了更深的体会。

# 来点实例？

以下需要一些scheme知识。建议下载Racket来执行下面的代码，毕竟，肉眼配对括号还是比较困难。

先看一个有名字的递归函数length，这个函数的目的是为了输出链表长度：

```scheme
(define (length l)
  (cond ((null? l) 0)
        (else (+ 1 (length (cdr l))))))
``` 
或者为了之后研究的方便，我们写length的等价形式：

```scheme
(define length
	(lambda (l)
   (cond ((null? l) 0)
       (else (+ 1 (length (cdr l)))))))
```
length是一个把链表的长度计算出来的函数。可以看到，函数执行过程中递归一层则链表长度+1。

现在我们怎样才能把这个函数改写为匿名函数呢？

首先，这个函数要变得需要调用自己，那么它得**接收自己**为参数，如下：

```scheme
(lambda (length);length现在不是函数名字而是想接收的自己
  (lambda (l)
    (cond ((null? l) 0)
        (else (+ 1 (length (cdr l)))))))
```

有可能会有这样的想法：既然把自己传给自己就能递归了，所以Y-combinator只要做到这点就行了，于是写出了这样的代码：

```scheme
(define Y (lambda (x) (x x)))
```

但我将应用这个Y变换求长度的匿名函数后，得到的函数只能求长度小于等于1的链表起作用。


这时我突然懂了，你们如果看到下面的对长度小于等于3起作用的代码应该也会懂：
```scheme
(define Y (lambda (x) (x (x (x x)))))
;					    ^  ^  ^
                    把自己传给自己三次
```

原来这样写并不够，我们还需要将这个内部的传值过程(自己传给自己)自动化扩展才能让这个函数具有无限递归的能力，我们好想让这个匿名函数自己内部也能应用一次参数，给予这个函数无限的递归能力。我们的目标就变为了先让函数被改成为如下这样：

```scheme
(lambda (length)   ;这个length不只是这个函数本身了，
   (lambda (l)     ;他是一个输入自己，返回自己的函数的代名词
     (cond 
       ((null? l) 0)
       (else (+ 1 ((length length) (cdr l)))))))
; 					   ^      ^
;               自己先自己传给自己，就能用前面的不好用的Y了。


```
能做到吗？事实证明，这个操作是存在的：

```scheme
;把函数加一层包装

(lambda (mk-length)  ;这个新函数接受一个函数，返回这个函数
  ((lambda (length)  ;注意看，把刚刚的匿名函数给代入了
    (lambda (l)  
     (cond     
     ((null? l) 0)
     (else (+ 1 (length (cdr l)))))))
   (lambda (x) ((mk-length mk-length) x))))
   ;上面这个括号是我给代入的匿名函数传的参数
   ;注意，这括号有闭包。
   ;返回值是一个 接受一个参数的函数

```
如果实在看不懂，就把这个函数展开之后的样子写出即可看清了。接下来把代入的匿名函数部分化简为le，变为如下形式：

```scheme
(lambda (le) 
(lambda (mk-length)
      (le (lambda (x) ((mk-length mk-length) x)))))
```

接下来就是把这个新函数自己传递给自己了。用这个重复：

```scheme
(lambda (mk-length)
      (mk-length mk-length))
```
上面的两个代码片段你只需照如下步骤来应用就可以达成Y-combinator的目的：
 1. 把原函数包装为一个符合要求的函数。
 2. 通过把自己传递给自己，把新的函数重复两次。

示例：

```scheme
(((lambda (mk-length)
    (mk-length mk-length)) 
    ;步骤2：让自己成为自己的参数
  ((lambda (le) 
	(lambda (mk-length)
      (le (lambda (x) ((mk-length mk-length) x))))) 
      (lambda (length);length现在不是函数名字而是想接收的自己
         (lambda (l)
           (cond ((null? l) 0)
           (else (+ 1 (length (cdr l)))))))))
           '(1 2 3 4 5)) ;用'(1 2 3 4 5)测试

```

故我们已经知道了如何达成目的，现在让我们动手把**包装**和把**自己当作自己**的参数两个代码片段合二为一吧：

```scheme
(define (Y le) ;定义Y为Y-combinator
  {(lambda (f) {f f}) ;<-步骤2：让自己成为自己的参数
   (lambda (f)
     (le (lambda (x) {(f f) x})))})
;                ^
;                |步骤1：修改此函数

```
这样，Y-combinator就做好了。

现在我们可以直接对函数fun应用Y-combinator `(Y fun)`

# 习题和解答

摘自[王垠博客](https://yinwang0.wordpress.com/2012/04/09/reinvent-y/)的习题和我自己做出的解答：

## 习题
> 试着导出一个适用于两个互相递归函数的Y-combinator，函数可以为下面的形式：

```scheme
(define even
  (lambda (x)
    (cond
     [(zero? x) #t]
     [(= 1 x) #f]
     [else (odd (sub1 x))])))

(define odd
  (lambda (x)
    (cond
     [(zero? x) #f]
     [(= 1 x) #t]
     [else (even (sub1 x))])))
```
## 解答
步骤为：
1. 先把互相递归函数弄成递归自己的函数
2. 再应用上面的思想

```scheme
;The answer of
;https://yinwang0.wordpress.com/2012/04/09/reinvent-y/
;First, make mutual recursive functions into a directive recursive function

(lambda (first second)
  ;return a function which will accept a function itself(the directive recursive function)
  (lambda (f-itself)
    (first (lambda (x)
             ((second f-itself) x)))))
;test
((lambda (first second) (lambda (f) (first (lambda (x) ((second f) x))))) even1 odd1)

((Y ((lambda (first second) (lambda (f) (first (lambda (x) ((second f) x))))) odd1 even1)) 8)


;poor man's
(define (Y-mutual-poor first second)
  {(lambda (f) {f f}) ;<-让自己成为自己的参数
   (lambda (f)
     (((lambda (first second) (lambda (f) (first (lambda (x) ((second f) x))))) first second) (lambda (x) {(f f) x})))})
((Y-mutual-poor odd1 even1) 8)

;final version
(define (Y-mutual first second)
  {(lambda (f) {f f}) ;<-让自己成为自己的参数
   (lambda (f)
     ((lambda (f) (first (lambda (x) ((second f) x)))) (lambda (x) {(f f) x})))})

;Y-mutual final version test
((Y-mutual even1 odd1) 8)
```