# 1、标准化变换
熟悉随机数的生成方式：mvnrnd
熟悉标准化变换的函数：zscore
~~~matlab
X = mvnrnd([10;20],[10 .9;.9 20],1000); 
%[10 .9;.9 20]表示10 0.9\n 0.9 20
%mvnrnd能够返回均值MU（均值中的行数表示返回几列）和协方差
%格式是mvnrnd(MU,COV,行数)，所以上述是构造1000*2，两列均值分别为10和20

scatter(X(:,1),X(:,2)); 
%以X的第一列作为横坐标，第二列作为纵坐标创建散点图

[Z,MU,SIGMA] = zscore(X); 
%标准化变换-zscore
%将标准化变换的值(矩阵)返回给Z，均值为MU，标准差为sigma

%返回均值、标准差，因为标准化变换后均值为0，标准差为1
mean(Z);
std(Z) ;

%做出标准化变换后的散点图
hold on;
scatter(Z(:,1),Z(:,2));
~~~
# 2、逐步计算标准化变换
同样是生成随机的数，用标准化的定义对数进行标准化变换
> 标准化变换是除以标准差还是除以方差待解决

~~~matlab
%逐步计算过程
X = mvnrnd([10;20],[10 .9;.9 20],1000); 
scatter(X(:,1),X(:,2));
Z=zeros(1000,2);
c=mean(X);
d=std(X,0,1); 

%手动求标准化变换
for i=1:1000;
    for j=1:2;
       a=X(i,j);
       b=(a-c(:,j))/d(:,j);
       Z(i,j)=b;
    end
end
hold on
scatter(Z(:,1),Z(:,2));
mean(Z)
std(Z)
~~~
# 3、比较马氏距离和欧氏距离
马氏距离考虑了变量之间的相关性，而且也考虑到了各个观测指标取值的差异程度。因此马氏距离在某些场合会更加有优势。
>马氏距离是d²还是d，欧氏距离也是
~~~matlab
X = mvnrnd([0;0],[1 .9;.9 1],1000); 
Y = [1 1;1 -1]; 
%取样点(1,1)和(1,-1)

d1 = mahal(Y,X) 
% Mahalanobis distance
%返回Y到X的马氏距离

d2 = sum((Y-repmat(mean(X),2,1)).^2, 2)
%repmat是复制和平铺矩阵
%这里是将mean复制2*1块
%这里是求出欧氏距离，所以用sum(,2)表示求行的总和；
%sum(,1)表示求列的总和

scatter(X(:,1),X(:,2));
hold on;
scatter(Y(:,1),Y(:,2),200,d1,'*','LineWidth',2);
%这句话不太明白

hb = colorbar; 
%建立一个色条

ylabel(hb,'Mahalanobis Distance')
legend('X','Y','Location','NW')
%添加图例
~~~
# 4、聚类分析
关键是四步

* 寻找变量之间的相似性Y=pdist(X)
* 定义变量之间的连接Z=linkage(Y)
* 创建聚类T=cluster(Z,'cutoff',c)
* 作出谱系聚类图H=dendrogram(Z)
~~~matlab
load fisheriris
%导入鸢尾花的数据
%meas是3组样本，每组样本有50个数据，每个数据有4种属性值

X=meas';
%将meas倒置，将属性值(要分类的对象)作为行

BX=zscore(X);
%将标准化的值放入到BX中
%标准化有利于进行统一量级

Y=pdist(X); 
%pdist是寻找变量之间的相似性
%pdist返回成对观测值之间的两两距离（欧几里得距离即绝对距离）
%X是m*n的矩阵，生成的Y就是1*m*(m-1)/2的矩阵，

D=squareform(Y);
%将Y转换成沿对角线为0的对称矩阵
%按列的顺序转化，这一步也不是必须的

Z=linkage(Y);
%linkage是用于定义变量之间的连接
%linkage(Y,'method')是用‘method’参数指定的算法计算系统聚类树
%method为single,表示最短距离，一般是默认的；若为complete，则是最长距离;average表示平均距离；centroid表示重心距离；ward表示离差平方和
%Z生成的是(m-1)*3的矩阵，最先合并的作为m+1

cluster(Z,3);
%创建聚类，cluster(Z,'cutoff',c)结果存入到ans中;
%ans是一个长度为m的矢量，表示每一个测点所属的类别
%T是默认的顺序，根据不同的c值，对比ans和T的内容可以进行聚类分析
%c是将类进行划分的阈值，作用是对ans有影响

[H,T]=dendrogram(Z) 
% dendrogram用于画聚类图
%H是线
%可以在Z后面跟上，加数字改变横坐标
~~~


