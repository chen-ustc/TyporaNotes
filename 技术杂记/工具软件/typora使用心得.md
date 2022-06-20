# typora使用心得

2022年5月2日

在此之前，已经使用过相当长一段时间的typora了，不过用到的功能都比较浅显，最近打算开始尝试看英文文档，刚好也想学习一下用typora来画图，因此有了这篇文档，之前已经比较熟练掌握的内容姑且先略过，先从画图开始，之后有时间的话可能会做出补充。

## 快捷键 

- ctrl+Enter 在表格下方插入行

  其余均没有快捷键

- ctrl+B 将选中内容变为粗体

- ctrl+I 将选中内容变为斜体

- 

## 公式

### 行间输入：

```
$公式内容$
```

### 公式块

```
$$+回车
公式内容
```

### 常用公式的latex语法

#### 能用键盘打出来的内容

- +
- -
- =
- /

#### 转义字符

- \

#### 乘号

```
\times
//dollar符前后的空格不是必须的，这里加空格只是为了美观
```

$\times$

#### 除号

```
\div
```

$\div$

#### 其它等号

- 大于等于号

  ```
  \ge
  其含义为greater than or equal to (大于或等于)
  ```

  $\ge$

- 小于等于号

  ```
  \le
  其含义为less than or equal to (小于或等于)
  ```

  $\le$

- 不等于号

  ```
  \ne
  其含义为not equal to (不等于)
  ```

  $\ne$

#### 上下标

- 上标

  ```
  ^上标内容 //上标为单个字符
  ^{上标内容} //上标为多个字符
  ```

  $a^2$

  $a^{local}$

- 下标

  ```
  _下标内容 //下标为单个字符
  _{下标内容} //下标为多个字符
  ```

  $a_2$

  $a_{local}$

#### 分数

```
\frac{分子内容}{分母内容}
frac是fraction(分数)的简写
```

#### 度

```
^{\circ}
\degree
```

$45\degree$

#### 给图片编号

typora本身没有内嵌给图片编号的方法，可以用html来给图片写编号，由于typora中插入的图片一般都是居中的，因此编号一般也选择居中。

```html
<center>
图1.1
<center>
```

若是需要修改样式，则用：
```html
<center style="color:#ffffff;text-declaration=underline;font-size=14px">
图1.1
<center>
```

之类的方式来进行修改

#### 行间公式换行

`\\`
$$
  A\\
  \times\\
  B
$$

#### 连等式

latex中连等式用{align}环境来操作，在需要对其的字符之前加上`&`,且不要忘记换行.

```latex]
\begin{align}
2\times(3+4)\\
&=2\times7\\
&=14
\end{align}
```

$$
\begin{align}
2\times(3+4)
&=2\times7\\
&=14
\end{align}
$$

#### 箭头

**单箭头**

- $\leftarrow$
- $\rightarrow$
- $\uparrow$
- $\downarrow$

其语法就是字面意思，很好理解

```latex
\leftarrow 或者 \gets
\right arrow 或者 \to
\uparrow
\downarrow
```

<font color='green'>左箭头可以用gets表示是因为它经常在伪代码中被用到，表示左边的值由右边的表达式推出。右箭头用to则是因为极限中经常用到它。</font>

<font color='green'>另外，左右箭头可以通过前面加n的方式来否定，类似不等号,下面的双框箭头也可以。但需要注意的是这里就不能用别名了</font>

- $\nleftarrow$
- $\nrightarrow$

**双框箭头**

双框箭头被认为是大号的单箭头，因此只需要将首字母大写即可

- $\Leftarrow$
- $\Rightarrow$
- $\Uparrow$
- $\Downarrow$
- $\iff$
- $\nLeftarrow$
- $\nRightarrow$

```
\Leftarrow
\Rightarrow 或者 \implies
\Uparrow
\Downarrow
\iff
\nLeftarrow
\nRightarrow
```

<font color='green'>iff代表 if and only if ,即等价。</font>

**半箭头**

harpoon是鱼叉的意思，可能是knuth觉得半箭头很像鱼叉，因此直接用harpoon来作为这种箭头的修饰词，它的规则是`"箭头方向"+"harpoon"+"鱼叉钩的方向"`

这里只举一个例子：

$\rightharpoonup$

```
\rightharpoonup
```

**其它异形箭头**

## 注脚

- 给某段文字插入注脚：

  ```
  [^num]
  ```

  你好[^1]

- 编辑注脚内容

  ```
  [^num]:write down something
  可以插入在文档的任意位置
  ```

  或者

  把鼠标悬停在注脚上，点击出现的超链接，就可以编辑注释内容，但注释内容默认在文档末尾。



[^1]: this is a simple test

常用于参考文献的标注等。

## 下划线

markdown本身并没有提供下划线的功能，可以用内嵌的html来实现。语法为：

```html
<u>下划线</u>
```

<u>下划线</u>

