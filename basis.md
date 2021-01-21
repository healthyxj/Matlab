# 一、简单了解
matlab社区与国内论坛
* https://ww2.mathworks.cn/matlabcentral/?s_tid=gn_mlc
* http://www.matlabsky.com/
* https://www.ilovematlab.cn/
~~~
注意事项
在社区提问时，要再现问题（完整的错误信息、源程序文件、数据等）；
帖子标题最好简要说明问题的本质，不要出现跪求，大神等词语
~~~

版本与选择
matlab一般半年一个版本，一年两个版本，分为a和b。若只是算法设计、模拟仿真、各版本差别不大；
若涉及到simulink，最好用最新版本。

开发环境
首先是路径的选择，然后有预览、命令行、工作空间、菜单栏等

---
# 二、使用matlab
## 变量命名规则
* 区分大小写
* 长度不超过63位
* 字母开头，只可以由字母、数字、下划线组成，不能使用标点
* 变量名应简洁明了，能够通过变量名直观看出物理意义

基本数据类型由数字、字符和字符串、数组、矩阵、元胞数组、结构体等

可以用两个%加一个空格对代码程序进行分段和分类；一个%用于注释；
可以用ctrl+r对段落进行注释，用ctrl+t取消注释

~~~
clear all
clc
%可以清除所有变量和command中的所有命令

%可以按下F9对选中的片段进行执行，这点很不错

%字符用单引号表示；abs字符返回ASCII码值，char字符返回字符
str='I love MATLAB & Machine Learning.'
length(str)
~~~

---
# 三、矩阵操作
矩阵中分号表示换行，用逗号和空格表示连续在同一行。matlab中可以直接定义一个矩阵。

## demo time
~~~
%转置矩阵在原矩阵后加一个'即可
B=A';

%将矩阵转换为列向量
C=A(:);

%用inv求矩阵的逆
D=inv(A);

A*D;

%对三维矩阵进行赋值
E=zeros(10,5,3);
%对三维数组的第一维进行赋值，rand是生成0到1的随机数
E(:,:,1)=rand(10,5)
%randi是生成介于1到i的伪随机整数randi(i,m,n);
E(:,:,2)=randi(5,10,5)
%randn返回正态分布中的伪随机标量
E(:,:,3)=randn(10,5)

%定义元胞数组,每个维度可以不同
F=cell(1,6)
F{5}=magic(5)

%结构体
books = struct('name',{{'Machine Learning','Data Mining'}},'price',[30 40])
books.name
%前者返回元胞数组，后者返回字符串的形式
books.name(1)
books.name{1}

%矩阵的构造，用冒号表示等差，repmat复制，后面跟的是复制的维度
G=1:2:9;
H=repmat(G,3,1);

%矩阵的乘法，对应位置相乘是点乘，剩下的是矩阵的相乘。

%矩阵的下标
I=A(3,:);	%取出A中的第三行所有列，:表示所有

%查找,取出下标
[m,n]=find(A>20);
~~~

## 逻辑与流程控制
* 判断
~~~
% 1. if ... else ... end
A = rand(1,10)
limit = 0.75;

B = (A > limit);   % B is a vector of logical values
if any(B)
  fprintf('Indices of values > %4.2f: \n', limit);
  disp(find(B))
else
  disp('All values are below the limit.')
end
~~~
* for循环
~~~
% 2. for ... end
k = 10;
hilbert = zeros(k,k);      % Preallocate matrix

for m = 1:k
    for n = 1:k
        hilbert(m,n) = 1/(m+n -1);
    end
end
hilbert
~~~
* while循环
~~~
% 3. while ... end
n = 1;
nFactorial = 1;
while nFactorial < 1e100
    n = n + 1;
    nFactorial = nFactorial * n;
end
n

factorial(69)
factorial(70)

prod(1:69)
prod(1:70)
~~~

## 脚本文件与函数文件
脚本文件可以立即执行，函数文件适用于需要多次使用的场合
~~~
function output = myFunction(input)

