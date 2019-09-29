---
title: Markdown中写数学公式
mathjax: true
categories: 办公
abbrlink: 4675177d
date: 2019-03-14 10:09:21
---
&emsp;&emsp;一些扩展的`Markdown`语法支持采用`LaTex`语法写数学公式，而在网页中使用`Mathjax`插件来显示数学公式。在`Markdown`中插入数学公式的语法是`$数学公式$`和`$$数学公式$$`。
&emsp;&emsp;行内公式是可以让公式在文中与文字或其他东西混编，不独占一行：

``` latex
质能方程$E = mc^2$
```

质能方程$E = mc^2$。
&emsp;&emsp;独立公式使公式单独占一行，不与文中其他文字等混编：

``` latex
$$E = mc^2$$
```

$$E = mc^2$$

### 普通公式

&emsp;&emsp;普通的加减乘除数学公式的输入方法与平常的书写一样：

``` latex
$$x = 100 * y + z - 10 / 33 + 10 % 3$$
```

$$x = 100 * y + z - 10 / 33 + 10 % 3$$

### 上下标

&emsp;&emsp;使用`^`来表示上标，`_`来表示下标，同时如果上下标的内容多于一个字符，可以使用`{}`来将这些内容括起来当做一个整体。实际上，上下标是可以嵌套的。

``` latex
$$x = a_{1}^n + a_{2}^n + a_{3}^n$$
```

$$x = a_{1}^n + a_{2}^n + a_{3}^n$$

如果希望左右两边都能有上下标，可以使用`\sideset`语法：

``` latex
$$\sideset{^1_2}{^3_4}A$$
```

$$\sideset{^1_2}{^3_4}A$$

### 括号

&emsp;&emsp;`()`、`[]`和`|`都表示它们自己，但是`{}`因为有特殊作用，因此当需要显示大括号时，一般使用`\lbrace \rbrace`来表示。

``` latex
$$f(x, y) = 100 * \lbrace[(x + y) * 3] - 5\rbrace$$
```

$$f(x, y) = 100 * \lbrace[(x + y) * 3] - 5\rbrace$$

### 分数

&emsp;&emsp;分数使用`\frac{分母}{分子}`这样的语法，不过推荐使用`\cfrac`来代替`\frac`，显示公式不会太挤。

``` latex
$$\frac{1}{3} 与 \cfrac{1}{3}$$
```

$$\frac{1}{3} 与 \cfrac{1}{3}$$

### 开方

&emsp;&emsp;开方使用`\sqrt[次数]{被开方数}`这样的语法：

``` latex
$$\sqrt[3]{X}$$
$$\sqrt{5 - x}$$
```

$$\sqrt[3]{X}$$
$$\sqrt{5 - x}$$

### 希腊字母

代码         | 显示        | 代码         | 显示
-------------|------------|--------------|--------
`$A$`        | $A$        | `$\alpha$`   | $\alpha$
`$B$`        | $B$        | `$\beta$`    | $\beta$
`$\Gamma$`   | $\Gamma$   | `$\gamma$`   | $\gamma$
`$\Delta$`   | $\Delta$   | `$\delta$`   | $\delta$
`$E$`        | $E$        | `$\epsilon$` | $\epsilon$
`$Z$`        | $Z$        | `$\zeta$`    | $\zeta$
`$H$`        | $H$        | `$\eta$`     | $\eta$
`$\Theta$`   | $\Theta$   | `$\theta$`   | $\theta$
`$I$`        | $I$        | `$\iota$`    | $\iota$
`$K$`        | $K$        | `$\kappa$`   | $\kappa$
`$\Lambda$`  | $\Lambda$  | `$\lambda$`  | $\lambda$
`$M$`        | $M$        | `$\mu$`      | $\mu$
`$N$`        | $N$        | `$\nu$`      | $\nu$
`$\Xi$`      | $\Xi$      | `$\xi$`      | $\xi$
`$O$`        | $O$        | `$\omicron$` | $\omicron$
`$\Pi$`      | $\Pi$      | `$\pi$`      | $\pi$
`$P$`        | $P$        | `$\rho$`     | $\rho$
`$\Sigma$`   | $\Sigma$   | `$\sigma$`   | $\sigma$
`$T$`        | $T$        | `$\tau$`     | $\tau$
`$\Upsilon$` | $\Upsilon$ | `$\upsilon$` | $\upsilon$
`$\Phi$`     | $\Phi$     | `$\phi$`     | $\phi$
`$X$`        | $X$        | `$\chi$`     | $\chi$
`$\Psi$`     | $\Psi$     | `$\psi$`     | $\psi$
`$\Omega$`   | $\Omega$   | `$\omega$`   | $\omega$

