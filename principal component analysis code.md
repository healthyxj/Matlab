# 1、主成分分析的实现-基于蓝莓品质判别
用函数pca来计算主成分

[COEFF,SCORE,Latent,Tsquare] = pca(X)
* X是一个n*p矩阵，<b>行代表的是样本</b>，列代表的是变量
* <b>COEFF是一个p*p矩阵，代表主成分系数(载荷系数)，每一列对于一个主成分的载荷系数</b>。列对应的主成分方差是递减排列的。
* SCORE是主成分得分，代表主成分空间中的X，行代表样本，列代表主成分。
* <b>Latent是由X的协方差矩阵的特征值组成的向量</b>
* Tsquare是由每个样本的Hotelling ' sT²统计值组成

使用函数cumsum来计算累计方差，累积贡献率

数据参照:[1]韩斯,孟宪军,汪艳群,李斌,李冬男.不同品种蓝莓品质特性及聚类分析[J].食品科学,2015,36(06):140-144.
~~~matlab
%导入blueberry的数据文件,数据存储在X
load blueberry.mat

%计算主成分得分
[COEFF,SCORE,Latent,Tsquare]=pca(X);
B=cumsum(Latent./sum(Latent) * 100);
%B是用于计算累积贡献率的

%利用累积贡献率看出可以选择前几个主成分作为最后的分析对象
plot(1:5,B(1:5),'-bo'); 
xlabel('Number of principal components');
ylabel('Percent Variance Explained in X');
~~~
以上是计算累积贡献率，可以选择三个主成分作为分析;下面这一段是用于作图查看主成分分析的效果
~~~matlab
load blueberry.mat

[COEFF,SCORE,Latent,Tsquare]=pca(X);
B=cumsum(Latent./sum(Latent) * 100);
plot(1:10,B(1:10),'-bo'); 
xlabel('Number of principal components');
ylabel('Percent Variance Explained in X');

scatter3(SCORE(1,1),SCORE(1,2),SCORE(1,3),'bo');
hold on;
scatter3(SCORE(2,1),SCORE(2,2),SCORE(2,3),'go');
hold on;
scatter3(SCORE(3,1),SCORE(1,2),SCORE(3,3),'ko');
hold on;
scatter3(SCORE(4,1),SCORE(4,2),SCORE(4,3),'ro');
hold on;
scatter3(SCORE(5,1),SCORE(5,2),SCORE(5,3),'b+');
hold on;
scatter3(SCORE(6,1),SCORE(6,2),SCORE(6,3),'g+');
hold on;
scatter3(SCORE(7,1),SCORE(7,2),SCORE(7,3),'k+');
hold on;
scatter3(SCORE(8,1),SCORE(8,2),SCORE(8,3),'r+');
hold on;
scatter3(SCORE(9,1),SCORE(9,2),SCORE(9,3),'b*');
hold on;
scatter3(SCORE(10,1),SCORE(10,2),SCORE(10,3),'g*');
hold on;
scatter3(SCORE(11,1),SCORE(11,2),SCORE(11,3),'k*');
hold on;
scatter3(SCORE(12,1),SCORE(12,2),SCORE(12,3),'r*');
hold on;
scatter3(SCORE(13,1),SCORE(13,2),SCORE(13,3),'bx');
hold on;
scatter3(SCORE(14,1),SCORE(14,2),SCORE(14,3),'gx');
hold on;

xlabel('First Principal Component'); 
ylabel('Second Principal Component');
zlabel('Third Principal Component');
title('Principal Component Scatter Plot');
legend('37-1','B','H34','L5','Z','埃利奥特1','埃利奥特2','奥尼尔','北陆','布尔吉塔','杜克','蓝丰','蓝塔1','蓝塔2');
~~~
# 2、主成分分析的实现-基于茶叶
茶叶的数据是浙江大学生物系统工程与食品科学学院李晓丽老师给的。茶叶数据是8类茶叶，每类茶叶30个样本的波长吸收率。

