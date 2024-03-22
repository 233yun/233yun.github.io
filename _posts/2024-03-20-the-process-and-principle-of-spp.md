---
title: The process and principle of SPP
date: 2024-03-20 20:31 +0800
description: equations, positioning error source, and correction of SPP.
author: 233yun
categories: [GNSS,SPP]
tags: [SPP]
pin: true
math: true
mermaid: true
---

SPP(Single Point Positioning)的核心原理就是伪距观测方程，但由于伪距观测值中存在许多误差，所以我们需要在SPP算法中对这一系列误差进行矫正，以提高定位精度。

## 伪距观测值是什么？

$$
\begin{equation}
    \rho^s_r(t)=c \cdot(t_r-t^s)
    \label{eq:pseudo}
\end{equation}
$$

伪距观测值$\rho$是由卫星发射的测距码信号到达接收机天线的传播时间乘以光速得出的量测距离，包括接收机和卫星的钟差（以及其他偏移，如大气延迟和电离层延迟等）。由于和真实的卫星到接收机间的几何距离存在较大的偏差，所以叫伪距（Pseudo）。

## 伪距观测量和真正几何距离之间的关系

$$
\begin{equation}
    P=\rho^s_r(t)-c \cdot (\delta t_r-\delta t^s)-I-T-\varepsilon_p
    \label{eq:pseudoDiff}
\end{equation}
$$

式中$P$是真正的卫星到接收机的几何距离，$\rho$为伪距测量值，$c$为光速。卫星上的卫星钟可以给出信号的发射时间$t^s$，接收机钟则可以给出接收到信号的时间$t_r$，但卫星钟和接收机钟并不一定是对准的，假设卫星钟对准的校正值是$\delta t^s$，接收机钟对准的校正值是$\delta t_r$，那么信号传输时间的真值就是：

$$
\begin{equation}
    \Delta t=(t_r-\delta t_r)-(t^s-\delta t^s)=(t_r-t^s)-(\delta t_r-\delta t^s)
\end{equation}
$$

给上式乘以光速后，我们得到了\eqref{eq:pseudoDiff}等式右边的前三项。卫星钟差一般在广播星历和精密星历中均有提供，接收机钟多为石英钟，钟差变化快且往往有跳变，所以实际上也是一个未知数。余下的$I$表示电离层延迟，一般使用klobuchar模型进行改正；$T$表示对流层延迟，一般使用Saastamoinen模型进行改正；$\varepsilon_p$表示测距误差，一般采用随机噪声模型进行改正。因此，\eqref{eq:pseudoDiff}中的未知数目前只有一个接收机钟差。

## 伪距观测方程

卫星在地固系下的位置可以从精密星历/广播星历中获取，一般精密星历中的卫星位置视为精确值，与真实卫星位置的误差可以忽略，广播星历计算出的卫星位置往往存在一定的误差，其误差量级往往与SPP定位误差量级相同，SPP中也无法消除这一误差，只能尽可能降低。我们将卫星位置代入\eqref{eq:pseudoDiff}，等式左边就可以写成:

$$
\begin{equation}
    P=\sqrt{\left(X^s(t)-\boldsymbol{X}_{\boldsymbol{r}}\right)^2+\left(Y^s(t)-\boldsymbol{Y}_{\boldsymbol{r}}\right)^2+\left(Z^s(t)-\boldsymbol{Z}_{\boldsymbol{r}}\right)^2}
\end{equation}
$$

上式中卫星的位置$X^s(t)$、$Y^s(t)$、$Z^s(t)$都是已知的，将上式代入到\eqref{eq:pseudoDiff}中，我们就可以得到如下方程：

$$
\begin{equation}
    \rho^s_r(t)=\sqrt{\left(X^s(t)-\boldsymbol{X}_{\boldsymbol{r}}\right)^2+\left(Y^s(t)-\boldsymbol{Y}_{\boldsymbol{r}}\right)^2+\left(Z^s(t)-\boldsymbol{Z}_{\boldsymbol{r}}\right)^2}+c \cdot (\delta t_r-\delta t^s)+I+T+\varepsilon_p  
     \label{eq:pseudObv}
\end{equation}
$$

上式就是伪距观测方程，方程中有4个未知数，分别是接收机的三维坐标值和钟差。为了求解这4个未知数，我们至少需要有4个这样的观测方程才可以求解，这也是卫星定位时需要接收机至少观测到4颗卫星的原因。

