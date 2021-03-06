# R语言基础入门之四：常用的统计推断
通常一个研究项目能够获得的数据是有限的，以有限的样本特征来推断总体特征就称为统计推断。推断又可细分为区间估计和假设检验，二者虽有区别，但却是一枚硬币的两面，之间有着紧密的关联。

## 1 对总体均值进行区间估计
假设我们从总体中抽得一个样本，希望根据样本均值判断总体均值的置信区间，如下例所示：

```r
x=rnorm(50,mean=10,sd=5)  #随机生成50个均值为10，标准差为5的随机数为作为研究对象
mean(x)-qt(0.975,49)*sd(x)/sqrt(50)  #根据统计学区间估计公式，得到95%置信度下的区间下界
mean(x)+qt(0.975,49)*sd(x)/sqrt(50)  #95%置信度下的区间上界
```

也可以直接利用R语言内置函数t.test

```r
t.test(x,conf.level=0.95)
```

从如下结果可得95%置信区间为（9.56，12.36）

    One Sample t-test
    data:  x
    t = 15.7301, df = 49, p-value < 2.2e-16
    alternative hypothesis: true mean is not equal to 0
    95 percent confidence interval:
      9.563346 12.364729
    sample estimates:
    mean of x
     10.96404 

## 2 对总体均值进行假设检验
还是以上面的X数据作为对象，来检验总体均值是否为10

```r
t.test(x,mu=10,alternative='two.sided')  #这里的原假设是总体均值（mu）为10，使用双侧检验，得到P值为0.17，可见P值不够小，不能够拒绝原假设。
```

T检验是极为常用的检验方法，除了单样本推断之外，t.test命令还可以实现两样本推断和配对样本推断。如果要对总体比率或总体方差进行推断，可以使用prop.test和var.test。

## 3 正态分布检验
T检验的前提条件是总体服从正态分布，因此我们有必要先检验正态性。而且在评价回归模型时，对残差也需要检验正态性。检验正态性的函数是shapiro.test

```r
shapiro.test(x)
```

结果所下：

    Shapiro-Wilk normality test
    data:  x
    W = 0.9863, p-value = 0.8265

该检验的原假设是服从正态分布，由P值为0.82可判断不能拒绝总体服从正态的假设

## 4 非参数检验
如果总体不服从正态分布，那么T检验就不再适用，此时我们可以利用非参数方法推断中位数。wilcoxon.test函数可实现符号秩检验。

```r
wilcox.test(x,conf.int=T)   #指定conf.int让函数返回中位数的置信区间
wilcox.test(x,mu=1）  #指定mu让函数返回中位数为10的检验结果
```
    
## 5 独立性检验（联列表检验）
卡方分布有一个重要应用就是根据样本数据来检验两个分类变量的独立性，我们以CO2数据为例来说明chisq.test函数的使用，help(CO2)可以了解更多信息。

```r
data(CO2)   #读入内置的数据包，其中Type和Treatmen是其中两个分类变量。
chisq.test(table(CO2$Type,CO2$Treatment))   #使用卡方检验函数来检验这两个因子之间是否独立
```

结果显示P值为0.82，因此可以认为两因子之间独立。在样本较小的情况下，还可以使用fisher精确检验，对应的函数是fisher.test。 