### 集合运算符

代码          | 符号         |  代码         | 符号
--------------|-------------|---------------|----
`$\emptyset$` | $\emptyset$ | `$\in$`       | $\in$
`$\notin$`    | $\notin$    | `$\subset$`   | $\subset$
`$\supset$`   | $\supset$   | `$\subseteq$` | $\subseteq$
`$\supseteq$` | $\supseteq$ | `$\bigcap$`   | $\bigcap$
`$\bigcup$`   | $\bigcup$   | `$\bigvee$`   | $\bigvee$
`$\bigwedge$` | $\bigwedge$ | `$\biguplus$` | $\biguplus$
`$\bigsqcup$` | $\bigsqcup$ | `$\ni$`       | $\ni$
`$\forall$`   | $\forall$   | `$\exists$`   | $\exists$
`$\neg$`      | $\neg$      |               |

### 对数运算符

代码     | 符号    | 代码    | 符号
---------|--------|---------|-----
`$\log$` | $\log$ | `$\lg$` | $\lg$
`$\ln$`  | $\ln$  |         |

### 三角运算符

代码      | 符号   | 代码       | 符号
---------|--------|------------|---------
`$\bot$` | $\bot$ | `$\angle$` | $\angle$
`$\sin$` | $\sin$ | `$\cos$`   | $\cos$
`$\tan$` | $\tan$ | `$\cot$`   | $\cot$
`$\sec`  | $\sec$ | `$\csc$`   | $\csc$

### 微积分运算符

代码           | 符号          | 代码         | 符号
---------------|--------------|--------------|-----
`$\prime$`     | $\prime$     | `$\int$`     | $\int$
`$\iint$`      | $\iint$      | `$\iiint$`   | $\iiint$
`$\oint$`      | $\oint$      | `$\lim$`     | $\lim$
`$\infty$`     | $\infty$     | `$\nabla$`   | $\nabla$
`$\mathrm{d}$` | $\mathrm{d}$ | `$\partial$` | $\partial$

### 绝对值

&emsp;&emsp;代码如下：

``` latex
$\left| x \right|$
```

$\left| x \right|$

### 箭头符号

代码                    | 符号                   | 代码                    | 符号
------------------------|-----------------------|-------------------------|----
`$\gets$`               | $\gets$               | `$\to$`                 | $\to$
`$\leftarrow$`          | $\leftarrow$          | `$\Leftarrow$`          | $\Leftarrow$
`$\rightarrow$`         | $\rightarrow$         | `$\Rightarrow$`         | $\Rightarrow$
`$\leftrightarrow$`     | $\leftrightarrow$     | `$\Leftrightarrow$`     | $\Leftrightarrow$
`$\mapsto$`             | $\mapsto$             | `$\hookleftarrow$`      | $\hookleftarrow$
`$\leftharpoonup$`      | $\leftharpoonup$      | `$\leftharpoondown$`    | $\leftharpoondown$
`$\rightleftharpoons$`  | $\rightleftharpoons$  | `$\longleftarrow$`      | $\longleftarrow$
`$\Longleftarrow$`      | $\Longleftarrow$      | `$\longrightarrow$`     | $\longrightarrow$
`$\Longrightarrow$`     | $\Longrightarrow$     | `$\longleftrightarrow$` | $\longleftrightarrow$
`$\Longleftrightarrow$` | $\Longleftrightarrow$ | `$\longmapsto$`         | $\longmapsto$
`$\hookrightarrow$`     | $\hookrightarrow$     | `$\rightharpoonup$`     | $\rightharpoonup$
`$\rightharpoondown$`   | $\rightharpoondown$   | `$\leadsto$`            | $\leadsto$
`$\uparrow$`            | $\uparrow$            | `$\Uparrow$`            | $\Uparrow$
`$\downarrow$`          | $\downarrow$          | `$\Downarrow$`          | $\Downarrow$
`$\updownarrow$`        | $\updownarrow$        | `$\Updownarrow$`        | $\Updownarrow$
`$\nearrow$`            | $\nearrow$            | `$\searrow$`            | $\searrow$
`$\swarrow$`            | $\swarrow$            | `$\nwarrow$`            | $\nwarrow$

