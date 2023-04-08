# (PART) 基础统计分析 {-} 



# t检验 {#sdjfl}

t检验主要适用于1组或2组的均数的比较，要求数据符合正态性和方差齐性。关于t检验的本质问题本文不做过多探讨，只是学习如何用R语言实现t检验！

在R中进行t检验非常简单，就是`t.test()`函数，不管是单样本、两样本都是这一个函数。使用R语言进行统计学比SPSS好的一点是，当你需要使用某种检验时，直接打出这个检验的名字一般就会跳出来相关的函数，而我在学习使用SPSS进行各种统计检验时，可能更多的是关注如何点点点（鼠标点击菜单），不利于记住哪种情况使用什么方法。

## 单样本t检验

使用课本例3-5的数据。

首先是读取数据，可以自己录入，也可以使用课本光盘里的数据，我这里直接使用了光盘里的数据。


```r
# 使用foreign包读取SPSS数据
library(foreign)
df <- read.spss('datasets/例03-05.sav',to.data.frame = T)

head(df)
##   no  hb
## 1  1 112
## 2  2 137
## 3  3 129
## 4  4 126
## 5  5  88
## 6  6  90
```

进行单样本t检验，与140进行比较：


```r
st <- t.test(df$hb,mu=140,alternative = 'two.sided') # 双侧检验

st
## 
## 	One Sample t-test
## 
## data:  df$hb
## t = -2.1367, df = 35, p-value = 0.03969
## alternative hypothesis: true mean is not equal to 140
## 95 percent confidence interval:
##  122.1238 139.5428
## sample estimates:
## mean of x 
##  130.8333
```

结果显示t=-2.1367，自由度df=35，p=0.03969,和课本一致。

## 配对样本t检验

使用**课本例3-6**的数据，首先是读取数据。


```r
library(foreign)
df <- read.spss('datasets/例03-06.sav',to.data.frame = T)

head(df)
##   no    x1    x2
## 1  1 0.840 0.580
## 2  2 0.591 0.509
## 3  3 0.674 0.500
## 4  4 0.632 0.316
## 5  5 0.687 0.337
## 6  6 0.978 0.517
```

数据一共3列10行，第1列是样本编号，第2列和第3列是要比较的值。

进行配对样本t检验：


```r
pt <- t.test(df$x1,df$x2,paired = T,var.equal = T)
pt
## 
## 	Paired t-test
## 
## data:  df$x1 and df$x2
## t = 7.926, df = 9, p-value = 2.384e-05
## alternative hypothesis: true mean difference is not equal to 0
## 95 percent confidence interval:
##  0.1946542 0.3501458
## sample estimates:
## mean difference 
##          0.2724
```

结果显示t=7.926，自由度df=9，p<0.001，结果和课本一致。

如果数据格式是两列数据，R语言也支持formula的形式，比如这样：


```r
suppressMessages(library(tidyverse))

df.l <- df |>
  pivot_longer(2:3,names_to = "group",values_to = "x")

# 数据一列是分组，另一列是数值，还有一列id没什么用
df.l
## # A tibble: 20 x 3
##       no group     x
##    <dbl> <chr> <dbl>
##  1     1 x1    0.84 
##  2     1 x2    0.58 
##  3     2 x1    0.591
##  4     2 x2    0.509
##  5     3 x1    0.674
##  6     3 x2    0.5  
##  7     4 x1    0.632
##  8     4 x2    0.316
##  9     5 x1    0.687
## 10     5 x2    0.337
## 11     6 x1    0.978
## 12     6 x2    0.517
## 13     7 x1    0.75 
## 14     7 x2    0.454
## 15     8 x1    0.73 
## 16     8 x2    0.512
## 17     9 x1    1.2  
## 18     9 x2    0.997
## 19    10 x1    0.87 
## 20    10 x2    0.506
```

这时可以用formula形式：


```r
t.test(x ~ group, data = df.l, paired = T, var.equal = T)
## 
## 	Paired t-test
## 
## data:  x by group
## t = 7.926, df = 9, p-value = 2.384e-05
## alternative hypothesis: true mean difference is not equal to 0
## 95 percent confidence interval:
##  0.1946542 0.3501458
## sample estimates:
## mean difference 
##          0.2724
```

结果也是一模一样的。

>在R语言中，绝大多数统计检验函数都是支持多种形式的输入形式的，需要自己注意。

## 两样本t检验

使用课本例3-7的数据。

首先是读取数据.


```r
library(foreign)
df <- read.spss('datasets/例03-07.sav',to.data.frame = T)
df$group <- c(rep('阿卡波糖',20),rep('拜糖平',20))
attributes(df)[3] <- NULL

head(df)
##   no    x    group
## 1  1 -0.7 阿卡波糖
## 2  2 -5.6 阿卡波糖
## 3  3  2.0 阿卡波糖
## 4  4  2.8 阿卡波糖
## 5  5  0.7 阿卡波糖
## 6  6  3.5 阿卡波糖
```

一共有3列40行，第1列是编号，第2列是血糖值，第3列是组别（阿卡波糖组和拜糖平组，每组20个人）。

进行两样本t检验：


```r
tt <- t.test(x ~ group, data = df, paired = F, var.equal = T)
tt
## 
## 	Two Sample t-test
## 
## data:  x by group
## t = -0.64187, df = 38, p-value = 0.5248
## alternative hypothesis: true difference in means between group 阿卡波糖 and group 拜糖平 is not equal to 0
## 95 percent confidence interval:
##  -2.326179  1.206179
## sample estimates:
## mean in group 阿卡波糖   mean in group 拜糖平 
##                  2.065                  2.625
```

结果显示t=-0.64187，自由度df=38，p=0.5248，结果和课本一致。


