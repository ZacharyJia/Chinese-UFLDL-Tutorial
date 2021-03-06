# Softmax回归（Softmax Regression）  
## 
## 介绍（Introduction）  

Softmax回归（或称为多元逻辑斯特回归）是通常的逻辑斯特回归用来处理多类分类问题的更一般化形式。在逻辑斯特回归中，我们假定标签都是二元的：即 $y^{(i)} \in \{0,1\}$ 。我们曾用这样的一个分类器来做两类的手写数字分类。然而，Softmax 回归可让我们处理K类（的分类问题），其中类标签 $y^{(i)} \in \{1,K\}$ 。  

不妨再回归一下逻辑斯特回归，我们有m个已标记样本的训练集 $\{ (x^{(1)}, y^{(1)}), \ldots, (x^{(m)}, y^{(m)}) \}$ ，其中（每个样本的）输入特征是 $x^{(i)} \in \Re^{n}$ 。在先前的逻辑斯特回归中，我们的分类设定的是两类，所以类标签是 $y^{(i)} \in \{0,1\}$ ，我们假设采取这样的形式：  

$$
\begin{align}
h_\theta(x) = \frac{1}{1+\exp(-\theta^\top x)},
\end{align}
$$  


其中，模型参数 $\theta$ 被用来训练以最小化代价函数  

$$
\begin{align}
J(\theta) = -\left[ \sum_{i=1}^m y^{(i)} \log h_\theta(x^{(i)}) + (1-y^{(i)}) \log (1-h_\theta(x^{(i)})) \right]
\end{align}
$$  

在Softmax回归的设定中，（与前文中两类分类相反）我们的兴趣在多类分类，正因如此标签 $y$ 可以取 $K$ 个不同的值，而不仅限于（两类分类中的）两个值。因此，训练集样本 $\{ (x^{(1)}, y^{(1)}), \ldots, (x^{(m)}, y^{(m)}) \}$ 的类别标签值有 $y^{(i)} \in \{1, 2, \ldots, K\}$ 。（注意：我们通常起始类从 $1$ 开始，而不是 $0$ ）。举个例子，在 $MNIST$ 数字识别任务（译者注： MNIST 是一个手写数字识别库，由NYU 的Yann LeCun 等人维护。http://yann.lecun.com/exdb/mnist/ ）中，我们有 $K = 10$ ，即不同的类别个数是10个。  


给出测试输入 $x$ ，我们希望我们的假设可以针对同一样本在不同的 $k$ （其中，$k = 1, ..., K$）值下估计概率 $P(y=k | x)$ 的值。也就是说，我们想要估计类标签取 $K$ 个不同的值时的概率。由此，我们的假设将会输出 $K$ 维向量（该向量元素值和为 $1$ ），它给出的是 $K$ 个估计的概率值。更具体地说，我们的假设 $h_{\theta}(x)$ 会采取这样的形式：  

$$
\begin{align}
h_\theta(x) =
\begin{bmatrix}
P(y = 1 | x; \theta) \\
P(y = 2 | x; \theta) \\
\vdots \\
P(y = K | x; \theta)
\end{bmatrix}
=
\frac{1}{ \sum_{j=1}^{K}{\exp(\theta^{(j)\top} x) }}
\begin{bmatrix}
\exp(\theta^{(1)\top} x ) \\
\exp(\theta^{(2)\top} x ) \\
\vdots \\
\exp(\theta^{(K)\top} x ) \\
\end{bmatrix}
\end{align}
$$  

这里，$\theta^{(1)}, \theta^{(2)}, \ldots, \theta^{(K)} \in \Re^{n}$ 是我们模型的参数。需要注意的是，$\frac{1}{ \sum_{j=1}^{K}{\exp(\theta^{(j)\top} x) } }$ 这一项对分布进行了标准化（$normalize$），所以其（最终）会加和为一项。  


为方便起见，我们也写 $\theta$ 来表示我们模型的所有参数。当你实现 Softmax 回归时，$n$ 行 $K$ 列的矩阵 $\theta$ 其实也是一列列 $\theta^{(k)}$ 所组成的，即  

$$
\theta = \left[\begin{array}{cccc}| & | & | & | \\
\theta^{(1)} & \theta^{(2)} & \cdots & \theta^{(K)} \\
| & | & | & |
\end{array}\right].
$$  


## 代价函数（Cost Function）  