### 操作符

代码                 | 符号                | 代码              | 符号            | 代码                | 符号
---------------------|--------------------|-------------------|-----------------|--------------------|-----
`$\pm$`              | $\pm$              | `$\mp$`           | $\mp$           | `$\times$`         | $\times$
`$\div$`             | $\div$             | `$\cdot$`         | $\cdot$         | `$\ast$`           | $\ast$
`$\star$`            | $\star$            | `$\dagger$`       | $\dagger$       | `$\ddagger$`       | $\ddagger$
`$\amalg$`           | $\amalg$           | `$\cap$`          | $\cap$          | `$\cup$`           | $\cup$
`$\uplus$`           | $\uplus$           | `$\sqcap$`        | $\sqcap$        | `$\sqcup$`         | $\sqcup$
`$\vee$`             | $\vee$             | `$\wedge$`        | $\wedge$        | `$\oplus$`         | $\oplus$
`$\ominus$`          | $\ominus$          | `$\otimes$`       | $\otimes$       | `$\circ$`          | $\circ$
`$\bullet$`          | $\bullet$          | `$\diamond$`      | $\diamond$      | `$\lhd$`           | $\lhd$
`$\rhd$`             | $\rhd$             | `$\unlhd$`        | $\unlhd$        | `$\unrhd$`         | $\unrhd$
`$\oslash$`          | $\oslash$          | `$\odot$`         | $\odot$         | `$\bigcirc$`       | $\bigcirc$
`$\triangleleft$`    | $\triangleleft$    | `$\Diamond$`      | $\Diamond$      | `$\bigtriangleup$` | $\bigtriangleup$
`$\bigtriangledown$` | $\bigtriangledown$ | `$\Box$`          | $\Box$          | `$\triangleright$` | $\triangleright$
`$\setminus$`        | $\setminus$        | `$\wr$`           | $\wr$           | `$\sqrt{x}$`       | $\sqrt{x}$
`$x^{\circ}$`        | $x^{\circ}$        | `$\triangledown$` | $\triangledown$ | `$\sqrt[n]{x}$`    | $\sqrt[n]{x}$
`$a^x$`              | $a^x$              | `$a^{xyz}$`       | $a^{xyz}$       | `$\sum$`           | $\sum$
`$\prod$`            | $\prod$            |                   |                 |                    |

### 关系运算符