## 观测方程线性化

当我们有四个观测方程时，理论上就可以求解出接收机的位置坐标。但\eqref{eq:pseudObv}方程是非线性的，其求解难忘往往较大，为了便于计算机处理及缩短计算时间，我们进一步通过泰勒级数展开进行方程线性化。

泰勒级数提供了一种将函数在某一点的局部行为展开成无限多项式的方法。对于在点 $a$ 处具有所有阶导数的函数 $f(x)$，其泰勒级数可以表示为：

$$
f(x) = f(a) + f'(a)(x-a) + \frac{f''(a)}{2!}(x-a)^2 + \frac{f'''(a)}{3!}(x-a)^3 + \ldots + \frac{f^{(n)}(a)}{n!}(x-a)^n + \ldots
$$

这里，$f^{(n)}(a)$是函数在$a$点的第$n$阶导数，而$n!$表示$n$的阶乘。

我们令 $(X_r,Y_r,Z_r)$为真实的接收机位置坐标，$(X_r^0,Y_r^0,Z_r^0)$表示近似坐标，$(\delta X_r,\delta Y_r,\delta Z_r)$表示坐标误差值，则有：

$$
\begin{equation}
    \left\{\begin{array}{c}
    X_r=X_r^0+\delta X_r \\
    Y_r=Y_r^0+\delta Y_r \\
    Z_r=Z_r^0+\delta Z_r
\end{array}\right.
\end{equation}
$$

对于伪距观测方程，我们对方程中非线性的部分进行展开，即对 $$P=\sqrt{\left(X^s(t)-\boldsymbol{X}_{\boldsymbol{r}}\right)^2+\left(Y^s(t)-\boldsymbol{Y}_{\boldsymbol{r}}\right)^2+\left(Z^s(t)-\boldsymbol{Z}_{\boldsymbol{r}}\right)^2}$$ 进行泰勒展开，则有：

$$
\begin{equation}
    P=f(X_r,Y_r,Z_r)=f(X_r^0,Y_r^0,Z_r^0)+f'_{X_r}(X_r^0,Y_r^0,Z_r^0)(X_r-X_r^0)+f'_{Y_r}(X_r^0,Y_r^0,Z_r^0)(Y_r-Y_r^0)+f'_{Z_r}(X_r^0,Y_r^0,Z_r^0)(Z_r-Z_r^0)
    \label{eq:simpleP}
\end{equation}
$$

上式中 $$f(X_r,Y_r,Z_r)=\sqrt{\left(X^s(t)-\boldsymbol{X}_{\boldsymbol{r}}\right)^2+\left(Y^s(t)-\boldsymbol{Y}_{\boldsymbol{r}}\right)^2+\left(Z^s(t)-\boldsymbol{Z}_{\boldsymbol{r}}\right)^2}$$，根据求导公式不难求出$f(X_r,Y_r,Z_r)$对于$X_r$的偏导为：

$$
\begin{equation}
\begin{split}
    &f'_{X_r}(X_r,Y_r,Z_r)=\frac{\partial P^s_r(t)}{\partial X_r}\\
    &=-\frac{X^s(t)-X_r}{\sqrt{(X^s(t)-X_r)^2+(Y^s(t)-Y_r)^2+(Z^s(t)-Z_r)^2}}\\
    &=-\frac{X^s(t)-X_r}{P^s_r(t)}
\end{split}
\end{equation}
$$

对于$Y_r$、$Z_r$的偏导同理可得。再代入点$(X_r^0,Y_r^0,Z_r^0)$，我们就可以得到如下偏导数：

$$
\begin{equation}
    \left\{\begin{array}
    (\frac{\partial P^s_r(t)}{\partial X_r})_0=-\frac{X^s(t)-X_r^0}{P^s_r(t)}=-l(t)^s\\
    (\frac{\partial P^s_r(t)}{\partial Y_r})_0=-\frac{Y^s(t)-Y_r^0}{P^s_r(t)}=-m(t)^s\\
    (\frac{\partial P^s_r(t)}{\partial Z_r})_0=-\frac{Z^s(t)-Z_r^0}{P^s_r(t)}=-n(t)^s
    \end{array}\right.
\end{equation}
$$

将偏导数代入\eqref{eq:simpleP}，得到线性化表达的$\boldsymbol{P}$之后，再代入伪距观测方程\eqref{eq:pseudObv}，整理后我们可以得到线性化的伪距观测方程如下：

$$
\begin{equation}
   \rho^s_r(t)=(P^s_r(t))_0-l(t)^s \delta X_r-m(t)^s \delta Y_r- n(t)^s \delta Z_r+c \cdot (\delta t_r-\delta t^s)+I+T+\varepsilon_p
   \label{eq:linearP}
\end{equation}
$$

## 最小二乘法求解

伪距观测方程线性化后，方程中的四个未知数分别为$\delta X_r$、$\delta Y_r$、$\delta Z_r$、$\delta t_r$，我们可以通过最小二乘法（Least Square Method,LSM）来进行迭代求解。通常设置$(X_r^0,Y_r^0,Z_r^0)$的初始值为(0,0,0)。

对于\eqref{eq:linearP}，方程左边为接收机的伪距观测值，右边为我们在真实距离的基础上进行误差建模拟合出来的伪距观测值，等式两边其实是存在误差的，我们用拟合值减去真实值，得到误差方程$V_r^s(t)$:

$$
\begin{equation}
    V_r^s(t)=(P^s_r(t))_0-l(t)^s \delta X_r-m(t)^s \delta Y_r- n(t)^s \delta Z_r+c \cdot (\delta t_r-\delta t^s)+I+T+\varepsilon_p-\rho^s_r(t)
    \label{eq:Veq}
\end{equation}
$$

令误差方程中的已知参数为$-\lambda^s_r(t)$:

$$
-\lambda^s_r(t)=(P^s_r(t))_0-c \cdot \delta t^s+I+T+\varepsilon_p-\rho^s_r(t)
$$

将上式代入误差方程\eqref{eq:Veq}中，我们可以得到：

$$
V^s_r(t)=l(t)^s \delta X_r-m(t)^s \delta Y_r- n(t)^s \delta Z_r+c \cdot \delta t_r -\lambda^s_r(t)
$$

当误差方程数$\ge 4$的时候，我们就可以求解出方程中的四个未知数，在此我们也给出矩阵形式的表达：

$$
\begin{equation}
    \begin{bmatrix}
    V^1_r(t)\\
    V^2_r(t)\\
    V^3_r(t)\\
    V^4_r(t)
    \end{bmatrix}=
    \begin{bmatrix}
        -l(t)^1&-m(t)^1&-n(t)^1&c\\
        -l(t)^2&-m(t)^2&-n(t)^2&c\\
        -l(t)^3&-m(t)^3&-n(t)^3&c\\
        -l(t)^4&-m(t)^4&-n(t)^4&c
    \end{bmatrix}
    \begin{bmatrix}
        \delta X_r\\
        \delta Y_r\\
        \delta Z_r\\
        \delta t_r
    \end{bmatrix}-
    \begin{bmatrix}
        \lambda^1_r(t)\\
        \lambda^2_r(t)\\
        \lambda^3_r(t)\\
        \lambda^4_r(t)
    \end{bmatrix}
\end{equation}
$$

即：$$\boldsymbol{V=Q\delta X -A}, min{\boldsymbol{V^TV}}$$

根据最小二乘法，当满足$min{\boldsymbol{V^TV}}$时，我们求得未知数的解。令$J=V^TV$，则当$\frac{\partial J}{\partial \delta X} =0$时，我们可以得到其最小值。

$$
\begin{equation}
    \begin{split}
        J&=V^TV\\
        &=(Q\delta X -A)^T(Q\delta X -A)\\
        &=\delta X^T Q^T Q \delta X-Q^T \delta X^T A -A^T  Q \delta X +A^TA
    \end{split}
\end{equation}
$$
通过对矩阵求导，可以得到：

$$
\begin{equation}
\begin{split}
    \frac{\partial J}{\partial \delta X} &=[Q^TQ+(Q^TQ)^T]\delta X -Q^T A -(A^TQ)^T\\
    &=2(Q^TQ)\delta X-2Q^TA=0
\end{split}
\end{equation}
$$

故而最小二乘法的求解结果为：

$$
\delta X= \frac{Q^TA}{Q^TQ}=(Q^TQ)^{-1}Q^TA
$$


