# 数据建模及MATLAB实现(二)
随着信息技术的发展和成熟，各行业积累的数据越来越多，因此需要通过数据建模的方法，从看似杂乱的海量数据中找到有用的信息。
## 主成分分析（PCA）
在数学建模中，经常会遇到研究多个变量的问题。当变量数量较多且变量之间存在复杂关系时，会显著增加分析问题的复杂性。

为了解决这种问题，科学家提出了主成分分析（Principal Component Analysis， PCA）的方法。
PCA是一种数学降维方法，其主要目的是将原来众多具有一定相关性的变量，重新组合为一组新的相互无关的综合变量。

通常，数学上的处理方法就是将原来的变量做线性组合，作为新的综合变量。通常，数学上的将选取的第一个线性组合即第一个综合变量记为$F_1$,而$F_1$需要尽可能多的反映原来变量的信息。信息通过方差来表示，则希望$var(F_1)$尽可能的大，表示$F_1$包含的信息尽可能的多。因此$F_1$应该是线性组合中方差最大的。

而如果第一主成分不能代表所有变量的信息，则需要第二主成分、第三主成分···，而前者已有的信息则不需要出现在后者中。用数学的方式表达则为$cov(F_1,F_2)=0$,称$F_2$为第二主成分（$cov()$为数学中的协方差）。

### 主成分分析的典型步骤
1. 对原始数据进行标准化处理。
   
   对于观测数据矩阵$X$，有
   $$X=\begin{bmatrix}
    x_{11}&x_{12}&···&x_{1p}\\
    x_{21}&x_{22}&···&x_{2p}\\
    \vdots&\vdots&\ddots&\vdots\\
    x_{n1}&x_{n2}&···&x_{np}\\
   \end{bmatrix}$$
   可以按照如下方法对原始数据进行标准化处理：
   $$
   x_{ij}^{*}=\frac{x_{ij}-\overline{x_j}}{\sqrt{Var(x_j)}}
   
   \ \ \ \ \ \ \ \ 
   (i=1,2,\cdots,n;j=1,2,\cdots,p)
   $$
   其中，
   $$
   \overline{x_{j}}=\frac{1}{n}\underset{i=1}{\overset{n}{\Sigma}}x_{ij};Var(x_j)=\frac{1}{n-1}\overset{n}{\underset{i=1}{\Sigma}}(x_ij-\overline{x_j})^2\\
   (j=1,2,\cdots,p)。
   $$
2. 计算样本相关系数矩阵。
   
   在相关系数矩阵中，有用到协方差的概念，具体概念见<a href='https://baike.baidu.com/item/%E5%8D%8F%E6%96%B9%E5%B7%AE/2185936?fr=aladdin'>百度百科-协方差</a>。而相关系数即是标准化后的协方差的值。
    $$
    R=
    \begin{bmatrix}
        r_{11}&r_{12}&\cdots&r_{1p}\\
        r_{21}&r_{22}&\cdots&r_{2p}\\
        \vdots&\vdots&\ddots&\vdots\\
        r_{p1}&r_{p2}&\cdots&r_{pp}\\
    \end{bmatrix}
    $$
    其中，
    $$
    r_{ij}=cov(x_i,x_j)=\frac{\overset{n}{\underset{k=1}{\Sigma}}(x_{ki}-\overline{x_i})(x_{kj}-\overline{x_j})}{n-1}
    $$
3. 计算相关系数矩阵中的特征值和相应的特征向量
   
   特征值：$\lambda_1,\lambda_2,\cdots,\lambda_p$（特征值即方差值）

   特征向量$a_i=(a_{i1},a_{i2},\cdots,a_{ip})^T,i=1,2,\cdots,p$
4. 选择重要的主成分，并写出主成分表达式
   
   由主成分分析得到p个主成分，但由于主成分的方差是递减的，所以在实际分析的过程中，一般不是选取p个主成分，而是各个主成分累计贡献率的大小选取前$k$个主成分。

   贡献率即某个主成分的方差占全部方差合计的比重：
   $$
   贡献率=\frac{\lambda_i}{\overset{p}{\underset{i=1}{\Sigma}}\lambda_i}\times100\%
   $$
   贡献率越大，说明该主成分所包含的原始变量的信息越多。一般k个主成分需要累计贡献率在80%或85%以上，这样才能保证综合变量能包括原始变量的大部分信息。
5. 计算主成分得分
   根据标准化的原始数据，按照各个样品，分别代入主成分表达式，就可以得到主成分下的各个样品的新数据，即主成分得分。具体形式如下：
   $$
   \begin{bmatrix}
    F_{11}&F_{12}&\cdots&F_{1k}\\
    F_{21}&F_{22}&\cdots&F_{2k}\\
    \vdots&\vdots&\ddots&\vdots\\
    F_{n1}&F_{n2}&\cdots&F_{nk}\\
   \end{bmatrix}
   $$
### 主成分分析MATLAB程序设计
以下为主成分分析的MATLAB程序PCA.m
```
function [F,new_score]=PCA(A,T)
a=size(A,1);
b=size(A,2);
% 数据标准化处理
for i=1:b
    SA(:,i)=(A(:,i)-mean(A(:,i)))/std(A(:,i));
end
% 计算相关系数矩阵的特征值和对应的特征向量
CM=corrcoef(SA);
[V,D]=eig(CM);
for j=1:b
    DS(j,1)=D(b+1-j,b+1-j);
end
for i=1:b
    DS(i,2)=DS(i,1)/sum(DS(:,1));
    DS(i,3)=sum(DS(1:i,2));
end
%保留k个主成分
for K=1:b
    if(DS(K,3)>=T)
        Com_num=K;
        brek;
    end
end
%提取主成分对应的特征向量
for i=1:Com_num
    F(:,i)=V(:,b+1-i);
end
new_score=SA*F;


```