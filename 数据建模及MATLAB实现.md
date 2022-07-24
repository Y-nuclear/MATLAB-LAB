# 数据建模及MATLAB实现
随着信息技术的发展和成熟，各行业积累的数据越来越多，因此需要通过数据建模的方法，从看似杂乱的海量数据中找到有用的信息。
## 云模型
云模型由我国李德毅院士首创，属于不确定性人工智能范畴，主要用于定性和定量之间的相互转换。

云模型的基本单元被称为“云”或者“云滴”。“云”是指其在论域上的一个分布，可以用联合概率的形式$(x,\mu)$类比。

云模型通过三个数据来表示其特征：

* 期望：云滴在论域空间分布的期望，用Ex表示。
* 熵：不确定性程度，由离群程度和模糊程度共同决定，用En表示。
* 超熵：度量熵的不确定性，即熵的熵，用He表示

云有两种发生器：正向云发生器和逆向云发生器，
分别生成足够的云滴和计算云滴的云数字特征（$Ex,En,He$）。

正向云发生器的触发机制：

1. 生成以$En$为期望，以$He^2$为方差的正态随机数$En^{'}$。
2. 生成以$Ex$为期望，以$En^{'2}$为方差的正态随机数$x$。
3. 计算隶属度也就是确定度$\mu=exp(-\frac{(x-Ex)^2}{2En^{'2}})$,则$(x,\mu)$便是相对于论域$U$的一个云滴。
   
逆向云发生器的触发机制：

1. 计算样本均值$\overline{X}$和方差$S^{2}$。
2. $Ex=\overline{X}$
3. $En=\sqrt{\frac{\pi}{2}} \times \frac{1}{n}\Sigma |x-Ex|$
4. $He=\sqrt{S^2-En^2}$

### 云模型的MATLAB程序设计
云模型生成程序 cloud_model.m 如下
```
function [x,y,Ex,En,He]=cloud_model(y,n)
Ex=mean(y);
En=mean(abs(y-Ex))*sqrt(pi/2);
He=sqrt(var(y)-En^2);%var()返回方差值
for q = 1:n
    Enn=randn(1)*He+En;%生成方差为He^2,期望为En的正态随机数
    x(q)=randn(1)*Enn+Ex;
    y(q)=exp(-(x(q)-Ex)^2/(2*Enn^2));
end
```
云模型图像显示程序 DrawCloud.m 如下
```
function DrawCloud(Y,n)
[x,y,Ex,En,He] = cloud_model(Y,n);
plot(x,y,'r.');
xlabel('X/单位');
ylabel('Y/单位');
disp(['Ex = ',num2str(Ex)]);
disp(['En = ',num2str(En)]);
disp(['He = ',num2str(He)]);
```
在评价时通过云模型的数字特征($Ex,En,He$)来比较判断

## Logistic回归 
在回归分析中，