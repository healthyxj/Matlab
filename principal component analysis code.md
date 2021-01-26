# 1、主成分分析的实现
用函数princomp来计算主成分

[COEFF,SCORE,Latent,Tsquare] = princomp(X)
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

%计算主成分
[COEFF,SCORE,Latent,Tsquare]=pca(X);
B=cumsum(Latent./sum(Latent) * 100);
plot(1:5,B(1:5),'-bo'); 
xlabel('Number of principal components');
ylabel('Percent Variance Explained in X');
~~~