我们现在来描述 Softmax 回归的代价函数。在下面的方程中， $1\{\cdot\}$ 被称为“指示器函数”（$indicator function$ ，译者注：老版教程中译为“示性函数”），即 $1\{值为真的表达式\} = 1$ ，$1\{值为假的表达式\} = 0$ 。例如， $1\{2+2=4\}$ 求出的数值为 $1$ ；而 $1\{1+1=5\}$ 求出的数值为 $0$ 。我们的代价函数将会是：  

$$
\begin{align}
J(\theta) = - \left[ \sum_{i=1}^{m} \sum_{k=1}^{K}  1\left\{y^{(i)} = k\right\} \log \frac{\exp(\theta^{(k)\top} x^{(i)})}{\sum_{j=1}^K \exp(\theta^{(j)\top} x^{(i)})}\right]
\end{align}
$$  

值得注意的是，我们也可以将逻辑斯特回归的代价函数等价地写成这样类似的形式：  

$$
\begin{align}
J(\theta) &= - \left[ \sum_{i=1}^m   (1-y^{(i)}) \log (1-h_\theta(x^{(i)})) + y^{(i)} \log h_\theta(x^{(i)}) \right] \\
&= - \left[ \sum_{i=1}^{m} \sum_{k=0}^{1} 1\left\{y^{(i)} = k\right\} \log P(y^{(i)} = k | x^{(i)} ; \theta) \right]
\end{align}
$$  

除了我们需要将 $K$ 个不同的类标签可能值相加外， Softmax 的代价函数（与逻辑斯特回归的代价函数）是相似的。需要注意的是，在 Softmax 回归中我们有：  

$$
P(y^{(i)} = k | x^{(i)} ; \theta) = \frac{\exp(\theta^{(k)\top} x^{(i)})}{\sum_{j=1}^K \exp(\theta^{(j)\top} x^{(i)}) }
$$  

对于 $\textstyle J(\theta)$ 的最小化问题，目前还没有闭式解法（译者注：参考老版教程。老版说是因为没有 $closed-form way$，但是新版用 $analytically$ 一词一带而过，新版原文：“We cannot solve for the minimum of $\textstyle J(\theta)$ analytically”，老版原文：“There is no known closed-form way to solve for the minimum of $\textstyle J(\theta)$”）。因此，如往常一样，我们使用迭代优化算法来求解。对其求导，其梯度为：  

$$
\begin{align}
\nabla_{\theta^{(k)}} J(\theta) = - \sum_{i=1}^{m}{ \left[ x^{(i)} \left( 1\{ y^{(i)} = k\}  - P(y^{(i)} = k | x^{(i)}; \theta) \right) \right]  }
\end{align}
$$  

回想 $\nabla_{\theta^{(k)}}$ 符号的含义。尤其需要注意的是， $\nabla_{\theta^{(k)}} J(\theta)$ 其本身就是一个向量，所以，其第 $j$ 个元素即 $\frac{\partial J(\theta)}{\partial \theta_{lk}}$ ，它是关于 $\theta^{(k)}$ 的第 $j$ 个元素的偏导数。  

有了这个导数公式，然后可以将其插入到一个优化包中并最小化 $J(\theta)$ 。  


##Softmax回归的参数属性（Properties of Softmax regression parameterization）  

Softmax 回归有一个不同寻常的特性，那就是参数冗余（ $redundant$ ）。为了解释这个特性，我们假设有参数向量 $\theta^{(j)}$ ，我们对该向量减去某个固定的向量 $\psi$ ，此时（向量中地每个元素） $\theta^{(j)}$ 就被$\theta^{(j)} - \psi$ （其中 $j = 1, ..., k$ ）替代了。现在，我们的假设在计算类标签的概率（表示）为  

$$
\begin{align}
P(y^{(i)} = k | x^{(i)} ; \theta)
&= \frac{\exp((\theta^{(k)}-\psi)^\top x^{(i)})}{\sum_{j=1}^K \exp( (\theta^{(j)}-\psi)^\top x^{(i)})}  \\
&= \frac{\exp(\theta^{(k)\top} x^{(i)}) \exp(-\psi^\top x^{(i)})}{\sum_{j=1}^K \exp(\theta^{(j)\top} x^{(i)}) \exp(-\psi^\top x^{(i)})} \\
&= \frac{\exp(\theta^{(k)\top} x^{(i)})}{\sum_{j=1}^K \exp(\theta^{(j)\top} x^{(i)})}.
\end{align}
$$  

