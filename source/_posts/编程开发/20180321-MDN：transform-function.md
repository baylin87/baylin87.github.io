---
title: 【翻译】MDN：transform-function
date: 2018-03-21 13:50:33
categories: 外文翻译
tags: [CSS,transform]
no_toc: true
---

原文地址：

[MDN : transform-function](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-function)

**transform-function**用于对元素的显示做变换。通常，这种变换可以由矩阵表示，并且可以使用每个点上的矩阵乘法来确定所得到的图像。

以下为3D图像关于各种转换函数的定义。

<!--more-->

### perspective()

 `perspective()` 这个CSS函数定义了 z=0平面与用户之间的距离，以便给三维定位元素一定透视度。当每个3D元素的z>0时会显得比较大，而在z<0时会显得比较小。其影响的程度由这个属性的值来决定。

#### 语法

```css
perspective(l)
```

#### 参数

- l

  该参数是一个 `length`， 给定从用户（显示屏）到z = 0平面的距离。 它用于将透视转换(3D)应用于元素。 如果它是0或负值，则不应用3D变换。

### rotate()

![rotate()](http://olvboulzy.bkt.clouddn.com/20180321-transform-functions-rotate_19.5.png) `rotate()` 这个CSS 函数 定义一个旋转属性，将元素在不变形的情况下旋转到不动点周围( `transform-origin`属性指定) 。 移动量由指定角度定义；如果为正值，则运动将为顺时针，如果为负值，则为逆时针 。 180°的旋转称为点反射 (*point reflection*)。

#### 语法

```css
rotate(a)
```

#### 参数

- *a*

  该参数为 `angle`， 代表旋转的角度。正角表示顺时针旋转，负角表示逆时针旋转。

### rotate3d()

 `rotate3d()` 这个CSS 函数定义一个3D旋转功能，该旋转使元素能够绕固定轴移动而不变形。 其中移动量由指定角度定义；如果为正值，则运动将为顺时针，如果为负值，则为逆时针。

在三维空间中，旋转有三个自由度，各表示某条旋转轴。 旋转轴由[x，y，z]向量定义，且过原点（由 `transform-origin` CSS属性定义）。 如果向量没有标准化，即它的三个坐标的平方之和不是1，它将在内部被标准化。 不可标准化的向量，如空向量[0,0,0]，将导致旋转不被应用，但不会影响整个CSS属性。

注意：相对于平面上的旋转，三维旋转的组成通常不是可交换位置顺序的，这意味着旋转的应用顺序是至关重要的。

#### 语法

```css
rotate3d(x, y, z, a)
```

#### 参数

- *x*

  该参数为"`number`" 类型值， 描述旋转轴向量的x坐标 。

- *y*

  该参数为 "`number`" 类型值，描述旋转轴向量的y坐标。

- *z*

  该参数为 "`number`" 类型值，描述旋转轴向量的z坐标。

- *a*

  该参数为 "`angle`" 代表旋转的角度 。 正角度表示顺时针旋转，负角度表示逆时针旋转 。

### rotateX()

 `rotateX()` 这个CSS 函数定义了将元素在横坐标上旋转而不使其变形的方法。 其运动的程度由指定的角度来定义；如果是正的，则为顺时针旋转，如果是负的，则是逆时针旋转。

旋转所经过的原点，由 `transform-origin` CSS属性定义。.

`rotateX(a)`是 `rotate3D(1, 0, 0, a)`的简写形式。

注意： 与平面上的旋转相反，3D旋转的组成通常也是不可交换顺序的；这意味着旋转的应用顺序至关重要。

#### 语法

```css
rotateX(a)
```

#### 参数

- *a*

  该参数为 "`angle`"， 代表旋转的角度， 正角表示顺时针旋转，负角表示逆时针旋转。

### rotateY()

 `rotateY()` 这个CSS 函数定义了将元素在纵坐标上旋转而不使其变形的方法。 其运动的程度由指定的角度来定义；如果是正的，则为顺时针旋转，如果是负的，则是逆时针旋转。

旋转所经过的原点，由 `transform-origin` CSS属性定义。

`rotateY(a)` 是 `rotate3D(0, 1, 0, a)` 的简写形式。

注意： 与平面上的旋转相反，3D旋转的组成通常也是不可交换顺序的；这意味着旋转的应用顺序至关重要。

#### 语法

```css
rotateY(a)
```

#### 参数

- *a*

  该参数为 "`angle`" 代表旋转的角度， 正角表示顺时针旋转，负角表示逆时针旋转。

### rotateZ()

 `rotateZ()`这个CSS 函数定义了将元素在z轴上旋转而不使其变形的方法。 其运动的程度由指定的角度来定义；如果是正的，则为顺时针旋转，如果是负的，则是逆时针旋转。

旋转所经过的原点，由 `transform-origin` CSS属性定义。

`rotateZ(a) `是 `rotate3D(0, 0, 1, a)` 的简写形式。

注意： 与平面上的旋转相反，3D旋转的组成通常也是不可交换顺序的；这意味着旋转的应用顺序至关重要。

#### 语法

```css
rotateZ(a)
```

#### 参数

- *a*

  该参数为 "`angle`" 代表旋转的角度， 正角表示顺时针旋转，负角表示逆时针旋转。

### `scale()`

![scale()](http://olvboulzy.bkt.clouddn.com/20180321-transform-functions-scale_2_2.png)

The `scale()` CSS 函数可改变元素的大小。 它可以增大或减小元素的大小，并且缩放量由矢量定义，并且它可以使在一个方向上比另一个方向更多。

这种变换的特点是矢量的坐标可定义在每个不同方向上各子完成一定比例缩放。如果矢量的两个坐标相等，则缩放是均匀的或各向同性的，并且元素的形状被保留。在这种情况下，缩放函数定义了一个同调变换。

当超出 `[-1, 1]`范围外时，缩放将在坐标方向上放大元素；当在该范围内时，它在该方向收缩元素。当等于1时，它什么也不做，当它为负时，它执行点反射和大小修改。

注意： `scale()` 函数仅在欧几里德平面(2D)中应用转换。要在空间中进行缩放，必须使用 `scale3D()` 函数。

#### 语法

```css
scale(sx) 或
scale(sx, sy)
```

#### 参数

- *sx*

  该参数为 "`number`" 类型值， 代表缩放矢量的横坐标。

- **sy**

  该参数为 "`number`" 类型值，代表缩放矢量的纵坐标 。 如果不存在，则其默认值为 **sx**，从而导致保持元素形状进行均匀缩放。

### `scale3d()`

 `scale3d()` CSS 函数可改变元素的大小。由于缩放的量由矢量定义，因此可以改变不同方向的尺寸。

这种变换的特点是矢量的坐标定义在每个方向上完成多少缩放。如果矢量的三个坐标都相等，则缩放是均匀的或各向同性的，并且保持元素形状。在这种情况下，这个缩放函数就是定义了一个同调变换。

当超出[-1，1]的范围之外时，缩放比例将在坐标方向上放大元素；当在[-1，1]范围内时，它在当前方向收缩元素。当等于1时，它什么也不做，当它为负时，它执行点反射和大小修改。

#### 语法

```css
scale3d(sx, sy, sz)
```

#### 参数

- *sx*

  该参数为 "`number`" 类型值， 代表缩放矢量的横坐标。

- *sy*

  该参数为 "`number`" 类型值， 代表缩放矢量的纵坐标。

- *sz*

  该参数为 "`number`" 类型值， 代表缩放矢量的z轴坐标。

### scaleX()

![scaleX()](http://olvboulzy.bkt.clouddn.com/20180321-transform-functions-scaleX_2.png) `scaleX()` 这个CSS函数是用一个常数因子来修改每个单元点的横坐标，在比例因子是1的情况下该函数是恒等变换。 缩放不是各向同性的，元素的角度也不守恒。

`scaleX(sx)` 是 `scale(sx, 1)` 和 `scale3d(sx, 1, 1)` 的简写形式。

`scaleX(-1)` 表示通过原点的垂直轴定义轴对称（由 `transform-origin` 属性指定）。

#### 语法

```css
scaleX(s)
```

#### 参数

- *s*

  该参数为 "`number`" 类型值， 表示在元素的每个点的横坐标上应用的缩放因子。

### scaleY()

![scaleY()](http://olvboulzy.bkt.clouddn.com/20180321-transform-functions-scaleY_2.png)

 `scaleY()` 这个 CSS函数用一个常数因子修改每个元素点的纵坐标，在比例因子是1的情况下该函数是恒等变换。 缩放不是各向同性的，元素的角度不守恒。

`scaleY(sy)` 是 `scale(1, sy)` 和 `scale3d(1, sy, 1)` 的简写形式。

`scaleY(-1)` 定义了通过原点的水平轴的轴对称（由 `transform-origin` 属性指定）。

#### 语法

```css
scaleY(s)
```

#### 参数

- *s*

  该参数为 "`number`" 类型值，表示在元素的每个点的纵坐标上应用的缩放因子。

### scaleZ()

 `scaleZ()` 这个CSS函数是用一个常数因子来修改每个单元点的z轴坐标，在比例因子是1的情况下该函数是恒等变换。 缩放不是各向同性的，元素的角度也不守恒。

`scaleZ(sz)` 是 `scale3d(1, 1, sz)` 的简写形式。

`scaleZ(-1)` 定义了通过原点的z轴的轴对称（由 `transform-origin` 属性指定）。

#### 语法

```css
scaleZ(s)
```

#### 参数

- *s*

  该参数为 "`number`" 类型值，表示在元素的每个点的z轴坐标上应用的缩放因子。

### skew()

 `skew()` 这个CSS函数是一种用于拉伸，或者说是平移，该函数会使得在每个方向上扭曲元素上的每个点以一定角度。这是通过将每个坐标增加一个与指定角度成比例的值和到原点的距离来完成的。离原点越远，拉伸的值就越大。

#### 语法

```css
skew(ax)       或
skew(ax, ay)
```

#### 参数

- *ax*

  该参数为一个角度 "`angle`" ， 表示用于沿着横坐标扭曲元素的角度 。

- *ay*

  该参数为一个角度 "`angle`" ， 表示用于沿纵坐标扭曲元素的角度 。

### skewX()

 `skewX()` 这个CSS函数是用于水平拉伸，它将元素的每个点在水平方向上扭曲一定角度。这是通过将横坐标增加一个与指定角度成比例的值以及到原点的距离来完成的。离原点越远，拉伸的值就越大。

#### 语法

```css
skewX(a)
```

#### 参数

- *a*

  该参数为一个角度 "`angle`" ， 表示用于沿着横坐标扭曲元素的角度。

### skewY()

 `skewY()` 这个CSS函数是用于垂直拉伸，它将元素的每个点在垂直方向上扭曲一定角度。这是通过将纵坐标增加一个与指定角度成比例的值以及到原点的距离来完成的。离原点越远，拉伸的值就越大。

#### 语法

```css
skewY(a)
```

#### 参数

- *a*

  该参数为一个角度 "`angle`" ，表示用于沿着纵坐标扭曲元素的角度。

### translate()

![translate()](http://olvboulzy.bkt.clouddn.com/20180321-transform-functions-translate_2.png) `translate()` 这个CSS 函数用于移动元素在平面上的位置。这种变换的特点是矢量的坐标定义了它在每个方向上的移动量。

#### 语法

```css
translate(tx)       或
translate(tx, ty)
```

#### 参数

- *tx*

  该参数为 "`length`" ，表示要移动矢量的横坐标。

- *ty*

  该参数为 "`length`" ，表示要移动矢量的纵坐标。 如果不写，则默认与 **tx **值相等，例如 :  `translate(2)` 表示 `translate(2, 2)`。

### translate3d()

 `translate3d()` 这个CSS 函数用于移动元素在3D空间中的位置。 这种变换的特点是三维矢量的坐标定义了它在每个方向上的移动量。

#### 语法

```css
translate3d(tx, ty, tz)
```

#### 参数

- *tx*

  该参数为 "`length`" ，代表移动矢量的横坐标。

- *ty*

  该参数为 "`length`" ，代表移动矢量的纵坐标。

- *tz*

  该参数为 "`length`" 代表移动矢量的z轴坐标。 该值不能使用百分比 "`percentage`" ；如果使用会被认为是无效属性。

### `translateX()`

![translateX()](http://olvboulzy.bkt.clouddn.com/20180321-transform-functions-translateX_2.png) `translateX()` 这个CSS 函数用于在平面上水平移动元素。 这个转换的特点是 "`length`" 定义了它的水平移动量。

`translateX(tx)` 是 `translate(tx, 0) `的简写形式。

#### 语法

```css
translateX(t)
```

#### 参数

- *t*

  该参数为 "`length`" ，代表移动矢量的横坐标。

### translateY()

![translateY()](http://olvboulzy.bkt.clouddn.com/20180321-transform-functions-translateY_2.png) `translateY()` 这个CSS 函数用于在平面上垂直移动元素。 这个转换的特点是通过 "`length`" 定义了它的垂直移动量。

`translateY(ty)` 是 `translate(0, ty)`的简写形式。

#### 语法

```css
translateY(t)
```

#### 参数

- *t*

  该参数为 "`length`" 代表移动矢量的横坐标。该参数不能使用百分比 "`percentage`" ；如果使用百分比形式，会被认为是无效参数。

### translateZ()

 `translateZ()` 这个CSS 函数用于3D空间的z轴方向移动元素， 这个转换的特点是通过 "`length`" 定义了它的z轴方向移动量。

`translateZ(tz)` 是 `translate3d(0, 0, tz) `的简写形式。

#### 语法

```css
translateZ(t)
```

#### 参数

- *t*

  该参数是 "`length`" ，代表移动矢量的z轴坐标。



本文完。