## demo1
~~~matlab
load tea.mat

%将第一列表示波长的列提出，转置原矩阵使之符合princomp函数的要求
X=T(:,2:241)';

[COEFF,SCORE,Latent,Tsquare]=pca(X);
%COEFF代表载荷系数；SCORE代表主成分得分；Latent代表特征值组成的向量

B=cumsum(Latent./sum(Latent) * 100);
%cumsum按类进行递加
%要对精度分析，利用贡献率反应某个指标所占比重

plot(325:1075,T(1:751,2:241));
xlabel('Wavelength(nm)'); 
ylabel('Absorption( )');
title('Wavelength diagram from initial data');

scatter3(SCORE(1:30,1),SCORE(1:30,2),SCORE(1:30,3),'bo');
hold on;
scatter3(SCORE(31:60,1),SCORE(31:60,2),SCORE(31:60,3),'go');
hold on;
scatter3(SCORE(61:90,1),SCORE(61:90,2),SCORE(61:90,3),'ko');
hold on;
scatter3(SCORE(91:120,1),SCORE(91:120,2),SCORE(91:120,3),'ro');
hold on;
scatter3(SCORE(121:150,1),SCORE(121:150,2),SCORE(121:150,3),'b+');
hold on;
scatter3(SCORE(151:180,1),SCORE(151:180,2),SCORE(151:180,3),'g+');
hold on;
scatter3(SCORE(181:210,1),SCORE(181:210,2),SCORE(181:210,3),'k+');
hold on;
scatter3(SCORE(211:240,1),SCORE(211:240,2),SCORE(211:240,3),'r+');
hold on;
~~~
对结果分析可知，主成分分析后，并没有对茶叶进行很好的区分，原因是对茶叶没有进行噪音的去除，以及未进行标准化变换。可以通过作图来观察，看见一开始和结尾部分都有波动，即噪音。
~~~matlab
plot(325:1075,T(1:751,2:241))
~~~
## demo2
由前面可知，<b>要去除噪音和标准化变换</b>
~~~matlab
load tea.mat

X=T(:,2:241)';

[SX,MU,SIGMA]=zscore(X);

[COEFF,SCORE,Latent,Tsquare]=pca(SX);
%COEFF代表载荷系数；SCORE代表主成分得分；Latent代表特征值组成的向量

B=cumsum(Latent./sum(Latent) * 100);
%cumsum按类进行递加
%要对精度分析，利用贡献率反应某个指标所占比重

plot(1:10,B(1:10),'-bo'); 
xlabel('Number of principal components');
ylabel('Percent Variance Explained in X');


plot(400:1000,SX(:,76:676));
xlabel('Wavelength(nm)'); 
ylabel('Absorption( )');
title('Normalized data after noise removal');
%plot(325:1075,SX(:,:));

scatter3(SCORE(1:30,1),SCORE(1:30,2),SCORE(1:30,3),'bo');
hold on;
scatter3(SCORE(31:60,1),SCORE(31:60,2),SCORE(31:60,3),'go');
hold on;
scatter3(SCORE(61:90,1),SCORE(61:90,2),SCORE(61:90,3),'ko');
hold on;
scatter3(SCORE(91:120,1),SCORE(91:120,2),SCORE(91:120,3),'ro');
hold on;
scatter3(SCORE(121:150,1),SCORE(121:150,2),SCORE(121:150,3),'b+');
hold on;
scatter3(SCORE(151:180,1),SCORE(151:180,2),SCORE(151:180,3),'g+');
hold on;
scatter3(SCORE(181:210,1),SCORE(181:210,2),SCORE(181:210,3),'k+');
hold on;
scatter3(SCORE(211:240,1),SCORE(211:240,2),SCORE(211:240,3),'r+');
hold on;
xlabel('PC1'); 
ylabel('PC2');
zlabel('PC3');
~~~