换句话说，从每个（元素） $\theta^{(j)}$ 中减去 $\psi$ 一点也不会影响到我们的假设预测！这表明了 Softmax 回归的参数中是有多余的。更正式地说，我们的 Softmax 模型是过参数化的（ $overparameterized$ ，或者说是参数冗余的），意味着对任何一个拟合数据的假设而言，有多种参数取值很有可能得到同样的假设 $h_\theta$，即从输入 $x$ 得到同样的预测结果。  

进一步说，如果成本函数（ $cost function$ ）$J(\theta)$ 被某组参数 $(\theta^{(1)}, \theta^{(2)},\ldots, \theta^{(k)})$ 最小化，那么对任意的 $\psi$ ，成本函数也可以被 $(\theta^{(1)} - \psi, \theta^{(2)} - \psi,\ldots, \theta^{(k)} - \psi)$ 最小化。因此， $J(\theta)$ 的最小值时的参数并不唯一。（有趣的是， $J(\theta)$ 仍是凸的，并且在梯度下降中不会遇到局部最优问题，但是 $Hessian$ 矩阵是奇异或者不可逆的，这将会导致在牛顿法的直接实现上遇到数值问题。）  

我们也注意到，通过设定 $\psi = \theta^{(K)}$ ，我们总是可以用 $\theta^{(K)} - \psi = \vec{0}（这是全零向量，其元素值均为0）$ 代替 $\theta^{(K)}$ ，而不会对我们的假设函数有任何影响。因此我们可以去掉参数向量 $\theta$ 中的最后一个元素 $\theta^{(K)}$ （或者该向量中任意其他的元素），而不影响假设函数的表达能力。实际上，与其优化全部的 $K\cdot n$ 个参数 $(\theta^{(1)}, \theta^{(2)},\ldots, \theta^{(K)})$ （其中 $\theta^{(k)} \in \Re^{n}$），我们可以令 $\theta^{(K)} = \vec{0}$ ，只优化剩余的 $K \cdot n$ 个参数（这样算法依然能够正常工作）。  

# 与逻辑斯特回归的关系（Relationship to Logistic Regression）  

在 $K=2$ 特例中，一个可以证明的是 Softmax 回归简化为了逻辑斯特回归。这表明了 Softmax 回归是逻辑斯特回归的一般化形式。更具体地说，当 $K=2$，Softmax 回归的假设函数为  

$$
\begin{align}
h_\theta(x) &=

\frac{1}{ \exp(\theta^{(1)\top}x)  + \exp( \theta^{(2)\top} x^{(i)} ) }
\begin{bmatrix}
\exp( \theta^{(1)\top} x ) \\
\exp( \theta^{(2)\top} x )
\end{bmatrix}
\end{align}
$$  

利用“假设是过参数化的”（或说“假设的回归参数冗余”） 这一特点，设定 $\psi = \theta^{(2)}$ ，并且从这两个向量中都减去向量 $\theta^{(2)}$ ，得到  

$$
\begin{align}
h(x) &=

\frac{1}{ \exp( (\theta^{(1)}-\theta^{(2)})^\top x^{(i)} ) + \exp(\vec{0}^\top x) }
\begin{bmatrix}
\exp( (\theta^{(1)}-\theta^{(2)})^\top x )
\exp( \vec{0}^\top x ) \\
\end{bmatrix} \\

&=
\begin{bmatrix}
\frac{1}{ 1 + \exp( (\theta^{(1)}-\theta^{(2)})^\top x^{(i)} ) } \\
\frac{\exp( (\theta^{(1)}-\theta^{(2)})^\top x )}{ 1 + \exp( (\theta^{(1)}-\theta^{(2)})^\top x^{(i)} ) }
\end{bmatrix} \\

&=
\begin{bmatrix}
\frac{1}{ 1  + \exp( (\theta^{(1)}-\theta^{(2)})^\top x^{(i)} ) } \\
1 - \frac{1}{ 1  + \exp( (\theta^{(1)}-\theta^{(2)})^\top x^{(i)} ) } \\
\end{bmatrix}
\end{align}
$$  

