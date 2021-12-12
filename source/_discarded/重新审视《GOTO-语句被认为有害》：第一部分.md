title: 重新审视《GOTO 语句被认为有害》：第一部分
author: emon100
categories:
  - 技术研究
tags:
  - C
  - C++
  - 编程语言
date: 2020-02-06 23:57:00
---
## 译者前言

Dijkstra的《GOTO 语句被认为有害》一文我好奇很久了，因为一直只知文章其名但从未未闻其声，在国内也很难搜到相关信息。这次看到05年写的[这篇](http://david.tribble.com/text/goto.html)批注《GOTO 语句被认为有害》和评论**goto**的用法的文章，我大喜，于是打算乘着假期把这文章阅读并翻译完分享给大家。限于译者水平，可能翻译的不太通顺，还请大家多多指教。以下是文章的第一部分：对《GOTO 语句被认为有害》的介绍，对历史背景的描述以及正文和评注。



*The original letter by Dijkstra quoted in this document is Copyright ©1968 by the [Association for Computing Machinery](http://www.acm.org/) (ACM). All other text in this document is Copyright ©2005 by [David R. Tribble](mailto:david@tribble.com).*



这篇文章可以分享，但请不要转载。



## 介绍

这是对**Edsger W. Dijkstra**于1968年致*计算机科学协会*（CACM）*通讯*的信件的讨论和分析，他在信中呼吁废除编程语言中的**goto**语句。

自首次发表以来，这封信已经名闻遐迩（或声名狼藉，取决于您对**goto**语句的感受），并且可能是有关编程被引用最多的文档。但它可能也是编程史上被阅读最少的文档。

大多数程序员都听过“永不使用**goto**语句”的格言，但在今天，很少有计算机科学专业的学生从Dijkstra反对**goto**的历史背景中受益。现代编程的教条已经接受了“**goto**语句是邪恶的”的神话，但有启发性的是阅读原始文本并意识到这种教条的信念完全没抓住重点。

当时，被接受的编程方式是使用**goto**语句手动编写**迭代循环**，**if-then**和其他控制结构，大多数编程语言都不支持我们今天认为理所当然的基本控制流语句，或者仅提供了非常有限的形式。Dijkstra并不是想说**goto**的*所有*用途都不好，而是应该存在高级控制结构。正确使用高级控制结构将消除当时流行的**goto**语句的*大多数*用途。Dijkstra仍然允许将**goto**用于更复杂的编程控制结构。



## 背景

Dijkstra和其他人（C. A. R. Hoare，Niklaus Wirth等人）在早期为新兴的计算机编程学科提供的工作的重要性不可低估。他们对于建立计算机科学这门严谨的学科以及将算法编程作为数学和逻辑学的官方分支有极有指导意义的贡献。

计算机科学形成初期，Dijkstra就像他的大多数同事一样，是一位接受过大量数学培训的学者。因此，毫不奇怪，计算机科学的许多早期工作都是为了使计算机编程成为一门严格的工程学科而在数学和逻辑方面具有扎实的基础。当时希望能够开发出能够证明程序正确性的编程语言。这种称为*形式验证*的理论是一小套编程结构（如**if-then-else** ，**循环语句**，**原始数据类型**等)可以被设计出且强大到能定义任何可能的编程任务，并且可以在数学上证明是正确的（即没有逻辑错误）。

这项运动始于1950年代后期，其精神类似于早期大卫·希尔伯特（David Hilbert）所说的*希尔伯特纲领*（*Hilbert's programme*)的数学运动。这个数学运动旨在将所有数学用自然数的组成部分以及简单的逻辑和算术规则编纂为一套完整的，包罗万象的法则。唉，哥德尔不完备性定理使这个梦想破灭，证明有存在于逻辑可证明性领域之外的数学真理（和非真理）。

在形式验证运动和牛顿物理学之间可以得出另一个相似之处：在牛顿运动定律被接受的早期，许多人渴望相信物理宇宙是确定性的，只要事先对所涉及的质量和动量有足够的了解，所有运动和活动最终都可以被以任意精度计算。遗憾的是，海森堡，玻尔等人的发现带来了量子力学的到来，从而结束了这一信念。他们意识到，在粒子层面上，所有物理行为本质上都是概率性和随机性的，因此是不可预测的。

同样，*形式验证*的目标最终被认为是行不通的。Dijkstra后来放弃探索程序可证明性而转向了研究正确*程序生成*（program derivation）的技术。此技术是用方法论来使程序员能够以有条理的方式构造程序，确保程序表现出正确的行为。该研究领域与*自顶向下设计*（top-down design）和*功能分解*（functional decomposition）的技术有很多共同点。

还应该认识到，今天被认为是理所当然的许多编程术语在1968年还没有被牢固地确立。当时对于用于编程概念的术语有很多辩论和讨论。我们今天使用的很多术语花了很多年才被广泛接受。

Dijkstra名副其实是一名院士，他在著作中倾向于使用技术上繁琐的词汇。这在一定程度上可以解释为什么很少有人读过他著名的“GOTO”信。值得注意的是，他鄙视用“ *bug* ”一词来表示编程错误，而宁愿用“ *error* ”一词。当然，今天这两个术语仍然在编程语境下使用，但*error*是由任何一个人或一台机器产生了错误（因为它总是有），*bug*仅用于人造系统领域，特别是可编程计算机，以表示设计中的特定故障或意外的执行结果。术语*debug*也应运而生，以表示在系统中查找和删除bug的特定活动。如果只留下*error*一词，*debug*这个好词也不会出现。

# 第一部分



## Dijkstra的信，带评注
接下来是Dijkstra在1968年写给CACM的著名的“GOTO”信，以及从历史角度讨论这封信细节的注释。

<table cols="3" border="0">
 <tbody><tr>
  <td width="20%"></td>
  <td width="55%"></td>
  <td width="25%"></td>
 </tr>
<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">

<h2 align="center">
 Go To Statement Considered Harmful <br>
 <i>Edsger W. Dijkstra</i>
</h2>

<p>
Reprinted from <i>Communications of the ACM</i>, <br>
<a href="http://www.acm.org/classics/oct95/">Vol. 11, No. 3, March 1968, pp. 147-148</a>. <br>
Copyright ©1968, Association for Computing Machinery, Inc.
</p>

<!-- OMITTED [from the original]
<p>
<i>
This is a digitized copy derived from an ACM copyrighted work.
It&nbsp;is not guaranteed to be an accurate copy of the author's original
work.
</i>
</p>
!-->
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0"><!-- OLD bgcolor="#F6F4E4" -->
<font color="#000000" face="helvetica,arial">
<h2 align="right">
 Annotations by <br>
 <i>David R. Tribble</i>
</h2>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">

<a name="para-1"></a>
<p>
编辑：

数年来我观察到，程序员的素质是他们程序中**goto**语句出现密度的递减函数。最近我发现了为什么使用**goto**语句会造成如此灾难性的后果，并且我深信应该从所有“高级”编程语言（即除普通机器代码之外的所有内容）中废除**goto**语句。当时我并不十分重视这个发现。现在，这个话题出现在最近的讨论中，这敦促了我将我的想法发表。
</p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
Dijkstra介绍他已经注意到goto语句在很大程度上不利于它们所在的程序。他认为程序员使用的goto越多，程序员的能力就越差。
</p>

<p>他建议应从所有高级编程语言中删除goto语句。他甚至暗示应该从<i>所有</i>编程语言（可能包括机器代码）中删除goto语句 ，人们不禁要问这如何实现。</p>

</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-2"></a>
<p>我的第一句话是，尽管编程者的动作(activity)在他完成了一个正确程序的时候就结束了， 然而它真正的主体部分，其实是这个程序所控制的流程，因为它必须要实现编程者所期望的效果，也必须在动态特性上满足编程者所期望的规范。不过，一旦程序被编写完成，相关流程(process)的完成就交给机器了。
</p>

</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">

<p>
这一段技术密集的词汇是Dijkstra学术写作风格的典型代表。
</p>
<p>
这仅意味着程序员执行的实际活动不仅是编写程序，还包括控制在计算机上执行代码时代码的动作。但他说，一旦程序员编写了一个工作程序，程序的实际执行就完全只靠机器本身控制。
</p>

<p>
Dijkstra使用“正确”一词来描述没有错误的程序，或者按照目前的说法，没有bug。该术语反映了当时的代码可以进行“形式化验证”的信念，即代码可以进行一系列的数学和逻辑操作，证明该代码包含错误（逻辑错误，约束错误，不变错误...），或者没有，因此可以<i>证明是正确的</i>。正如上面的[背景技术](#background)部分所述，计算机科学家（或至少程序员）今天并不以这种方式考虑编程。

</p>


</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-3"></a>
<p>
我的第二句话是，我们的智力很适合掌握静态关系，而我们可视化随时间变化的动态流程的能力相对较弱。出于这个原因，我们（既然明智的程序员意识到了我们的局限性）应该尽力缩短静态程序和动态流程之间的概念鸿沟，以尽可能消除程序（在文本空间中扩展）和流程（在时间中扩展）之间的关联。
</p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
Dijkstra在这里观察到，与动态关系相比，人类更擅长可视化静态关系。因此他认为，将两种关系表示为程序代码时，我们应将两者之间的差异最小化，以便在源代码本身的结构中明显看出程序的动态（非恒定）方面。
</p>

<p>
对当前大多数以线性，语句接着语句的方式运行的编程语言来说这说法都是对的。但是，当我们观察到现实世界中的编程任务必须处理的复杂性（例如多任务，多线程，中断处理，易失性硬件寄存器，虚拟内存分页，设备延迟，实时事件处理等等，仅举几例）时，在某种程度上，Dijkstra的原则尚未完全实现。简单的一次执行一行的程序设计模型已无法充分解决当今的编程问题。
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-4"></a>

<p>
现在让我们考虑如何表示一个流程的进行。（你可以用一种非常具体的方式考虑这个问题：假设一个流程（按时间顺序的一串行为）在某个行为之后停止，我们必须修正哪些数据才能重做整个流程直到停止的那一点？）如果程序文本纯粹是赋值语句的序列（为方便讨论，赋值语句被视为单个动作的描述），代码中两个<i>连续的动作的描述</i>之间的一点。
</p>

</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">

<p>
Dijkstra开始构建<i>程序执行</i>的正式定义，或他所谓<i>的流程进度</i>。接下来的讨论类似于在C和C++（和其他）语言所采用的执行模型的正式定义中使用的<i>序列点</i>的定义。
</p>

<p>
必须记住，我们今天想当然的许多术语在当时还没有牢固地确立，并且没有普遍接受的语言或伪语言用于论述算法和程序。当然，今天的作者会使用诸如C，Java，Pascal，LISP之类的具体语言，或者与这些语言中的一种非常相似的伪语言作为<i>通用语言</i>来说明编程概念。
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-4b"></a>
<p>
(In the absence of <b>go&nbsp;to</b> statements I can permit myself the
syntactic ambiguity in the last three words of the previous sentence:
if we parse them as "successive (action descriptions)" we mean successive in
text space; if we parse as "(successive action) descriptions" we mean successive
in time.)
Let us call such a pointer to a suitable place in the text a "textual index."
</p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
In typical academic style, Dijkstra employs a bit of linguistic cleverness to
allow the ambiguous phrase <i>successive action descriptions</i> to take on two
different meanings.
This reflects the dual nature of programming tasks that he mentioned previously,
these being related to the sequential nature of executing one statement (or
action) after another, i.e., allowing source code for a program to be composed
of a series of separate statements (actions) to reflect the sequential nature in
which the statements are to be executed in time.
</p>

<p>
His term <i>textual index</i> is essentially a <i>program counter</i>.
However, he is attempting to go beyond simply tracking the location of the
current execution thread, to making an explicit connection between a statement
in the source code text and a program execution state.
So&nbsp;perhaps a better term would be <i>statement pointer</i>.
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-5"></a>
<p>
When we include conditional clauses
(<b>if</b>&nbsp;<i>B</i>&nbsp;<b>then</b>&nbsp;<i>A</i>),
alternative clauses
(<b>if</b>&nbsp;<i>B</i> <b>then</b>&nbsp;<i>A</i><sub>1</sub>
<b>else</b>&nbsp;<i>A</i><sub>2</sub>),
choice clauses as introduced by C.&nbsp;A.&nbsp;R. Hoare
(<b>case</b>[i]&nbsp;<b>of</b>
(<i>A</i><sub>1</sub>, <i>A</i><sub>2</sub>, ···,
<i>A<sub>n</sub></i>)), or conditional expressions as introduced by J. McCarthy
(<i>B</i><sub>1</sub>&nbsp;→&nbsp;<i>E</i><sub>1</sub>,
<i>B</i><sub>2</sub>&nbsp;→&nbsp;<i>E</i><sub>2</sub>,
···,
<i>B<sub>n</sub></i>&nbsp;→&nbsp;<i>E<sub>n</sub></i>),
the fact remains that the progress of the process remains characterized by a
single textual index.
</p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
Dijkstra introduces more complex flow control statements such as
<b>if-then-else</b> conditional statements and <b>case</b>
(a.k.a. <b>select</b> or <b>switch</b>) selection statements,
noting that these do not change the basic nature of his <i>textual index</i>
(or <i>statement pointer</i>).
</p>

<p>
This reflects the fact that, at the time, much effort was being made to
formulate the best minimal set of flow control structures for programming
languages and for programming theory in general.
It&nbsp;is no accident that most of the constructs resembled the control
structures supported by ALGOL, because many of the people who worked or
influenced the design of ALGOL were academics who wrote a great deal about
programming.
</p>

<p>
A major goal of all of this effort was to create a nomeclature that could be
used not just for actual programming languages, but which also could be used
directly for mathematical formulations of programming algorithms.
As&nbsp;stated in the <a href="#background">Background</a> section above,
this reflected a belief that programs could be expressed in a form that would
make it possible to prove their correctness mathematically.
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-6"></a>
<p>
As soon as we include in our language procedures we must admit that a single
textual index is no longer sufficient.
In&nbsp;the case that a textual index points to the interior of a procedure
body the dynamic progress is only characterized when we also give to which call
of the procedure we refer.
With the inclusion of procedures we can characterize the progress of the process
via a sequence of textual indices, the length of this sequence being equal to
the dynamic depth of procedure calling.
</p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
This is an observation that a single statement pointer is not sufficient to
define the state of an executing program if the program employs
<i>subroutines</i> (variously known as <i>procedures</i>, <i>functions</i>, or
<i>methods</i>).
To&nbsp;handle this additional complexity, Dijkstra defines a
<i>sequence of textual indices</i>.
</p>

<p>
This reflects what is known in modern parlance as a <i>call stack</i>, which is
an array of program counters (a.k.a. <i>return addresses</i>), each designating
the last statement from which a procedure call was made.
Since he is establishing an explicit relation between a textual index and the
program execution state, though, it would be more correct to think of
the call stack as an array of statement pointers.
The number of statement pointers needed is simply the number of procedure calls
that are currently active at a given point in the execution, i.e., the depth of
the call stack.
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-7"></a>
<p>
Let us now consider repetition clauses (like,
<b>while</b>&nbsp;<i>B</i>&nbsp;<b>repeat</b>&nbsp;<i>A</i> or
<b>repeat</b>&nbsp;<i>A</i>&nbsp;<b>until</b>&nbsp;<i>B</i>).
Logically speaking, such clauses are now superfluous, because we can express
repetition with the aid of recursive procedures.
For reasons of realism I don't wish to exclude them: on the one hand, repetition
clauses can be implemented quite comfortably with present day finite equipment;
on the other hand, the reasoning pattern known as "induction" makes us well
equipped to retain our intellectual grasp on the processes generated by
repetition clauses.
</p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
Dijkstra adds repetition control flow statements to the mix.
He&nbsp;states in passing that such repetition statements are completely
unnecessary because they can be replaced with equivalent recursive calls.
</p>

<p>
This reflects the fact that, at that time, recursion was very much in vogue
and was considered by many, especially the more academically inclined, to be
a superior form of expressing programs and algorithms.
The reason for this popularity is that recursive definitions have a history of
mathematical rigor - specifically, <i>recursive formulas</i> and <i>recurrence
relations</i>, which deal with recursively defined sequences wherein each
element in the sequence is defined in simpler terms using previous elements
in the sequence.
Two classic examples are the factorial function,
<b>n!&nbsp;=&nbsp;n(n-1)!</b>,
and the Fibonacci sequence,
<b>F<sub>i</sub>&nbsp;=&nbsp;F<sub>i-2</sub>&nbsp;+&nbsp;F<sub>i-1</sub></b>.
</p>

<p>
This is a typically academic observation.
It&nbsp;is true in theory that any looping statement can be replaced with a
recursive call, and certain languages such as LISP do in fact support a
recursive style of programming (also called <i>functional programming</i>).
However, for most programming applications, and consequently what is actually
supported by most programming languages, recursion plays only a minor (but
still very useful) role.
</p>

<p>
Dijkstra mentions that iterative statements can be implemented on
<i>finite equipment</i>, which of course is what all actually existing machines
are, no matter how much virtual memory they possess.
This is a subtle way of admitting that some forms of recursion require
potentially infinite resources (i.e., an infinite call stack).
Consider a typical embedded application having a main program loop that polls
for an event, processes the event, then waits for the next event, looping
forever.
Such an infinite loop could indeed be written as a tail-recursive procedure
call, but what would be the point?
More complicated forms, which are written recursively simply for the sake of
being recursive, would be impossible to program on most real systems.
</p>

<p>
Dijkstra seems to imply that iterative looping (inductive) statements are
intellectually harder to grasp than recursion, which is the kind of thing only a
mathematician would say.
</p>

<p>
<b>iterate</b>, v. - See <i>iterate</i>.
<!-- OMITTED
<br>
This is a more correct variation of the more famous definition:
<br>
<b>recurse</b>, v. - See <i>recurse</i> (but in a different dictionary).
!-->
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-7b"></a>
<p>
With the inclusion of the repetition clauses textual indices are no longer
sufficient to describe the dynamic progress of the process.
With each entry into a repetition clause, however, we can associate a so-called
"dynamic index," inexorably counting the ordinal number of the corresponding
current repetition.
As&nbsp;repetition clauses (just as procedure calls) may be applied nestedly,
we find that now the progress of the process can always be uniquely
characterized by a (mixed) sequence of textual and/or dynamic indices.
</p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
With the addition of repetition control structures, we require a way of
specifying more than just the current statement - we also need to keep track of
which <i>iteration</i> of each loop is currently being executed.
So&nbsp;just like nested procedure calls, we must employ a
<i>loop iteration stack</i> to track these iteration counts, with one entry
per (nested) loop, which Dijkstra calls a <i>dynamic index sequence</i>.
</p>

<p>
So, putting it all together, we have a <i>textual index sequence</i>
(<i>call stack</i>) and a <i>dynamic index sequence</i>
(<i>loop iteration stack</i>), which together define the current state of
an executing program.
</p>

<p>
<i>(Nestedly?)</i>
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-8"></a>
<p>
The main point is that the values of these indices are outside
the <!-- Note: "the" was not present in the original document -->
programmer's control; they are generated (either by the write-up of his program
or by the dynamic evolution of the process) whether he wishes or not.
They provide independent coordinates in which to describe the progress of the
process.
</p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
Again, Dijkstra states the obvious, that once a program is written and running,
the programmer no longer has any control over the actual execution.
The execution is represented by the contents of the call stack and loop
iteration stack at any given point during the execution - what Dijkstra calls
the <i>independent coordinates</i> of the program execution, and what we might
simply call the <i>state</i> or <i>execution history</i> of the program.
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-9"></a>
<p>
Why do we need such independent coordinates?
The reason is - and this seems to be inherent to sequential processes - that we
can interpret the value of a variable only with respect to the progress of the
process.
If&nbsp;we wish to count the number, <i>n</i> say, of people in an initially
empty room, we can achieve this by increasing <i>n</i> by one whenever we see
someone entering the room.
In&nbsp;the in-between moment that we have observed someone entering the room
but have not yet performed the subsequent increase of <i>n</i>, its value equals
the number of people in the room minus one.
</p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
Dijkstra observes that the value of a given variable in a program can only be
known if the history of the program's execution is known precisely up to a given
point in time.
In&nbsp;other words, a program is expected to execute in a <i>deterministic</i>
fashion, and it should be possible to determine the value of any variable at any
point during the execution from the history of the execution (or the history of
the program states) up to that point.
</p>

<p>
Dijkstra introduces the concept of an <i>in-between moment</i> prior to the
completion of a program statement.
This is similar to the notion of a <i>sequence point</i> as specified in
languages like C and C++, which defines precisely when actions are
to occur and in what order, and just as importantly, what actions are left
unspecified.
</p>

<p>
The execution of a program is well-defined only at specific sequence points,
which typically occur at the end of statements, prior to function calls,
and at specific points in the evaluation of subexpressions.
Between any two sequence points, the state of the program is not well-defined,
which means that until the next sequence point is reached,
the values of the program variables are in an indeterminate
(or <i>in-between</i>) state.
</p>

<p>
Dijkstra suggests a simple example of this: incrementing a counter, or
a statement such as <b>n&nbsp;=&nbsp;n+1</b>.
When this statement is actually executed, there is a point during which the
previous value of <b>n</b> is read and <b>1</b> has been added to it,
but that new value has not yet been written back to the variable <b>n</b>.
This is the <i>in-between</i> state he refers to, or a state in the execution
between two <i>sequence points</i>, during which the variable <b>n</b> still
contains its old value instead of its new value.
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-10"></a>
<p>
The unbridled use of the <b>go&nbsp;to</b> statement has an immediate
consequence that it becomes terribly hard to find a meaningful set of
coordinates in which to describe the process progress.
Usually, people take into account as well the values of some well chosen
variables, but this is out of the question because it is relative to the
progress that the meaning of these values is to be understood!
With the <b>go&nbsp;to</b> statement one can, of course, still describe the
progress uniquely by a counter counting the number of actions performed since
program start (viz. a kind of normalized clock).
The difficulty is that such a coordinate, although unique, is utterly unhelpful.
In&nbsp;such a coordinate system it becomes an extremely complicated affair to
define all those points of progress where, say, <i>n</i> equals the number of
persons in the room minus one!
</p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
Here, finally, we get to the crux of Dijkstra's argument concerning the lowly
goto statement.
Essentially, Dijkstra argues that the "unbridled use" of goto
statements in a program obscures the execution state and history of the
program, so that at any given moment the values of the call stack and loop
iteration stack are no longer sufficient to determine the value of the program
variables.
</p>

<p>
This obfuscation is a consequence of the fact that an unconstrained
goto statement can transfer control out of a loop before it is
completed, and likewise can transfer control into the middle of a loop that is
already being iterated.
Both cases complicate the way in which the counters in the loop iteration stack
are modified.
</p>

<p>
Add to that the possibility of <i>non-local</i> gotos, which are
transfers of control out of currently executing procedures back into
previously called procedures, which really disrupt the execution state by
invalidating the values of entire portions of the call stack.
</p>

<p>
Dijkstra states a specific example of transferring control out of a loop or
procedure during an <i>in-between moment</i>, which renders the execution state
indeterminate from that point on.
</p>

<p>
Another way to say this is that gotos can invalidate the
<i>program invariants</i> that are supposed to be guaranteed inviolate by the
program structure.
The example invariant he uses here is that counter <b>n</b> always represents
the number of persons in a room.
Allowing unstructured gotos to change the course of execution
could cause that invariant to become invalid (i.e., no longer invariant), thus
making the value of <b>n</b> meaningless, or at least make it extremely
difficulty to determine its true value from the execution history.
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-11"></a>
<p>
The <b>go&nbsp;to</b> statement as it stands is just too primitive; it is too
much an invitation to make a mess of one's program.
One can regard and appreciate the clauses considered as bridling its use.
I&nbsp;do not claim that the clauses mentioned are exhaustive in the sense that
they will satisfy all needs, but whatever clauses are suggested (e.g. abortion
clauses) they should satisfy the requirement that a programmer independent
coordinate system can be maintained to describe the process in a helpful and
manageable way.
</p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
What Dijkstra means by the goto statement <i>as it stands</i> is
otherwise known as an <i>unstructured</i> goto.
That is, a goto statement with no restrictions about how it may
be used in an otherwise structured language.
</p>

<p>
Limiting the use of gotos to a few simple, well-structured controls
such as exiting early from loops, error handling (a.k.a. exceptions), and the
like brings the goto statement back into the realm of
<i>structured</i> control flow modification.
But without rules that enforce these limitations, the goto
statement provided by a language cannot be said to be truly
<i>well-structured</i>.
</p>

<p>
Dijkstra admits that not all of the flow control structures provided by a
language will satisfy all programming needs.
This implies that goto still has its place for those fairly
rare programming situations that require more complicated flow control.
</p>

<p>
Dijkstra mentions <i>abortion clauses</i>, or what is now commonly known as
<i>exception handlers</i>, hinting that these kinds of things are really fancy
gotos underneath, but that they can be defined so that they
behave well within the confines of a structured language, i.e., that they will
not corrupt the execution state in haphazard ways.
</p>

<p>
The exception handling clauses of object-oriented languages like Ada, C++, Java,
and others have for the most part adhered to this principle, so that when an
exception is thrown in these languages, the execution state (which includes
global and local variables, the procedure call stack, the heap, etc.) is altered
in clean and predictable ways.
</p>

<p>
More primitive languages such as FORTRAN, COBOL, C, Pascal, and the like may
provide some kinds of primitive exception handling mechanisms, but their use
does not guarantee that the execution state is cleanly preserved or that
allocated resources will be properly released.
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-12"></a>
<p>
It is hard to end this with a fair acknowledgment.
Am&nbsp;I to judge by whom my thinking has been influenced?
It&nbsp;is fairly obvious that I am not uninfluenced by Peter Landin and
Christopher Strachey.
Finally I should like to record (as I remember it quite distinctly) how Heinz
Zemanek at the pre-ALGOL meeting in early 1959 in Copenhagen quite explicitly
expressed his doubts whether the <b>go&nbsp;to</b> statement should be treated
on equal syntactic footing with the assignment statement.
To&nbsp;a modest extent I blame myself for not having then drawn the
consequences of his remark.
</p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
Here Dijkstra acknowledges the influences that led him to his observation about
the dangers of goto.
As&nbsp;noted in the <a href="#background">Background</a> section, many of the
people who influenced the design of the ALGOL language were also involved in
discussions about proper language design and correct program control flow
structures.
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-13"></a>
<p>
The remark about the undesirability of the <b>go&nbsp;to</b> statement is far
from new.
I&nbsp;remember having read the explicit recommendation to restrict the use of
the
<b>go&nbsp;to</b> statement to alarm exits, but I have not been able to trace
it; presumably, it has been made by C.&nbsp;A.&nbsp;R. Hoare.
In&nbsp;[<a href="#WIRTH66">1</a>, Sec.&nbsp;3.2.1] Wirth and Hoare together
make a remark in the same direction in motivating the <b>case</b> construction:
</p><blockquote>
 "Like the conditional, it mirrors the dynamic structure of a program more
 clearly than <b>go&nbsp;to</b> statements and switches, and it eliminates the
 need for introducing a large number of labels in the program."
</blockquote>
<p></p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
Dijkstra states that goto statements may be deemed acceptable
(only) for <i>alarm exits</i>, which we would call <i>fatal exceptions</i>.
For languages that lack robust exception handling mechanisms,
gotos may be the only practical substitute.
</p>

<p>
Dijkstra mentions the design of the <b>case</b> (or <b>select</b>)
control flow structure as proposed by Hoare and Wirth.
We&nbsp;take this control structure for granted today, but at the time its
merits were still being debated.
Dijkstra reminds us that it was originally proposed as a superior alternative to
the clumsy use of multiple <b>if</b>s, <b>goto</b>s, and labels.
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="para-14"></a>
<p>
In [<a href="#BOHM66">2</a>] Guiseppe Jacopini seems to have proved the
(logical) superfluousness of the <b>go&nbsp;to</b> statement.
The exercise to translate an arbitrary flow diagram more or less mechanically
into a jump-less one, however, is not to be recommended.
Then the resulting flow diagram cannot be expected to be more transparent than
the original one.
</p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td bgcolor="#F0F8FF">&nbsp;</td>
  <td colspan="2" bgcolor="#F0F0E0">
<font color="#000000" face="helvetica,arial">
<p>
Dijkstra mentions <i>flow diagrams</i>, which reflects the state of the art of
program design at the time.
Since then, programming technology has evolved through the stages of structured
programming, top-down programming, object-oriented programming, component
programming, aspect programming, and beyond.
And yet, in spite of all of these adavances in design, some of Dijkstra's main
point about unstructured program flow remains just as valid now as then.
</p>

<p>
It must be noted that Dijkstra's final comment on the subject seems to imply
that completely removing all of the gotos from one's own programs
is a bad idea.
While he states that it has been proven that goto statements are
in fact redundant in any given program, Dijkstra nevertheless admits that
removing <i>all</i> of the gotos in a program will render its
flow more difficult to understand.
</p>

<p>
He is in fact arguing that <i>some</i> gotos in a program may be
useful and may actually make the program easier to understand.
So&nbsp;it is safe to say that Dijkstra considered goto statements to
be <i>harmful</i>, but not <i>lethal</i>, and certainly not <i>useless</i>.
</p>
</font>
  </td>
 </tr>

<!-- ------------------------------- -->
 <tr>
  <td colspan="2" bgcolor="#F0F8FF">
<font color="#0000B0" face="times,times roman,serif" size="+1">
<a name="Dijkstra-references"></a>
<h3>References:</h3>
<p></p>
<ol type="1">
 <li> <!-- 1. -->
  <a name="WIRTH66"></a>
  Wirth, Niklaus, and Hoare C.&nbsp;A.&nbsp;R. <br>
  A contribution to the development of ALGOL. <br>
  <i>Comm. ACM 9</i> (June 1966), 413-432.

 <p>
 </p></li><li> <!-- 2. -->
  <a name="BOHM66"></a>
  Böhm, Corrado, and Jacopini Guiseppe. <br>
  Flow diagrams, Turing machines and languages with only two formation rules.
  <br>
  <i>Comm. ACM 9</i> (May 1966), 366-371.
 <p></p>
</li></ol>

<a name="signed"></a>
<p>
Edsger W. Dijkstra <br>
<i>Technological University <br>
Eindhoven, The Netherlands</i>
</p>
<p></p>
</font>
  </td>
  <td bgcolor="#F0F0E0">&nbsp;</td>
 </tr>

</tbody></table>