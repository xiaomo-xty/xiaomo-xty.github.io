---
title: markdown语法学习
date: 2021-02-19 14:21:46
tags: [目录,language,markdown,note]
---

![markdown](https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=1110695959,2322594919&fm=26&gp=0.jpg)

作为未来的一名极客，写博客是日常操作，而markdown作为一种轻量级的笔记语言，当然是我的首选。

<!--more-->



## 工具
[菜鸟工具--markdown编辑器](https://c.runoob.com/front-end/712)  
[有道云笔记--网页版](https://note.youdao.com/web)

## 语法（本人不熟悉的）

* ### 表格
#### markdown表格 

```markdown
|表头1|表头2|表头3|
|:-|:-:|-:|
|左对齐|居中对齐|右对齐|
```
| 表头1      |      表头2      |             表头3 |
| :--------- | :-------------: | ----------------: |
| 左对齐     |    居中对齐     |            右对齐 |
| dfsfsafasf | dsfsfasdfadsdfa | fdasfdsafasfasfsa |

---
#### html表格  

<table>
    <tr>
        <th>表头一</th>
        <th>表头二</th>
        <th>表头三</th>
    </tr>
    <tr>
        <td>表头一</td>
        <td>表头二</td>
        <td>表头三</td>
    </tr>
</table>  

上代码：
```html
<table>
    <tr>
        <th>表头一</th>
        <th>表头二</th>
        <th>表头三</th>
    </tr>
    <tr>
        <td>表头一</td>
        <td>表头二</td>
        <td>表头三</td>
    </tr>
</table>
```

----

* ### 图表  
#### 流程图  

```mermaid
graph TB
A[方形A] --> B[方形B]
B --> C{菱形C}
C -->|线中注释1| D((圆形D))
C -->|线中注释2| F(圆角F)
C --> G>旗帜G]
F -.字.-> S((虚线))
F ==>|粗线| S
F ---|开放线| S
F ===|开放粗线| S
F --细线2--> S
F ==粗线2==> S

```
说明：
* graph关键字 标示流程图方向   

| 标示符 |     含义      |
| :----: | :-----------: |
|   T    |  "top" 上面   |
|   B    | "bottom" 下面 |
|   L    |  "left" 左边  |
|   R    | "right" 右边  |
则 TB 即 Top to Bottom==：从上至下


* 节点形状
大写字母表示节点，name表示节点的名字，主要形状如下：

| 节点类型 |   符号   |
| :------: | :------: |
|   矩形   |  [name]  |
|   菱形   |  {name}  |
|   圆角   |  (name)  |
|   圆形   | ((name)) |
|   标签   |  >name]  |

* 连线  

>箭头连接 A1–>B1

>开放连接 A2—B2

>标签连接 A3-- text —B3 或者 A3—|text|B3

>箭头标签连接 A4–text–>B4 或者 A4–>|text|B4

>虚线开放连接 A5.-B5 或者 A5-.-B5 或者 A5…-B5

>虚线箭头连接 A6.->B6 或者 A6-.->B6

>标签虚线连接 A7-.text.-B7

>标签虚线箭头连接 A8-.text.->B8

>粗线开放连接 A9===B9

>粗线箭头连接 A10==>B10

>标签粗线开放连接 A11==text===B11

>标签粗线箭头连接A12==text==>B12

* 基础fontawesome支持  
暂不讨论

---

#### gantt

```mermaid
gantt
dateFormat YYYY-MM-DD
title 甘特图练习
section A
taskA1: crit, 2019-03-01 ,18d
taskA2: done,2019-03-01 ,30d
section B
taskB1: active, 2019-05-06,12d
taskB2: active,2019-06-21,21d

```

gantt图结构：  
1. ==dateFormat==: 指定日期格式
2. ==title==: 指定标题
3. ==section==: 项目
4. ==task-name==: 任务名，后跟三项参数[^1]     

第一项：任务标识  



|  标识   |    含义    |
| :----: | :--------: |
|  done  |    已做    |
|  crit  |    重点    |
| active |  正在进行  |
|  (无)   | 默认待完成 |

第二项：日期
1. 绝对日期：具体的年月日
2. 相对日期：使用语法 "after" 接任务名

第三项：持续时间
n(y,m,d,h)

- [x] 待办
- [ ] jnn



###  数学公式LaTex

## **1. 样式系列**

### **1.1. 换行`\\`、空格`\:`**

```
$换行\\萌萌哒\:小明$
```



![img](https://www.zhihu.com/equation?tex=%E6%8D%A2%E8%A1%8C%5C%5C%E8%90%8C%E8%90%8C%E5%93%92%5C%3A%E5%B0%8F%E6%98%8E)



### **1.2. 居中`$$\**$$`**

```
$$萌萌哒$$
```



![img](https://www.zhihu.com/equation?tex=%E8%90%8C%E8%90%8C%E5%93%92+%5C%5C)



### **1.3. 加粗（mathbf）、斜体（mathit）**

```
$\mathbf{萌萌哒}$

$\mathit{小明}$
```



![img](https://www.zhihu.com/equation?tex=%5Cmathbf%7B%E8%90%8C%E8%90%8C%E5%93%92%7D)





![img](https://www.zhihu.com/equation?tex=%5Cmathit%7B%E5%B0%8F%E6%98%8E%7D)



### **1.4. 大小**

```
$\tiny 萌萌哒$

$\scriptsize 萌萌哒$

$\small 萌萌哒$

$\normalsize 萌萌哒(正常)$

$\large 萌萌哒$

$\Large 萌萌哒$

$\huge 萌萌哒$

$\Huge 萌萌哒$
```



![img](https://www.zhihu.com/equation?tex=%5Ctiny+%E8%90%8C%E8%90%8C%E5%93%92)





![img](https://www.zhihu.com/equation?tex=%5Cscriptsize+%E8%90%8C%E8%90%8C%E5%93%92)





![img](https://www.zhihu.com/equation?tex=%5Csmall+%E8%90%8C%E8%90%8C%E5%93%92)





![img](https://www.zhihu.com/equation?tex=%5Cnormalsize+%E8%90%8C%E8%90%8C%E5%93%92%28%E6%AD%A3%E5%B8%B8%29)





![img](https://www.zhihu.com/equation?tex=%5Clarge+%E8%90%8C%E8%90%8C%E5%93%92)





![img](https://www.zhihu.com/equation?tex=%5CLarge+%E8%90%8C%E8%90%8C%E5%93%92)





![img](https://www.zhihu.com/equation?tex=%5Chuge+%E8%90%8C%E8%90%8C%E5%93%92)





![img](https://www.zhihu.com/equation?tex=%5CHuge+%E8%90%8C%E8%90%8C%E5%93%92)



如果是单行写，记得加换行符号：

```
$\tiny 萌萌哒\\$
$\scriptsize 萌萌哒\\$
$\small 萌萌哒\\$
$\normalsize 萌萌哒(正常)\\$
$\large 萌萌哒\\$
$\Large 萌萌哒\\$
$\huge 萌萌哒\\$
$\Huge 萌萌哒\\$
```

### **1.5. 颜色（有些编辑器不支持）**

```
$\color{Red}{Red}$, $\color{blue}{Blue}$,  $\color{orange}{Orange}$,  
$\color{Green}{Green}$,  $\color{gray}{Gray}$,  $\color{purple}{Purple}$.
```



![img](https://www.zhihu.com/equation?tex=%5Ccolor%7BRed%7D%7BRed%7D)

, ,

![img](https://www.zhihu.com/equation?tex=%5Ccolor%7Borange%7D%7BOrange%7D)

,
,

![img](https://www.zhihu.com/equation?tex=%5Ccolor%7Bgray%7D%7BGray%7D)

, .

## **2. 常用数学**

### **2.1. 常用表达式**

**常用数学 LaTex 公式**

![img](https://www.zhihu.com/equation?tex=%5Csqrt%7Bab%7D)

```
$\sqrt{ab}$
```

![img](https://www.zhihu.com/equation?tex=%5Csqrt%5Bn%5D%7Bab%7D)

```
$\sqrt[n]{ab}$
```

![img](https://www.zhihu.com/equation?tex=%5Clog_%7Ba%7D%7Bb%7D)

```
$\log_{a}{b}$
```

![img](https://www.zhihu.com/equation?tex=%5Clg%7Bab%7D)

```
$\lg{ab}$
```

![img](https://www.zhihu.com/equation?tex=a%5E%7Bb%7D)

```
$a^{b}$
```

![img](https://www.zhihu.com/equation?tex=a_%7Bb%7D)

```
$a_{b}$
```

![img](https://www.zhihu.com/equation?tex=x_a%5Eb)

```
$x_a^b$
```

![img](https://www.zhihu.com/equation?tex=%5Cint)

```
$\int$
```

![img](https://www.zhihu.com/equation?tex=%5Cint_%7Ba%7D%5E%7Bb%7D)

```
$\int_{a}^{b}$
```

![img](https://www.zhihu.com/equation?tex=%5Coint)

```
$\oint$
```

![img](https://www.zhihu.com/equation?tex=%5Coint_a%5Eb)

```
$\oint_a^b$
```

![img](https://www.zhihu.com/equation?tex=%5Csum)

```
$\sum$
```

![img](https://www.zhihu.com/equation?tex=%5Csum_a%5Eb)

```
$\sum_a^b$
```

![img](https://www.zhihu.com/equation?tex=%5Ccoprod)

```
$\coprod$
```

![img](https://www.zhihu.com/equation?tex=%5Ccoprod_a%5Eb)

```
$\coprod_a^b$
```

![img](https://www.zhihu.com/equation?tex=%5Cprod)

```
$\prod$
```

![img](https://www.zhihu.com/equation?tex=%5Cprod_a%5Eb)

```
$\prod_a^b$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigcap)

```
$\bigcap$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigcap_a%5Eb)

```
$\bigcap_a^b$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigcup)

```
$\bigcup$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigcup_a%5Eb)

```
$\bigcup_a^b$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigsqcup)

```
$\bigsqcup$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigsqcup_a%5Eb)

```
$\bigsqcup_a^b$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigvee)

```
$\bigvee$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigvee_a%5Eb)

```
$\bigvee_a^b$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigwedge)

```
$\bigwedge$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigwedge_a%5Eb)

```
$\bigwedge_a^b$
```

![img](https://www.zhihu.com/equation?tex=%5Cwidetilde%7Bab%7D)

```
$\widetilde{ab}$
```

![img](https://www.zhihu.com/equation?tex=%5Cwidehat%7Bab%7D)

```
$\widehat{ab}$
```

![img](https://www.zhihu.com/equation?tex=%5Coverleftarrow%7Bab%7D)

```
$\overleftarrow{ab}$
```

![img](https://www.zhihu.com/equation?tex=%5Coverrightarrow%7Bab%7D)

```
$\overrightarrow{ab}$
```

![img](https://www.zhihu.com/equation?tex=%5Coverbrace%7Bab%7D)

```
$\overbrace{ab}$
```

![img](https://www.zhihu.com/equation?tex=%5Cunderbrace%7Bab%7D)

```
$\underbrace{ab}$
```

![img](https://www.zhihu.com/equation?tex=%5Cunderline%7Bab%7D)

```
$\underline{ab}$
```

![img](https://www.zhihu.com/equation?tex=%5Coverline%7Bab%7D)

```
$\overline{ab}$
```

![img](https://www.zhihu.com/equation?tex=%5Cfrac%7Bab%7D%7Bcd%7D)

```
$\frac{ab}{cd}$
```

![img](https://www.zhihu.com/equation?tex=%5Cfrac%7B%5Cpartial+a%7D%7B%5Cpartial+b%7D)

```
$\frac{\partial a}{\partial b}$
```

![img](https://www.zhihu.com/equation?tex=%5Cfrac%7B%5Ctext%7Bd%7Dx%7D%7B%5Ctext%7Bd%7Dy%7D)

```
$\frac{\text{d}x}{\text{d}y}$
```

![img](https://www.zhihu.com/equation?tex=%5Clim_%7Ba+%5Crightarrow+b%7D)

```
$\lim_{a \rightarrow b}$
```



### **2.2. 附录：数学公式大全**

**数学公式 LaTex 公式**

![img](https://www.zhihu.com/equation?tex=%5Cdisplaystyle%5Csum%5Climits_%7Bi%3D0%7D%5En+i%5E3)

```
$\displaystyle\sum\limits_{i=0}^n i^3$
```

![img](https://www.zhihu.com/equation?tex=%5Cleft%28%5Cbegin%7Barray%7D%7Bc%7Da%5C%5C+b%5Cend%7Barray%7D%5Cright%29)

```
$\left(\begin{array}{c}a\\ b\end{array}\right)$
```

![img](https://www.zhihu.com/equation?tex=%5Cleft%28%5Cfrac%7Ba%5E2%7D%7Bb%5E3%7D%5Cright%29)

```
$\left(\frac{a^2}{b^3}\right)$
```

![img](https://www.zhihu.com/equation?tex=%5Cleft.%5Cfrac%7Ba%5E3%7D%7B3%7D%5Cright%5Clvert_0%5E1)

```
$\left.\frac{a^3}{3}\right\lvert_0^1$
```

![img](https://www.zhihu.com/equation?tex=%5Cbegin%7Bbmatrix%7Da+%26+b+%5C%5Cc+%26+d+%5Cend%7Bbmatrix%7D)

```
$\begin{bmatrix}a & b \\c & d \end{bmatrix}$
```

![img](https://www.zhihu.com/equation?tex=%5Cbegin%7Bcases%7Da+%26+x+%3D+0%5C%5Cb+%26+x+%3E+0%5Cend%7Bcases%7D)

```
$\begin{cases}a & x = 0\\b & x > 0\end{cases}$
```

![img](https://www.zhihu.com/equation?tex=%5Csqrt%7B%5Cfrac%7Bn%7D%7Bn-1%7D+S%7D)

```
$\sqrt{\frac{n}{n-1} S}$
```

![img](https://www.zhihu.com/equation?tex=%5Cbegin%7Bpmatrix%7D+%5Calpha%26+%5Cbeta%5E%7B%2A%7D%5C%5C+%5Cgamma%5E%7B%2A%7D%26+%5Cdelta+%5Cend%7Bpmatrix%7D)

```
$\begin{pmatrix} \alpha& \beta^{*}\\ \gamma^{*}& \delta \end{pmatrix}$
```

![img](https://www.zhihu.com/equation?tex=A%5C%3A%5Cxleftarrow%7Bn%2B%5Cmu-1%7D%5C%3AB)

```
$A\:\xleftarrow{n+\mu-1}\:B$
```

![img](https://www.zhihu.com/equation?tex=B%5C%3A%5Cxrightarrow%5BT%5D%7Bn%5Cpm+i-1%7D%5C%3AC)

```
$B\:\xrightarrow[T]{n\pm i-1}\:C$
```

![img](https://www.zhihu.com/equation?tex=%5Cfrac%7B1%7D%7Bk%7D%5Clog_2+c%28f%29%5C%3B)

```
$\frac{1}{k}\log_2 c(f)\;$
```

![img](https://www.zhihu.com/equation?tex=%5Ciint%5Climits_A+f%28x%2Cy%29%5C%3B)

```
$\iint\limits_A f(x,y)\;$
```

![img](https://www.zhihu.com/equation?tex=x%5En+%2B+y%5En+%3D+z%5En)

```
$x^n + y^n = z^n$
```

![img](https://www.zhihu.com/equation?tex=E%3Dmc%5E2)

```
$E=mc^2$
```

![img](https://www.zhihu.com/equation?tex=e%5E%7B%5Cpi+i%7D+-+1+%3D+0)

```
$e^{\pi i} - 1 = 0$
```

![img](https://www.zhihu.com/equation?tex=p%28x%29+%3D+3x%5E6)

```
$p(x) = 3x^6$
```

![img](https://www.zhihu.com/equation?tex=3x+%2B+y+%3D+12)

```
$3x + y = 12$
```

![img](https://www.zhihu.com/equation?tex=%5Cint_0%5E%5Cinfty+%5Cmathrm%7Be%7D%5E%7B-x%7D%5C%2C%5Cmathrm%7Bd%7Dx)

```
$\int_0^\infty \mathrm{e}^{-x}\,\mathrm{d}x$
```

![img](https://www.zhihu.com/equation?tex=%5Csqrt%5Bn%5D%7B1%2Bx%2Bx%5E2%2B%5Cldots%7D)

```
$\sqrt[n]{1+x+x^2+\ldots}$
```

![img](https://www.zhihu.com/equation?tex=%5Cbinom%7Bx%7D%7By%7D+%3D+%5Cfrac%7Bx%21%7D%7By%21%28x-y%29%21%7D)

```
$\binom{x}{y} = \frac{x!}{y!(x-y)!}$
```

![img](https://www.zhihu.com/equation?tex=%5Cfrac%7B%5Cfrac%7B1%7D%7Bx%7D%2B%5Cfrac%7B1%7D%7By%7D%7D%7By-z%7D)

```
$\frac{\frac{1}{x}+\frac{1}{y}}{y-z}$
```

![img](https://www.zhihu.com/equation?tex=f%28x%29%3D%5Cfrac%7BP%28x%29%7D%7BQ%28x%29%7D)

```
$f(x)=\frac{P(x)}{Q(x)}$
```

![img](https://www.zhihu.com/equation?tex=%5Cfrac%7B1%2B%5Cfrac%7Ba%7D%7Bb%7D%7D%7B1%2B%5Cfrac%7B1%7D%7B1%2B%5Cfrac%7B1%7D%7Ba%7D%7D%7D)

```
$\frac{1+\frac{a}{b}}{1+\frac{1}{1+\frac{1}{a}}}$
```

![img](https://www.zhihu.com/equation?tex=%5Csum_%7B%5Csubstack%7B0%5Cle+i%5Cle+m%5C%5C+0%5Clt+j%5Clt+n%7D%7D+P%28i%2Cj%29)

```
$\sum_{\substack{0\le i\le m\\ 0\lt j\lt n}} P(i,j)$
```

![img](https://www.zhihu.com/equation?tex=%5Clim_%7Bx+%5Cto+%5Cinfty%7D+%5Cexp%28-x%29+%3D+0)

```
$\lim_{x \to \infty} \exp(-x) = 0$
```

![img](https://www.zhihu.com/equation?tex=%5Ccos+%282%5Ctheta%29+%3D+%5Ccos%5E2+%5Ctheta+-+%5Csin%5E2+%5Ctheta)

```
$\cos (2\theta) = \cos^2 \theta - \sin^2 \theta$
```



## **3. 数学符号**

### **3.1. 集合系列**

**运算符公式运算符公式运算符公式**

![img](https://www.zhihu.com/equation?tex=%5Cemptyset)

```
$\emptyset$
```

![img](https://www.zhihu.com/equation?tex=%5Cin)

```
$\in$
```

![img](https://www.zhihu.com/equation?tex=%5Cnotin)

```
$\notin$
```

![img](https://www.zhihu.com/equation?tex=%5Csubset)

```
$\subset$
```

![img](https://www.zhihu.com/equation?tex=%5Csupset)

```
$\supset$
```

![img](https://www.zhihu.com/equation?tex=%5Csubseteq)

```
$\subseteq$
```

![img](https://www.zhihu.com/equation?tex=%5Cnsubseteq)

```
$\nsubseteq$
```

![img](https://www.zhihu.com/equation?tex=%5Cnsupseteq)

```
$\nsupseteq$
```

![img](https://www.zhihu.com/equation?tex=%5Cnsubseteqq)

```
$\nsubseteqq$
```

![img](https://www.zhihu.com/equation?tex=%5Cnsupseteqq)

```
$\nsupseteqq$
```

![img](https://www.zhihu.com/equation?tex=%5Csubsetneq)

```
$\subsetneq$
```

![img](https://www.zhihu.com/equation?tex=%5Csupsetneq)

```
$\supsetneq$
```

![img](https://www.zhihu.com/equation?tex=%5Csubsetneqq)

```
$\subsetneqq$
```

![img](https://www.zhihu.com/equation?tex=%5Csupsetneqq)

```
$\supsetneqq$
```

![img](https://www.zhihu.com/equation?tex=%5Cvarsubsetneq)

```
$\varsubsetneq$
```

![img](https://www.zhihu.com/equation?tex=%5Cvarsupsetneq)

```
$\varsupsetneq$
```

![img](https://www.zhihu.com/equation?tex=%5Cvarsubsetneqq)

```
$\varsubsetneqq$
```

![img](https://www.zhihu.com/equation?tex=%5Cvarsupsetneqq)

```
$\varsupsetneqq$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigcap)

```
$\bigcap$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigcup)

```
$\bigcup$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigvee)

```
$\bigvee$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigwedge)

```
$\bigwedge$
```

![img](https://www.zhihu.com/equation?tex=%5Cbiguplus)

```
$\biguplus$
```

![img](https://www.zhihu.com/equation?tex=%5Cbigsqcup)

```
$\bigsqcup$
```

![img](https://www.zhihu.com/equation?tex=%5CSubset)

```
$\Subset$
```

![img](https://www.zhihu.com/equation?tex=%5CSupset)

```
$\Supset$
```

![img](https://www.zhihu.com/equation?tex=%5Csubseteqq)

```
$\subseteqq$
```

![img](https://www.zhihu.com/equation?tex=%5Csupseteqq)

```
$\supseteqq$
```

![img](https://www.zhihu.com/equation?tex=%5Csqsubset)

```
$\sqsubset$
```

![img](https://www.zhihu.com/equation?tex=%5Csqsupset)

```
$\sqsupset$
```



### **3.2. 常用符号**

**基本符号公式基本符号公式基本符号公式**

![img](https://www.zhihu.com/equation?tex=%5Ccdot)

```
$\cdot$
```

![img](https://www.zhihu.com/equation?tex=%5Cvdots)

```
$\vdots$
```

![img](https://www.zhihu.com/equation?tex=%5Cgrave%7Bx%7D)

```
$\grave{x}$
```

![img](https://www.zhihu.com/equation?tex=.)

```
$.$
```

![img](https://www.zhihu.com/equation?tex=%5Cddots)

```
$\ddots$
```

![img](https://www.zhihu.com/equation?tex=%5Cbreve%7Bx%7D)

```
$\breve{x}$
```

![img](https://www.zhihu.com/equation?tex=%2A)

```
$*$
```

![img](https://www.zhihu.com/equation?tex=%2C)

```
$,$
```

![img](https://www.zhihu.com/equation?tex=%5Cdot%7Bx%7D)

```
$\dot{x}$
```

![img](https://www.zhihu.com/equation?tex=%2B)

```
$+$
```

![img](https://www.zhihu.com/equation?tex=%21)

```
$!$
```

![img](https://www.zhihu.com/equation?tex=%5Cwidehat%7Bxxx%7D)

```
$\widehat{xxx}$
```

![img](https://www.zhihu.com/equation?tex=-)

```
$-$
```

![img](https://www.zhihu.com/equation?tex=%3B)

```
$;$
```

![img](https://www.zhihu.com/equation?tex=%5Cddot%7Bx%7D)

```
$\ddot{x}$
```

![img](https://www.zhihu.com/equation?tex=%5Ctimes)

```
$\times$
```

![img](https://www.zhihu.com/equation?tex=%3F)

```
$?$
```

![img](https://www.zhihu.com/equation?tex=%5Ccheck%7Bx%7D)

```
$\check{x}$
```

![img](https://www.zhihu.com/equation?tex=%5Cdiv)

```
$\div$
```

![img](https://www.zhihu.com/equation?tex=%5Ccolon)

```
$\colon$
```

![img](https://www.zhihu.com/equation?tex=%5Cddot%7Bx%7D)

```
$\ddot{x}$
```

![img](https://www.zhihu.com/equation?tex=%3D)

```
$=$
```

![img](https://www.zhihu.com/equation?tex=%5Cacute%7Bx%7D)

```
$\acute{x}$
```

![img](https://www.zhihu.com/equation?tex=%5Ctilde%7Bx%7D)

```
$\tilde{x}$
```

![img](https://www.zhihu.com/equation?tex=%5Cneq)

```
$\neq$
```

![img](https://www.zhihu.com/equation?tex=%5Cbar%7Bx%7D)

```
$\bar{x}$
```

![img](https://www.zhihu.com/equation?tex=%5Chat%7Bx%7D)

```
$\hat{x}$
```

![img](https://www.zhihu.com/equation?tex=%5Cdotsm)

```
$\dotsm$
```

![img](https://www.zhihu.com/equation?tex=%5Cvec%7Bx%7D)

```
$\vec{x}$
```

![img](https://www.zhihu.com/equation?tex=%5Cdddot%7Bx%7D)

```
$\dddot{x}$
```

![img](https://www.zhihu.com/equation?tex=%5Cdotso)

```
$\dotso$
```

![img](https://www.zhihu.com/equation?tex=%5Cwidetilde%7Bxxx%7D)

```
$\widetilde{xxx}$
```

![img](https://www.zhihu.com/equation?tex=%5Cbackslash)

```
$\backslash$
```

![img](https://www.zhihu.com/equation?tex=%2F)

```
$/$
```

![img](https://www.zhihu.com/equation?tex=%5Cbracevert)

```
$\bracevert$
```

![img](https://www.zhihu.com/equation?tex=%5D)

```
$]$
```

![img](https://www.zhihu.com/equation?tex=%5Csmallsetminus)

```
$\smallsetminus$
```

![img](https://www.zhihu.com/equation?tex=%5ClVert)

```
$\lVert$
```

![img](https://www.zhihu.com/equation?tex=%5Clbrace)

```
$\lbrace$
```

![img](https://www.zhihu.com/equation?tex=%5Carrowvert)

```
$\arrowvert$
```

![img](https://www.zhihu.com/equation?tex=%5CrVert)

```
$\rVert$
```

![img](https://www.zhihu.com/equation?tex=%5Crbrace)

```
$\rbrace$
```

![img](https://www.zhihu.com/equation?tex=%5Clvert)

![img](https://www.zhihu.com/equation?tex=%5Clvert)

![img](https://www.zhihu.com/equation?tex=%5Clgroup)

```
$\lgroup$
```

![img](https://www.zhihu.com/equation?tex=%5Clangle)

```
$\langle$
```

![img](https://www.zhihu.com/equation?tex=%5Clvert)

```
$\lvert$
```

![img](https://www.zhihu.com/equation?tex=%5Crgroup)

```
$\rgroup$
```

![img](https://www.zhihu.com/equation?tex=%5Crangle)

```
$\rangle$
```

![img](https://www.zhihu.com/equation?tex=%5Crvert)

```
$\rvert$
```

![img](https://www.zhihu.com/equation?tex=%5B)

```
$[$
```

![img](https://www.zhihu.com/equation?tex=%5Clmoustache)

```
$\lmoustache$
```

![img](https://www.zhihu.com/equation?tex=%5Crmoustache)

```
$\rmoustache$
```

![img](https://www.zhihu.com/equation?tex=%5Clceil)

```
$\lceil$
```

![img](https://www.zhihu.com/equation?tex=%5Crceil)

```
$\rceil$
```

![img](https://www.zhihu.com/equation?tex=%5Clfloor)

```
$\lfloor$
```

![img](https://www.zhihu.com/equation?tex=%5Crfloor)

```
$\rfloor$
```



### **3.3. 希腊字母表**

**字母公式字母公式字母公式**

![img](https://www.zhihu.com/equation?tex=%5Calpha)

```
$\alpha$
```

![img](https://www.zhihu.com/equation?tex=%5Cbeta)

```
$\beta$
```

![img](https://www.zhihu.com/equation?tex=%5Cchi)

```
$\chi$
```

![img](https://www.zhihu.com/equation?tex=%5Cdelta)

```
$\delta$
```

![img](https://www.zhihu.com/equation?tex=%5CDelta)

```
$\Delta$
```

![img](https://www.zhihu.com/equation?tex=%5Cepsilon)

```
$\epsilon$
```

![img](https://www.zhihu.com/equation?tex=%5Ceta)

```
$\eta$
```

![img](https://www.zhihu.com/equation?tex=%5CGamma)

```
$\Gamma$
```

![img](https://www.zhihu.com/equation?tex=%5Ciota)

```
$\iota$
```

![img](https://www.zhihu.com/equation?tex=%5Ckappa)

```
$\kappa$
```

![img](https://www.zhihu.com/equation?tex=%5Clambda)

```
$\lambda$
```

![img](https://www.zhihu.com/equation?tex=%5CLambda)

```
$\Lambda$
```

![img](https://www.zhihu.com/equation?tex=%5Cmu)

```
$\mu$
```

![img](https://www.zhihu.com/equation?tex=%5Cnabla)

```
$\nabla$
```

![img](https://www.zhihu.com/equation?tex=%5Cnu)

```
$\nu$
```

![img](https://www.zhihu.com/equation?tex=%5Comega)

```
$\omega$
```

![img](https://www.zhihu.com/equation?tex=%5COmega)

```
$\Omega$
```

![img](https://www.zhihu.com/equation?tex=%5Cphi)

```
$\phi$
```

![img](https://www.zhihu.com/equation?tex=%5CPhi)

```
$\Phi$
```

![img](https://www.zhihu.com/equation?tex=%5Cpi)

```
$\pi$
```

![img](https://www.zhihu.com/equation?tex=%5CPi)

```
$\Pi$
```

![img](https://www.zhihu.com/equation?tex=%5Cpsi)

```
$\psi$
```

![img](https://www.zhihu.com/equation?tex=%5CPsi)

```
$\Psi$
```

![img](https://www.zhihu.com/equation?tex=%5Crho)

```
$\rho$
```

![img](https://www.zhihu.com/equation?tex=%5Csigma)

```
$\sigma$
```

![img](https://www.zhihu.com/equation?tex=%5CSigma)

```
$\Sigma$
```

![img](https://www.zhihu.com/equation?tex=%5Ctau)

```
$\tau$
```

![img](https://www.zhihu.com/equation?tex=%5Ctheta)

```
$\theta$
```

![img](https://www.zhihu.com/equation?tex=%5CTheta)

```
$\Theta$
```

![img](https://www.zhihu.com/equation?tex=%5Cupsilon)

```
$\upsilon$
```

![img](https://www.zhihu.com/equation?tex=%5Cvarepsilon)

```
$\varepsilon$
```

![img](https://www.zhihu.com/equation?tex=%5Cvarsigma)

```
$\varsigma$
```

![img](https://www.zhihu.com/equation?tex=%5Cvartheta)

```
$\vartheta$
```

![img](https://www.zhihu.com/equation?tex=%5Cxi)

```
$\xi$
```

![img](https://www.zhihu.com/equation?tex=%5Czeta)

```
$\zeta$
```



### **3.4. 函数公式表**

**函数公式函数公式函数公式**

![img](https://www.zhihu.com/equation?tex=%5Csin)

```
$\sin$
```

![img](https://www.zhihu.com/equation?tex=%5Csin%5E%7B-1%7D)

```
$\sin^{-1}$
```

![img](https://www.zhihu.com/equation?tex=%5Cinf)

```
$\inf$
```

![img](https://www.zhihu.com/equation?tex=%5Ccos)

```
$\cos$
```

![img](https://www.zhihu.com/equation?tex=%5Ccos%5E%7B-1%7D)

```
$\cos^{-1}$
```

![img](https://www.zhihu.com/equation?tex=%5Carg)

```
$\arg$
```

![img](https://www.zhihu.com/equation?tex=%5Ctan)

```
$\tan$
```

![img](https://www.zhihu.com/equation?tex=%5Ctan%5E%7B-1%7D)

```
$\tan^{-1}$
```

![img](https://www.zhihu.com/equation?tex=%5Cdet)

```
$\det$
```

![img](https://www.zhihu.com/equation?tex=%5Csinh)

```
$\sinh$
```

![img](https://www.zhihu.com/equation?tex=%5Csinh%5E%7B-1%7D)

```
$\sinh^{-1}$
```

![img](https://www.zhihu.com/equation?tex=%5Cdim)

```
$\dim$
```

![img](https://www.zhihu.com/equation?tex=%5Ccosh)

```
$\cosh$
```

![img](https://www.zhihu.com/equation?tex=%5Ccosh%5E%7B-1%7D)

```
$\cosh^{-1}$
```

![img](https://www.zhihu.com/equation?tex=%5Cgcd)

```
$\gcd$
```

![img](https://www.zhihu.com/equation?tex=%5Ctanh)

```
$\tanh$
```

![img](https://www.zhihu.com/equation?tex=%5Ctanh%5E%7B-1%7D)

```
$\tanh^{-1}$
```

![img](https://www.zhihu.com/equation?tex=%5Chom)

```
$\hom$
```

![img](https://www.zhihu.com/equation?tex=%5Ccsc)

```
$\csc$
```

![img](https://www.zhihu.com/equation?tex=%5Cexp)

```
$\exp$
```

![img](https://www.zhihu.com/equation?tex=%5Cker)

```
$\ker$
```

![img](https://www.zhihu.com/equation?tex=%5Csec)

```
$\sec$
```

![img](https://www.zhihu.com/equation?tex=%5Clg)

```
$\lg$
```

![img](https://www.zhihu.com/equation?tex=%5CPr)

```
$\Pr$
```

![img](https://www.zhihu.com/equation?tex=%5Ccot)

```
$\cot$
```

![img](https://www.zhihu.com/equation?tex=%5Cln)

```
$\ln$
```

![img](https://www.zhihu.com/equation?tex=%5Csup)

```
$\sup$
```

![img](https://www.zhihu.com/equation?tex=%5Ccoth)

```
$\coth$
```

![img](https://www.zhihu.com/equation?tex=%5Clog)

```
$\log$
```

![img](https://www.zhihu.com/equation?tex=%5Cdeg)

```
$\deg$
```

![img](https://www.zhihu.com/equation?tex=%5Chom)

```
$\hom$
```

![img](https://www.zhihu.com/equation?tex=%5Clog_%7Be%7D)

```
$\log_{e}$
```

![img](https://www.zhihu.com/equation?tex=%5Cinjlim)

```
$\injlim$
```

![img](https://www.zhihu.com/equation?tex=%5Carcsin)

```
$\arcsin$
```

![img](https://www.zhihu.com/equation?tex=%5Clog_%7B10%7D)

```
$\log_{10}$
```

![img](https://www.zhihu.com/equation?tex=%5Cvarinjlim)

```
$\varinjlim$
```

![img](https://www.zhihu.com/equation?tex=%5Carccos)

```
$\arccos$
```

![img](https://www.zhihu.com/equation?tex=%5Clim)

```
$\lim$
```

![img](https://www.zhihu.com/equation?tex=%5Cvarprojlim)

```
$\varprojlim$
```

![img](https://www.zhihu.com/equation?tex=%5Cdet)

```
$\det$
```

![img](https://www.zhihu.com/equation?tex=%5Climinf)

```
$\liminf$
```

![img](https://www.zhihu.com/equation?tex=%5Cvarliminf)

```
$\varliminf$
```

![img](https://www.zhihu.com/equation?tex=%5Carctan)

```
$\arctan$
```

![img](https://www.zhihu.com/equation?tex=%5Climsup)

```
$\limsup$
```

![img](https://www.zhihu.com/equation?tex=%5Cprojlim)

```
$\projlim$
```

![img](https://www.zhihu.com/equation?tex=%5Ctextrm%7Barccsc%7D)

```
$\textrm{arccsc}$
```

![img](https://www.zhihu.com/equation?tex=%5Cmax)

```
$\max$
```

![img](https://www.zhihu.com/equation?tex=%5Cvarlimsup)

```
$\varlimsup$
```

![img](https://www.zhihu.com/equation?tex=%5Ctextrm%7Barcsec%7D)

```
$\textrm{arcsec}$
```

![img](https://www.zhihu.com/equation?tex=%5Cmin)

```
$\min$
```

![img](https://www.zhihu.com/equation?tex=%5Ctextrm%7Barccot%7D)

```
$\textrm{arccot}$
```

![img](https://www.zhihu.com/equation?tex=%5Cinfty)

```
$\infty$
```



### **3.5. 特殊符号 - 箭头系列**

**箭头公式箭头公式箭头公式**

![img](https://www.zhihu.com/equation?tex=%5Cuparrow)

```
$\uparrow$
```

![img](https://www.zhihu.com/equation?tex=%5Clongleftarrow)

```
$\longleftarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cdowndownarrows)

```
$\downdownarrows$
```

![img](https://www.zhihu.com/equation?tex=%5Cdownarrow)

```
$\downarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Clongrightarrow)

```
$\longrightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cupuparrows)

```
$\upuparrows$
```

![img](https://www.zhihu.com/equation?tex=%5Cupdownarrow)

```
$\updownarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Crightarrow)

```
$\rightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Crightharpoondown)

```
$\rightharpoondown$
```

![img](https://www.zhihu.com/equation?tex=%5CUparrow)

```
$\Uparrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cleftarrow)

```
$\leftarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cdownharpoonleft)

```
$\downharpoonleft$
```

![img](https://www.zhihu.com/equation?tex=%5CDownarrow)

```
$\Downarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cmapsto)

```
$\mapsto$
```

![img](https://www.zhihu.com/equation?tex=%5Crightharpoonup)

```
$\rightharpoonup$
```

![img](https://www.zhihu.com/equation?tex=%5CLeftarrow)

```
$\Leftarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cnrightarrow)

```
$\nrightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cdownharpoonright)

```
$\downharpoonright$
```

![img](https://www.zhihu.com/equation?tex=%5CRightarrow)

```
$\Rightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cnleftarrow)

```
$\nleftarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cupharpoonleft)

```
$\upharpoonleft$
```

![img](https://www.zhihu.com/equation?tex=%5CLeftrightarrow)

```
$\Leftrightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Crightrightarrows)

```
$\rightrightarrows$
```

![img](https://www.zhihu.com/equation?tex=%5Cupharpoonright)

```
$\upharpoonright$
```

![img](https://www.zhihu.com/equation?tex=%5CnLeftrightarrow)

```
$\nLeftrightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cleftleftarrows)

```
$\leftleftarrows$
```

![img](https://www.zhihu.com/equation?tex=%5Cleftharpoondown)

```
$\leftharpoondown$
```

![img](https://www.zhihu.com/equation?tex=%5CnLeftarrow)

```
$\nLeftarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Crightleftarrows)

```
$\rightleftarrows$
```

![img](https://www.zhihu.com/equation?tex=%5Cleftharpoonup)

```
$\leftharpoonup$
```

![img](https://www.zhihu.com/equation?tex=%5CnRightarrow)

```
$\nRightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cleftrightarrows)

```
$\leftrightarrows$
```

![img](https://www.zhihu.com/equation?tex=%5Chookleftarrow)

```
$\hookleftarrow$
```

![img](https://www.zhihu.com/equation?tex=%5CUpdownarrow)

```
$\Updownarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Ccurvearrowleft)

```
$\curvearrowleft$
```

![img](https://www.zhihu.com/equation?tex=%5Chookrightarrow)

```
$\hookrightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Ccirclearrowleft)

```
$\circlearrowleft$
```

![img](https://www.zhihu.com/equation?tex=%5Ccurvearrowright)

```
$\curvearrowright$
```

![img](https://www.zhihu.com/equation?tex=%5Crightleftharpoons)

```
$\rightleftharpoons$
```

![img](https://www.zhihu.com/equation?tex=%5Ccirclearrowright)

```
$\circlearrowright$
```

![img](https://www.zhihu.com/equation?tex=%5CLongleftarrow)

```
$\Longleftarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cleftrightharpoons)

```
$\leftrightharpoons$
```

![img](https://www.zhihu.com/equation?tex=%5CLleftarrow)

```
$\Lleftarrow$
```

![img](https://www.zhihu.com/equation?tex=%5CLongrightarrow)

```
$\Longrightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Clooparrowleft)

```
$\looparrowleft$
```

![img](https://www.zhihu.com/equation?tex=%5CRrightarrow)

```
$\Rrightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Clongleftrightarrow)

```
$\longleftrightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Clooparrowright)

```
$\looparrowright$
```

![img](https://www.zhihu.com/equation?tex=%5Cnwarrow)

```
$\nwarrow$
```

![img](https://www.zhihu.com/equation?tex=%5CLongleftrightarrow)

```
$\Longleftrightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Crightsquigarrow)

```
$\rightsquigarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cswarrow)

```
$\swarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Clongmapsto)

```
$\longmapsto$
```

![img](https://www.zhihu.com/equation?tex=%5CLsh)

```
$\Lsh$
```

![img](https://www.zhihu.com/equation?tex=%5Csearrow)

```
$\searrow$
```

![img](https://www.zhihu.com/equation?tex=%5Crightarrowtail)

```
$\rightarrowtail$
```

![img](https://www.zhihu.com/equation?tex=%5CRsh)

```
$\Rsh$
```

![img](https://www.zhihu.com/equation?tex=%5Cnearrow)

```
$\nearrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cleftarrowtail)

```
$\leftarrowtail$
```

![img](https://www.zhihu.com/equation?tex=%5Cmultimap)

```
$\multimap$
```

![img](https://www.zhihu.com/equation?tex=%5Ctwoheadleftarrow)

```
$\twoheadleftarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Ctwoheadrightarrow)

```
$\twoheadrightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cleftrightsquigarrow)

```
$\leftrightsquigarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cleftrightarrow)

```
$\leftrightarrow$
```

![img](https://www.zhihu.com/equation?tex=%5Cnleftrightarrow)

```
$\nleftrightarrow$
```



## **4. 逆天常用**

逆天用到就添加进去（不定期更新）根据上面有的，这些其实都可以自己写出来的

### **4.1. 二次方程求解**

```
$\mathbf{a*x^2+b*x+c}$

$$x={\frac{-b \pm \sqrt{b^2-4ac}}{2a}}$$ or $$x = {-b \pm \sqrt{b^2-4ac} \over 2a}$$
```



![img](https://www.zhihu.com/equation?tex=%5Cmathbf%7Ba%2Ax%5E2%2Bb%2Ax%2Bc%7D)





![img](https://www.zhihu.com/equation?tex=x+%3D+%7B-b+%5Cpm+%5Csqrt%7Bb%5E2-4ac%7D+%5Cover+2a%7D+%5C%5C)



### **4.2. 矩阵系列**

```
$$
\begin{bmatrix}
1&0&0 \\
0&1&0 \\
0&0&1
\end{bmatrix}
$$
```



![img](https://www.zhihu.com/equation?tex=%5Cbegin%7Bbmatrix%7D+1%260%260+%5C%5C+0%261%260+%5C%5C+0%260%261+%5Cend%7Bbmatrix%7D++%5C%5C)



### **4.3 方程组**

### **左侧花括号**

```
​```
\begin{equation}
% \begin{equation*} 加'*'去掉公式编号
\left\{
\begin{aligned}     %请使用'aligned'或'align*'
2x + y  &= 1  \\     %加'&'指定对齐位置
2x + 2y &= 2
\end{aligned}
\right.
\end{equation}
% \end{equation*}   加'*'去掉公式编号

% 注意：在 markdown 环境下，某些特殊字符，如'\', '*'等，会首先被 markdown 语法转义，然后再被 Latex 转义。
% 因此有时候 '\{'需要写作'\\{'，'*'需要写作'\*'，'\\'需要写作'\\\\'等，视不同的解释环境而定
​```
```



![img](https://www.zhihu.com/equation?tex=%5Cbegin%7Bequation%7D+++++%5Cleft%5C%7B+++++%5Cbegin%7Baligned%7D++++++++2x+%2B+y+%26%3D+1++%5C%5C+++++++++2x+%2B+2y+%26%3D+2+++++%5Cend%7Baligned%7D+++++%5Cright.+++++%5Cend%7Bequation%7D+%5C%5C)



**注**：如果各个方程需要在某个字符处对齐（如等号对齐），只需在所有要对齐的字符前加上 `&` 符号。如果不需要公式编号，只需在宏包名称后加上 `*` 号。

### **分情况讨论方程式**

```
​```
f(x) =
\begin{cases}
x^2 \qquad & a \gt 0 \\
e^x \qquad & a \le 0
\end{cases}
​```
```



![img](https://www.zhihu.com/equation?tex=++++f%28x%29+%3D+++++%5Cbegin%7Bcases%7D+++++x%5E2+%5Cqquad+%26+a+%5Cgt+0+%5C%5C+++++e%5Ex+%5Cqquad+%26+a+%5Cle+0+++++%5Cend%7Bcases%7D+%5C%5C)



### **4.4 编号**

### **插入编号**

使用 `\tag` 指令指定公式的具体编号，并使用 `\label` 指令埋下锚点。如 `y=x^2 \tag{1.5a} \label{eq:test}`：



![img](https://www.zhihu.com/equation?tex=y%3Dx%5E2+%5Ctag%7B1.5a%7D+%5C%5C)



### **引用编号**

使用 `\eqref` 指令引用前面埋下的锚点，`\eqref{eq:test}` 将显示为：

![img](https://www.zhihu.com/equation?tex=%5Ceqref%7Beq%3Atest%7D)

。(简书里还不支持公式编号的锚点引用)。

-----------未完待续！