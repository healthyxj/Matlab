# 1、安德森鸢尾花花卉采集
matlab自带的安德森鸢尾花花卉数据集包括三类样本、每类50个数据，共150个数据。每个数据包含4个属性：萼片长度、萼片宽度、花瓣长度、花瓣宽度
~~~
%导入数据
load fisheriris
%会创建species（150*1，里面的元素是setosa，versicolor，virginica三种花的类型）和meas（150*4，每种花对应的参数值）

gscatter(meas(:,1), meas(:,2), species,'rgb','osd');
%gscatter绘制散点图，gscatter(a,b,group,clr,siz)括号里分别代表x坐标、y坐标，要分类的样本、颜色、形状
%这里是取meas的第1、2列作为x、y坐标，按照species绘制散点图
xlabel('Sepal length');
ylabel('Sepal width');

%进行判别分析
sample=meas([31:50,81:100,131:150],:); %取meas中的第31到50行，81到100行，131到150行的所有内容作为样本
training=meas([1:30,51:80,101:130],:); 
group1=species([1:30,51:80,101:130],:); %取species中的
group2=species([31:50,81:100,131:150],:); 
linclass = classify(sample,training,group1); 
%用training和group1构造判别函数，再应用到sample中，最后将结果保存到linclass
bad = ~strcmp(linclass,group2); 
%strcmp用于比较括号里的值，两者值相等，返回1
numobs = size(meas,1);  %求出行数
sum(bad) / numobs  %sum返回数组元素的之和,因此这里计算的是出错率

%绘制bad data
hold on;    %保留图形
plot(meas(bad,1), meas(bad,2), 'kx');   %可以直接用bad的值带入到meas的行中绘制bad data
hold on; 

%绘制判别结果
[x,y] = meshgrid(4:.1:8,2:.1:4.5); 
%按照括号里的向量x，y构建网格，逗号前是x，这里是4到8以0.1为间隔
x = x(:); 
y = y(:); 
j = classify([x y],meas(:,1:2),species);
gscatter(x,y,j,'grb','sod')
~~~
# 2、求马氏距离
||x1|x2|
|:--:|:--:|:--:|
|1|2|1|
|2|4|4|
|3|5|5|
|4|9|7|

判断A(-8,-6)属于哪一类总体
~~~
X1=[2;4;5;9];
X2=[1;4;5;7];
X=[X1,X2];
mX=mean(X)';
x=[-8;-6];
w=cov(X);  %协方差用cov函数来求
d=(x-mX)' / w * (x-mX);
~~~
>注意：求出来的是马氏距离的平方
# 3、对鸢尾花花卉进行fisher判别分析
直接根据定义做出投影轴
~~~
X1 = normrnd(40,10,[200,1]); 
%normrnd-正态随机数，在40为均值，10为标准差，[200,1]表示200*1
Y1 = normrnd(40,10,[200,1]); 
w1=[X1, Y1];    %代表矩阵的拼接
X2 = normrnd(5 ,10,[100,1]); 
Y2 = normrnd(0 ,10,[100,1]); 
w2=[X2, Y2];
plot(X1,Y1,'.r',X2,Y2,'.b');

%计算各样本的均值，最终用列向量表示
m1 = mean(w1)';
m2 = mean(w2)';

%计算第一二类样本的类内离散度
s1=zeros(2); 
[row1,colum1]=size(w1); %将w1的行列数拷贝到row和colum中
for i=1:row1 
    s1 = s1 + (w1(i,:)'-m1)*(w1(i,:)'-m1)'; 
end; 
s2=zeros(2); 
[row2,colum2]=size(w2); 
for i=1:row2 
    s2 = s2 + (w2(i,:)' - m2)*(w2(i,:)' - m2)';
end;

%计算类内总离散度
Sw=s1+s2; 
%计算fisher准则取最大值时的解
w=inv(Sw)*(m1-m2);  %w就是投影目标向量
%计算阈值w0
ave_m1 = w'*m1; 
ave_m2 = w'*m2;
w0 = (ave_m1+ave_m2)/2;

figure(1) 
plot(X1,Y1,'.r',X2,Y2,'.b');    %画出两类样本点
hold on; 
grid; 
%画出满足极大值时的投影向量
x = [-40:0.1:40]; 
y = x*w(2)/w(1);
plot(x,y,'g')
~~~


