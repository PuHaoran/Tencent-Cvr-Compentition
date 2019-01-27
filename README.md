# Tencent-Cvr-Compentition

CVR (Conversion Rate): 转化率。是一个衡量CPA广告效果的指标，CVR=(转化量/点击量)*100%。

从社交广告系统中某一连续两周的日志中按照推广中的App和用户维度随机采样，训练样本为广告日志，点击后时候发生转化为0和1，然后预测随后一天指定用户和对应广告的转化率pCVR=P(conversion=1 | Ad,User,Context)，即给定广告、用户和上下文情况下广告被点击后发生激活的概率。这是一个典型的不均衡样本下的分类问题，可以采用分类模型进行建模。

为何要做广告转化率的实时预估？

因为这是广告排序和计费的基础。广告平台会按期望收益对广告进行排序。而广告的期望收益等于广告主出价（Bid）和广告预估转化率的乘积。

## 评估方式

交叉熵损失

![](http://qzonestyle.gtimg.cn/gdt/canvas/Starry/public/image/formula-1.png)

数值越小，说明模型效果越好。

## 模型简介

1. 基于ad的方法

根据训练集对某个appID平均转化率来确定测试集用户对该appID的平均转化率。
缺点：影响广告转化的因素有很多，只考虑广告的点击和转化次数，忽略其他特征的做法会影响准确率。比如：一个卖女装的广告，展现给男性和女性的转化率是不同的。不能一概使用 `转化量/点击量` 来做估计。

2. ad+LR版本

对素材ID、广告ID、推广计划ID、账户ID、APPID、App平台等ID类特征做OneHot编码，然后采用LR模型进行分类预测。

3. RandomForest版本

做了更多的特征工程，根据已有数据在user表上提取了更多特征，比如常住地省、市，籍贯省、市，对年龄分桶，更细粒度的时间等。

由于这里正负样本数量相差很大，数据严重不均衡，所以我们采用bagging进行修正。bagging分类器是一个集成分类器，首先使用基分类器得出部分数据集得出多个预测结果，然后采用投票或平均来提高最后的预测准确率。