代码            | 符号           | 代码              | 符号            | 代码             | 符号
----------------|---------------|-------------------|-----------------|-----------------|-----
`$\le$`         | $\le$         | `$\ge$`           | $\ge$           | `$\neq$`        | $\neq$
`$\sim$`        | $\sim$        | `$\ll$`           | $\ll$           | `$\gg$`         | $\gg$
`$\doteq$`      | $\doteq$      | `$\simeq$`        | $\simeq$        | `$\lneqq$`      | $\lneqq$
`$\gneqq$`      | $\gneqq$      | `$\approx$`       | $\approx$       | `$\asymp$`      | $\asymp$
`$\lnsim$`      | $\lnsim$      | `$\gneq$`         | $\gneq$         | `$\cong$`       | $\cong$
`$\smile$`      | $\smile$      | `$\sqsubset$`     | $\sqsubset$     | `$\sqsupset$`   | $\sqsupset$
`$\equiv$`      | $\equiv$      | `$\frown$`        | $\frown$        | `$\sqsubseteq$` | $\sqsubseteq$
`$\sqsupseteq$` | $\sqsupseteq$ | `$\propto$`       | $\propto$       | `$\bowtie$`     | $\bowtie$
`$\lneq$`       | $\lneq$       | `$\ngtr$`         | $\ngtr$         | `$\prec$`       | $\prec$
`$\succ$`       | $\succ$       | `$\vdash$`        | $\vdash$        | `$\dashv$`      | $\dashv$
`$\preceq$`     | $\preceq$     | `$\succeq$`       | $\succeq$       | `$\models$`     | $\models$
`$\perp$`       | $\perp$       | `$\parallel$`     | $\parallel$     | `$\mid$`        | $\mid$
`$\bumpeq$`     | $\bumpeq$     | `$\nmid$`         | $\nmid$         | `$\nleq$`       | $\nleq$
`$\ngeq$`       | $\ngeq$       | `$\nsim$`         | $\nsim$         | `$\ncong$`      | $\ncong$
`$\nparallel$`  | $\nparallel$  | `$\not<$`         | $\not<$         | `$\not>$`       | $\not>$
`$\not=$`       | $\not=$       | `$\not\le$`       | $\not\le$       | `$\not\ge$`     | $\not\ge$
`$\not\sim$`    | $\not\sim$    | `$\not \approx$`  | $\not\approx$   | `$\not\cong$`   | $\not\cong$
`$\not\equiv$`  | $\not\equiv$  | `$\not\parallel$` | $\not\parallel$ | `$\nless$`      | $\nless$

### Accents符号

代码               | 符号              | 代码              | 符号            | 代码                 | 符号
-------------------|------------------|-------------------|-----------------|---------------------|-----
`$\hat{x}$`        | $\hat{x}$        | `$\check{x}$`     | $\check{x}$     | `$\dot{x}$`         | $\dot{x}$
`$\breve{x}$`      | $\breve{x}$      | `$\acute{x}$`     | $\acute{x}$     | `$\ddot{x}$`        | $\ddot{x}$
`$\grave{x}$`      | $\grave{x}$      | `$\tilde{x}$`     | $\tilde{x}$     | `$\mathring{x}$`    | $\mathring{x}$
`$\bar{x}$`        | $\bar{x}$        | `$\vec{x}$`       | $\vec{x}$       | `$\vec{\jmath}$`    | $\vec{\jmath}$
`$\tilde{\imath}$` | $\tilde{\imath}$ | `$\widehat{7+x}$` | $\widehat{7+x}$ | `$\widetilde{abc}$` | $\widetilde{abc}$

### Bracketing符号

代码            | 符号         | 代码        | 符号       | 代码        | 符号
---------------|--------------|-------------|-----------|-------------|-----
`$\{$`         | $\{$         | `$\}$`      | $\}$      | `$\rangle$` | $\rangle$
`$\backslash$` | $\backslash$ | `$\lfloor$` | $\lfloor$ | `$\rfloor$` | $\rfloor$
`$\lceil$`     | $\lceil$     | `$\rceil$`  | $\rceil$  | `$\langle$` | $\langle$

### 点符号

代码           | 符号          | 代码         | 符号
---------------|--------------|--------------|------
`$\cdot$`      | $\cdot$      | `$\vdots$`   | $\vdots$
`$\dots$`      | $\dots$      | `$\ddots$`   | $\ddots$
`$\cdots$`     | $\cdots$     | `$\because$` | $\because$
`$\therefore$` | $\therefore$ |              |

---

&emsp;&emsp;You might notice that if you use any of these to typeset an expression that is vertically large, like

``` latex
(\frac{a}{x})^2
```

the parentheses don't come out the right size:

$(\frac{a}{x})^2$

If we put `\left` and `\right` before the relevant parentheses, we get a prettier expression:

``` latex
\left(\frac{a}{x} \right)^2
```

$\left(\frac{a}{x} \right)^2$

&emsp;&emsp;if you type this:

``` latex
\underbrace{a_0+a_1+a_2+\cdots+a_n}_{x}
```

Gives

$\underbrace{a_0+a_1+a_2+\cdots+a_n}_{x}$

Or

``` latex
\overbrace{a_0+a_1+a_2+\cdots+a_n}^{x}
```

Gives

$\overbrace{a_0+a_1+a_2+\cdots+a_n}^{x}$