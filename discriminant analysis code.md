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
# 4、用fisher判别分析进行二分类
为了解某河段As、Pb污染状况，设在甲、乙两地监测，采样测的这两种元素在水中和底泥中的浓度如下表，依据数据判断未知样本是从哪个区域采得的。
|地点|样品号|水体As|水体Pb|底泥As|底泥Pb|
|:--:|:--:|:--:|:--:|:--:|:--:|
|甲地|1|2.79|7.8|13.85|49.6|
|甲地|2|4.67|12.31|22.31|47.8|
|甲地|3|4.63|16.81|28.82|62.15|
|甲地|4|3.54|7.58|15.29|43.2|
|甲地|5|4.9|16.12|28.29|58.7|
|乙地|1|1.06|1.22|2.18|20.6|
|乙地|2|0.8|4.06|3.85|27.1|
|乙地|3|0|3.5|11.4|0|
|乙地|4|2.42|2.14|3.66|15|
|乙地|5|0|5.68|12.1|0|
|未知样品|1|2.4|14.3|7.9|33.2|
|未知样品|2|5.1|4.43|22.4|54.6|

~~~
%甲总体的
jsa=[2.79 4.67 4.63 3.54 4.9]';
jsp=[7.8 12.31 16.81 7.58 16.12]';
jda=[13.85 22.31 28.82 15.29 28.29]';
jdp=[49.6 47.8 62.15 43.2 58.7]';
j=[jsa,jsp,jda,jdp];

%乙总体的
ysa=[1.06 0.8 0 2.42 0]';
ysp=[1.22 4.06 3.5 2.14 5.68]';
yda=[2.18 3.85 11.4 3.66 12.1]';
ydp=[20.6 27.1 0 15 0]';
%y=[ysa,ysp,yda,ydp];
y = [ysa ysp yda ydp];

mj=mean(j)';
my=mean(y)';

%计算d,算出类间距离
d=mj-my;

%根据协方差计算s，s=协方差乘以(样本数-1)
sj=cov(j)*4;
sy=cov(y)*4;
s=sj+sy;    %计算总的类内离散度

%求出c以构建判别函数
%c=inv(s)*d;    系统提示用\能够提高速度
c=s\d;

yj=j*c;
yy=y*c;
myj=mean(yj);
myy=mean(yy);

%求出临界值
y0 = (5*myj+5*myy)/(5+5);

sample1=[2.4 14.3 7.9 33.2];
sample2=[5.1 4.43 22.4 54.6];

%将样品值带入到判别函数中
y1=sample1*c;
y2=sample2*c;

if myy < myj
    if y1>y0
        disp("1样本属于甲地")
    else 
        disp("1样本属于乙地")
    end
    if y2>y0
        disp("2样本属于甲地")
    else 
        disp("2样本属于乙地")
    end
else
    if y1>y0
        disp("1样本属于乙地")
    else
        disp("1样本属于甲地")
    end
    if y2>y0
        disp("2样本属于乙地")
    else 
        disp("2样本属于甲地")
    end
end
~~~