switch input
    case -1
        output = 'negative one';
    case 0
        output = 'zero';
    case 1
        output = 'positive one';
    otherwise
        output = 'other value';
end

end
~~~

## 作图
~~~
%二维平面作图
x=0:0.01:2*pi;
y=sin(x);
plot(x,y);
title('y=sin(x)');
xlabel('x');
ylabel('sin(x)');
%设置横坐标范围为0到2Π
xlim([0 2*pi]);

%三维立体绘图
t = 0:pi/50:10*pi;
plot3(sin(t),cos(t),t)
xlabel('sin(t)')
ylabel('cos(t)')
zlabel('t')
grid on		%打开网格
axis square	%让坐标轴相等
~~~

## 文件导入
~~~
%mat格式,用文件名.mat 要保存的变量
save data.mat x y1 y2

%txt格式
M=importdata('myfile.txt');	%导入的是结构体格式
S=M.data;

%xls or xlsx格式
xlswrite('data.xls',S);
M=xlsread('data.xls');
isequal(S,W);	%判断两者是否相等，相等返回1
~~~

# 三、进阶
要会使用cell mode模式，即用两个%加一个空格的形式。这样子就能够便于程序的发布(publish).
## 调试
在要开始调试的语句处的左边点击，会出现红点。按下F5就会进入调试模式，具体是在命令行栏出现K；按下F10，进入下一行；按shift+F5退出调试。
## 查看自带的工具箱函数 
edit or help  加函数名
varargin  变长度输入参数
varargout 变长度输出参数
nargin
## 内存优化配置
物理上：禁用java虚拟机；增加虚拟内存数量；打开系统3GB开关（32位系统）；
软件上：及时清除不用的变量；预分配内存；选择恰当的数据类型；循环与向量化 ；按列优先循环；循环次数多的变量安排在内层
* 预分配内存
~~~
n = 30000;
tic;
for k = 1:n
    a(k) = 1;
end
time = toc;
disp(['未预分配内存下动态赋值长为',num2str(n),'的数组时间是:',num2str(time),'秒！'])

tic
b = zeros(1,n);
for k = 1:n
    b(k) = 1;
end
time = toc;
disp(['预分配内存下动态赋值长为',num2str(n),'的数组时间是:',num2str(time),'秒！'])
~~~
## 图像对象和句柄
图像对象是用于界面制作和数据可视的基本绘图元素。
图形对象的属性由属性名和属性值两部分组成。句柄是图形对象的标识代码，句柄含有图形对象的各种必要的属性信息；根屏幕的句柄为0，图形窗口的句柄为整数，其他对象的句柄为浮点数。
~~~
%修改线条的属性，线性可以选择'-'or'--'or':'or'-.'or'none'
x = 0:0.01:2*pi;
y = sin(x);
h = plot(x,y);
grid on
get(h)
set(h,'linestyle','-','linewidth',2,'color','k')
%可以看出，是采用将图像赋值给某个变量，再对变量进行处理

%修改网格间隔
set(gca,'xtick',0:0.5:7)
set(gca,'ytick',-1:0.1:1)

%设置图例的字体及大小
x = 0:0.01:2*pi;
y1 = sin(x);
y2 = cos(x);
plot(x,y1,'r')
hold on
plot(x,y2,'-.b')
h = legend('sin(x)','cos(x)');
set(h,'fontsize',16,'color','k','edgecolor','r','textcolor','w')
%这里的color是指背景的颜色

%拆分图例
x = 0:0.01:2*pi;
y1 = sin(x);
y2 = cos(x);
h1 = plot(x,y1,'r');
hold on
h2 = plot(x,y2,'-.b');
ax1 = axes('position',get(gca,'position'),'visible','off');
legend(ax1,h1,'sin(x)','location','northwest')
ax2 = axes('position',get(gca,'position'),'visible','off');
legend(ax2,h2,'cos(x)','location','northeast')
~~~

