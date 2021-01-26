# 1、主成分分析的实现-基于蓝莓品质判别
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
茶叶的数据是浙江大学生物系统工程与食品科学学院李晓丽老师给的。



