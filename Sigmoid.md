# 激活函数

- Sigmoid
  $$
  Sigmoid(x) = \frac{1}{1 + e^{-x}}
  $$

- tanh

$$
tanh(x)= \frac{e^x - e^{-x}}{e^x+e^{-x}}
$$

- Relu

$$
\left.ReLU(x)=\left\{\begin{aligned}0&&x<0\\ x&&x\geq0\end{aligned}\right.\right.
      
$$

- Mish

$$
Mish(x) = x \times tanh(ln(1+e^x)
$$

- Swish
  $$
  Swish(x) = x\times sigmoid(x)
  $$
  
- Gelu
  $$
  Gelu(x) = 0.5x(1+tanh(\sqrt{\frac{2}{\pi}}(x + 0.044715x^3))
  $$

# 优化器

## 一. SGD

$\theta_{t+1} = \theta_t-\eta\nabla J(\theta_t; x_i,y_i)$

其中：

- $\theta_t$是模型参数在第t次迭代的值
- $\eta$是学习率，控制更新步长
- $J(\theta_t;x_i,y_i)$是损失函数
- $\nabla J(\theta_t; x_i,y_i)$是损失函数关于参数的梯度



## 二. Adam

$m_t = \beta_1\cdot m_{t-1} + (1-\beta_1)\cdot g_t$
$v_t = \beta_2\cdot v_{t-1} + (1-\beta)(g_t)^2$
$\hat{m_t} = \frac{m_t}{1-\beta_1^t} $
$\hat{v_t}=\frac{v_t}{1-\beta_2^t}$
$\theta_{t+1} = \theta_t-\eta\cdot\frac{\hat{m_t}}{\sqrt{\hat{v_t}}+\epsilon}$

其中：

- $m_t$ 是对梯度一阶矩估计，表示梯度的指数加权平均
- $v_t$ 是梯度的二阶矩估计，表示梯度的平方指数加权平均
- $\hat{m_t}$ 和$\hat{v_t}$ 是对$m_t$ 和$v_t$ 进行偏差修正后的估计
- $\beta_1$ 和$\beta_2$ 是衰减系数，通常取值为0.9和0.999
- $\eta$ 是学习率

- $\epsilon$是一个很小的常数用于防止除零错误



# 距离度量

### 一. Euclidean Distance 

$$
Distance = \sqrt{(x_2-x_1)^2 + (y_2-y_1)^2}
$$

### 二. Manhattan Distance(L1 Norm)

$$
Distance = |x_2-x_1| + |y_2-y_1|
$$

### 三. Minkowski Distance

$$
Distance = (|x_2-x_1|^p + |y_2-y_1|^p)^{\frac{1}{p}}
$$

### 四. Cosine Similarity

$$
Distance = \frac{A\cdot B}{||A||\cdot||B||}
$$





# Loss

### 一. Triplet Loss

$$
Triplet Loss = max((d(A,P) - d(A, N)+\alpha, 0)
$$

### 二.  HardMine Triplet Loss

$$
HardMine\ \  Triplet \ \ Loss= max(0, max(d(A, N)) - min(d(A,P)+\alpha )
$$







