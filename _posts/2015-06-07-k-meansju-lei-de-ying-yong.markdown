---
layout: post
title: "K-means聚类的应用"
date: 2015-05-31 19:02
comments: true
categories: [Data mining,R,Kmeans]
---

#K-means 简介
* K-means算法（k-means clustering）：一种适用于大样本的无监督式的聚类分析方法。
* 我对算法基本原理的理解：
 *  1.随机初始化k个聚类中心，也可以指定聚类中心。
 *  2.计算样本与聚类中心的距离，将样本划分到最近的聚类中心的类里。
 *  3.划分完毕后，计算每个类新的聚类中心，可以采用不同算法计算。
 *  4.如果新的聚类中心没有变化，算法结束；如果有变化，goto 2、3。

# K-means 应用
* 由于它流行于数据挖掘领域，常用来探索未知客群的结构。
* 在划分问题中，作为预处理工作，划分出了大致类别，然后可探究类内特性和差异。
* 其他：可以用作一种剔除算法、[向量的量化、特征学习][1]。

# K-means 的 R 实践

* 使用R语言使用K-means算法快捷方便。

<!-- more -->

```js

# 初始化数据 
m <- rbind(
matrix(rnorm(100, sd = 0.6), ncol = 2), # 标准正态分布 
matrix(rnorm(100, mean = 2, sd = 0.6), ncol = 2),
matrix(rnorm(100, mean = 4, sd = 0.6), ncol = 2),
matrix(rnorm(50, mean = -3, sd = 0.6), ncol = 2)
)
colnames(m) <- c("x", "y") 

m  <- apply(m,2,scale) # 标准化
cl <- kmeans(m, 4) # 使用Kmeans进行聚类分析

cl

cl$size  # 聚类每个分组的数量
cl$totss # The total sum of squares.
cl$withinss # Vector of within-cluster sum of squares
cl$centers  # 聚类中心
1-(cl$tot.withinss/cl$totss)  #1-(sum(cl$withinss)/cl$totss)

############################
# K-means 确定组数1

png("Kmeans_group_1.png") # 输出图像到png文件

wss <- (nrow(m)-1)*sum(apply(m,2,var))

for(i in 2:20)
    wss[i] <- sum(kmeans(m,centers=i)$withinss)

plot(1:20,wss,type="b",xlab="No. Clusters",
		ylab="Within groups sum of squares",
		main="Kmeans Centers Method 1")
identify(wss)
dev.off()

#  K-means 确定组数2

png("Kmeans_group_2.png")

wt <- c()
for(i in 1:20){
	ks <- kmeans(m,centers=i)
	wt[i] <- (1 - (ks$tot.withinss / ks$totss))
}
plot(1:20,wt,type="b",xlab="No. Clusters",
		ylab="tot withinss / totss",
		main="Kmeans Centers Method 2")
abline(h=0.9);identify(wt)

dev.off()

############################
# 找出内部点

resid.m <- m - fitted(cl)
# 计算 样本与对应中心点的差
# cluster centers "fitted" to each obs.

## 计算距离
distance <- function(x){sqrt(x[1]^2+x[2]^2)} 
dis <- apply(resid.m,1,distance)

# 将距离与样本整合在一起
m <- as.data.frame(cbind(m,cl$cluster,dis))
colnames(m) <- c("x", "y","cluster","dis")

inpoint <- c()

# 筛选出每个类中内部的样本 小于1.2倍的类内平均距离
for (i in 1:length(cl$size)){
	
	# print(i)

	tm <- m[which(m$cluster == i),]

	means <- mean(tm$dis) #每群的平均距离

	tpoint <- tm[which(tm$dis <= 1.2*means),]
	# <每群的平均距离,在类内部

	inpoint <- rbind(inpoint,tpoint)

}

inpoint <- inpoint[,c("x","y")]
m <- m[,c("x","y")]


png("Kmeans_inside.png")

# 设置背景颜色
par(bg = "azure")

# 画出聚类样本
plot(m,col = cl$cluster,main="K均值聚类结果与类内聚集点")

# 画出样本中心
points(cl$centers, col = 1:length(cl$size), pch = 8, cex = 5)

# 画出内部点
points(inpoint, col = 1:nrow(inpoint), pch = 1, cex = 2)

dev.off()

```

* 确定分类组数方法：
* ![确定组数方法1](/images/Kmeans_group_1.png)
* ![确定组数方法2](/images/Kmeans_group_2.png)

# 其他应用
* 最后找出内部点中，可以用作大样本快速SVM的样本筛选方法
* 因为支持向量只由超平面决定，而样本外部的点才能影响SV
* 所以可以将内部的点剔除而加快SVM的计算效率
* 如下图没有圈中的可作为训练样本，剔除可按需要进行
* ![K均值聚类结果与类内聚集点](/images/Kmeans_inside.png)


[1]:http://zh.wikipedia.org/zh/K-平均算法









