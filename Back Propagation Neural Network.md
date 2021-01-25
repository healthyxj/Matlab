# BP神经网络
## 1、人工神经网络

* 人工神经元模型
  * 输入
  * 输出
  * 权值
  * 阈值
  * 激活函数or映射(要求可导、)

输入一般为行向量，权值一般为列向量。两者相乘减去阈值作为映射的输入。

激活函数有线性函数、斜坡函数（三段，中间是线性的，两端是定值）、阈值函数（大于阈值时为1，or为0）、S型函数也叫sigmoid函数（fx等于1/（1+e的负ax）、总体范围是0到1），双极S型函数（fx等于2/（1+e负ax-1））

* 分类
  * 按连接方式
    * 前向神经网络
    * 反馈递归神经网络
  * 按学习方式
    * supervised learning 监督学习(有导师学习神经网络)
    * 无导师学习神经网络
  * 按实现功能
    * 拟合回归神经网络
    * 分类神经网络



---

## 2、BP神经网络(Backpropagation 反向传播)

主要包括propagation(传播)和weight updates(权值修正)

* propagation
  * forward proagation of a training pattern's input through the neural network in order to generate the propagation's output activations
  * back propagation of the propagation's output activations through the neural network using the training pattern's target in order to generate the deltas of all output and hidden neurons
* weight upate
  * multiply its output and input activations to get the gradient of the weight	梯度下降法
  * bring the weight in the opposite direction of the gradient by subtracting a ration of it from the weight.

原理和步骤要掌握

---

## 3、数据归一化

数据归一化是将数据映射到[0,1]or[-1,1]区间

* why
  * 输入数据单位不一样，某些数据范围大，导致神经网络收敛缓慢，训练时间长
  * 数据范围大的输入在模式分类中作用偏大
  * 神经网络的激活函数的值域有限制，例如s型函数的值域限制在(0,1)
  * S型激活函数在(0,1)区间外区域平缓，区分度小
* 算法
  * y=(x-min)/(max-min)
  * y=2*(x-min)/(max-min)-1

* 重点函数解读
  * mapminmax
    * process matrices by mapping row minimum and maximum values to[-1,1]
    * [Y,PS]=mapminmax(X,YMIN,YMAX)
      * X是要归一化的数据，YMIN和YMAX是归一化的范围
    * Y=mapminmax('apply',X,PS)
      * 利用归一化的信息PS对X进行应用
    * X=mapminmax('reverse',Y,PS)
      * 对X进行反归一化
  * newff
    * create feed-forward backpropagation network
    * net=newff(P,T,[S1,S2,……,S(n-1)],{TF1,TF2,……TFN},BTF,BLF,PF,IPF,OPF,DDF)
      * PT分别代表输入和输出，S代表隐藏传递的个数，TF代表传递函数
  * train
    * [net,tr,Y,E,Pf,Af]=train(net,P,T,Pi,Ai)
    * 训练
  * sim
    * 仿真和模拟
    * [Y,Pf,Af,perf]=sim(net,P,Pi,Ai,I)
