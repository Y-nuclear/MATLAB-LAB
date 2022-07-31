# 数据建模及MATLAB实现(四)
随着信息技术的发展和成熟，各行业积累的数据越来越多，因此需要通过数据建模的方法，从看似杂乱的海量数据中找到有用的信息。
## K-均值(K-Means)
将物理或者抽象对象的集合分成由<strong>类似</strong>的对象组成的多个类的过程称为聚类。由聚类所生成的簇是一组数据对象的集合，这些对象与同一个簇内的对象彼此相似，与其他簇内的对象相异。

K-均值(K-Means)聚类算法是著名的划分聚类分割的办法。划分的基本思路是：$\\$
1. 对于一个给定的有$N$个元组或记录的数据集，首先构造$K$个分组，每一个分组代表一个聚类，$K<N$。(对于这K个分组需要满足：每一个分组至少包含一个数据记录；一个数据记录仅属于一个分组)
2. 通过反复迭代的方法改变分组，使得每一次改进的分组方案都较前一次好。好的标准就是同一分组中的记录越来越接近，不同的分组中的记录越来越远。
### K-均值算法原理
1. 首先随机从数据中选取$K$个点，每个点初始地代表每个簇的聚类中心。
2. 然后计算剩余每个样本到聚类中心的距离，将它赋给最近的簇。
3. 重新计算每一个簇的平均值。
4. 重复2，3直至满足某个终止条件：
   
    1.没有对象被重新分配给不同的聚类$\\$
    2.聚类中心不再发生变化$\\$
    3.误差平方和局部最小
### K-均值聚类MATLAB程序设计
```
function [z1]=K_Means(x,k)
[m,n]=size(x);%x是坐标点
z=zeros(k,n);
z1=zeros(k,n);
for i=1:k
    for j=1:n
        z(i,j)=x(i,j);
    end
end
while 1
    count=zeros(k,1);%类型个数
    allsum=zeros(k,n);%距离矩阵
    for i =1:m
        min_Distance=inf;
        index=0;
        for j=1:k
            distance=0;
            for t=1:n
                distance=distance+(x(i,t)-z(j,t))^2;
            end
            if (min_Distance > distance)
                min_Distance = distance;
                index=j;
            end
        end
        count(index)=count(index)+1;
        for t=1:n
            allsum(index,t)=allsum(index,t)+x(i,t);
        end
    end
    for i =1:k
        for j=1:n
            z1(i,j)=allsum(i,j)/count(i);
        end
    end
    if (z==z1)
        break;
    else
        z=z1;
    end
end
end
```