因此，用一个参数向量 $\theta'$ 来表示 $\theta^{(2)}-\theta^{(1)}$ ，我们就会发现 Softmax 回归预测其中一个类别的概率为 $\frac{1}{ 1  + \exp(- (\theta')^\top x^{(i)} ) }$ ，另一个类别的概率为 $1 - \frac{1}{ 1  + \exp(- (\theta')^\top x^{(i)} ) }$，这与 logistic回归是一致的。  

## 练习 1C（Exercise 1C）  

针对这一部分练习的初学者代码（Starter code）已经在 <a href="https://github.com/amaas/stanford_dl_ex">GitHub 代码仓库</a> 中的 <font color=red>`ex1/`</font> 目录下。  

在本次练习中，你将会借助 $MNIST$ 数据集，训练一个用于处理 $10$ 个数字的分类器。这部分代码除会读取整个 $MNIST$ 数据的训练和测试集外，其余的部分会与先前我们在练习 1B 中的代码（仅仅是识别数字 $0$ 和 $1$）非常类似，并且标签值 $y^{(i)}$ 从原本的 $2$ 类到现在的 $10$ 类， 即 $y^{(i)} \in \{1,\ldots,10\}$ 。（标签值的改变可以使您方便地将 $y^{(i)}$ 的值作为矩阵的下标。）  

这部分代码的表现应该和在练习 1B 中的一样：读取训练和测试数据，同时加入截距项，然后借助 <font color=red>`softmax_regression_vec.m`</font> 文件调用 <font color=red>`minFunc`</font> 作为目标函数。当训练完成后，将会输出手写数字识别问题中，这 $10$ 个类（译者注：对应从 $0$ 到 $9$ 这$10$ 个数字）的训练和测试集上的准确率。  

您的任务是实现 <font color=red>`softmax_regression_vec.m`</font> 文件中计算 softmax 目标函数 $J(\theta; X,y)$ 的部分，同时将计算结果存储在变量 $f$ 中。  

您也务必计算梯度项  $\nabla_{\theta} J(\theta; X,y)$ ，并将其结果存在变量 $g$ 中。请不要忘记 <font color=red>`minFunc`</font> 提供了向量参数 $\theta$ 。初学者代码将会对参数 $\theta$ 变形为一个 $n$ 行 $K-1$ 列的矩阵（对于 $10$ 个类这种情况，即 $K=10$）。同时，您也不要忘记了如何将返回的梯度 $g$ 返回成一个向量的方法，即 `g=g(:)` ；


如果有必要得到梯度权，你可以以写一段使用 for 循环的代码开始（请务必使用前面介绍的渐变检查调试策略！）。然而，您也许会发现这个实现的版本速度太慢，以至于优化不能通过所有的方式（ 译者注：翻译不确定。“However, you might find that this implementation is too slow to run the optimizer all the way through.”）。在您得到一个运行较慢梯度权计算的版本后，您可以在进行所有实验前，尝试尽可能地将您的代码进行向量化处理。   

下面是几条 MATLAB 的小提示，可能对您实现或者加速代码能起到作用（这些提示可能多少会有用处，但更多地取决于您的实现策略）。  

1. 假设我们有一个矩阵 $A$ ，我们想从每行抽出单个元素。其中，从第 $i$ 行抽出的元素，其列值并存在变量 $y(i)$ 中， $y$ 是一个行向量。这个转换过程我们可以用函数 <font color=red>`sub2ind`</font>来实现：  

    ```
    I=sub2ind(size(A), 1:size(A,1), y);  
    values = A(I);  
    ```  

    这段代码将会采用索引对 $(i,j)$ ，并计算出矩阵 $A$ 中在$(i,j)$ 位置处的一维索引。所以， $I(1)$ 将会矩阵 $A$ 中位置在 $(1, y(1))$ 处的元素下标，同样， $I(2)$ 将会矩阵 $A$ 中位置在 $(2, y(2))$ 处的元素下标。  

2. 当你计算预测类标签概率 $\hat{y}^{(i)}_k = \exp(\theta_{:,k}^\top x^{(i)}) / (\sum^K_{j=1} \exp(\theta_{:,j}^\top x^{(i)}))$ 时，试着用矩阵乘法以及 <font color=red>`bsxfun`</font> 来加速计算。比方说，当 $\theta$ 是矩阵的形式时，你可以为每个样本及其对应的 $9$ 类使用 $a = \theta^\top X$ 这样矩阵的形式，来计算乘积（再次强调一下，第 $10$ 类已经从 $\theta$ 中省略了，也就是说 $a(10,:)$ 的值被假定为 $0$）。