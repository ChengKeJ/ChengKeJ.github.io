---
layout: post
title: "在3分钟内学习二分查找"
categories: 算法
tags: 算法
keywords: 二分查找算法, 有序数组查找, 线性查找对比, 逐步排除原理, 时间复杂度与空间复杂度, 算法性能优化, 数据库中的有序序列, 排序链表搜索算法, 决策过程中的应用
date: 2023/12/17 20:35:25
---


二分查找是一种在有序数组中查找元素的算法，通过不断将搜索区域分成两半来实现。你可能在日常生活中已经不知不觉地使用了大脑里的二分查找。

最常见的例子是在字典中查找一个单词。假设你想找到“马拉松”的定义。你不会从字典的开头开始查找。你会打开字典大约在中间位置。如果你在‘T’处，你已经过头了。所以，你会调整并分割差距，缩小范围直到找到“马拉松”。

![1*eyscCE4qgTvv-yHMQzfpoQ.png](/uploads/1*eyscCE4qgTvv-yHMQzfpoQ.png)

这个逐步排除的过程就是二分查找的要点，但是针对数组的情况。

# 线性查找 vs 二分查找

考虑一个从1到100的数字数组，你需要在这个范围内找到一个特定的数字，就像玩一个猜数游戏。

## 线性查找

简单的方法是使用一个简单的for循环——遍历数组中的每个元素直到找到目标项。

```javascript
function findItem(arr, item) {
  for (let i = 0; i < arr.length; i++) {  
    if (arr[i] === item) {  
      return i;  
    }
  }
  return -1; // 未找到项
}
```

这个方法可以找到，但它的时间复杂度是O(n)；想象一下，如果你要找的数在1到1万亿之间。

你可以比线性查找做得更好。

<!--more-->

## 二分查找

使用二分查找，不是检查每个元素，而是从中间开始。如果你要找的数字更大，你向右走；如果更小，你向左走。

![1*hAOPpSt3hqukVmFdPrKefQ.png](/uploads/1*hAOPpSt3hqukVmFdPrKefQ.png)

然后呢？你重复这个过程。中间，左边或右边，缩小可能性直到找到数字。

```javascript
function binarySearch(arr, target) {
  let left = 0;
  let right = arr.length - 1;
  while(left <= right) {
    let mid = Math.floor((left + right) / 2);
    if(arr[mid] === target) return mid;
    if(arr[mid] < target) left = mid + 1;
    else right = mid - 1;
  }
  return -1;
}
```

它不仅能迅速切入数据；时间复杂度为O(logN)，比线性搜索快得多。

在空间方面，它是O(1)。不需要额外的存储空间。

# 小结：何时使用二分查找？

二分查找最常用于数组，但也可以应用于数据库中的有序序列、排序链表中的搜索算法，甚至决策过程中可以预测和系统地划分范围的情况。

![1*Rg-FcePvP1TUC1FN55UfcQ.png](/uploads/1*Rg-FcePvP1TUC1FN55UfcQ.png)

重要的是，二分查找只能在排序的项目集合上执行。整个二分查找的方法基于这样一个原则，即集合是按顺序排列的，这样算法才能通过比较中间项来预测搜索项的位置。如果集合没有排序，这种预测就不起作用，算法也不能正确运行。



## 系统设计概念系列文章

[计算机的层次化架构](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488478&idx=1&sn=351b322c2089d8fd7124cf801e0a0524&chksm=fb7c9d29cc0b143facd8d62fb15812d6d28e22749e1183c48d6946dbd13d2e206cd9826bfb20&scene=21#wechat_redirect)

[每个开发者都应该知道的7个原则](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488411&idx=1&sn=2520f364a171f0d747a855600c99cc4b&chksm=fb7c9d6ccc0b147a44627a8eef07b5772acbda406ce566afe306ed2b3c98f665e05c12b6ad36&scene=21#wechat_redirect)

[6个系统设计的基本概念](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488409&idx=1&sn=a02b97d71d301be4200ab25e4ffad0bc&chksm=fb7c9d6ecc0b1478f85986554735e307e2c92d45236f241dec44a529e9c799c521d5448f9f9a&scene=21#wechat_redirect)

[数据库：系统设计的核心](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488407&idx=1&sn=6072efba48ed2f9f1968594a1055a3f6&chksm=fb7c9d60cc0b14766e394d5c04f33d5b1c63987d6cf5b66732887e9ffedbbfc576e5a6617891&scene=21#wechat_redirect)


## 图解系列

[系统设计中的缓存技术：完整指南](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488260&idx=1&sn=89701a38ab8af1c670b432b5e7bedf2d&chksm=fb7c9df3cc0b14e52a043999e59b5c1251c3183a61b8c9d35777f237d1478c07b7d01352346e&scene=21#wechat_redirect)

[关系数据库的全景图](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247488165&idx=1&sn=2c4aaad53e6ca87f61e23dfba2fd8f9e&chksm=fb7c9c52cc0b1544266bb7e008f55bfbaa9b2acdb512eb07beece554e9f766e80f12699208b7&scene=21#wechat_redirect)

[Redis 全景解析](http://mp.weixin.qq.com/s?__biz=MzU0NDMzMjY5Ng==&mid=2247487961&idx=1&sn=f1437b130e1828f4c1c0c45806e4922f&chksm=fb7c9f2ecc0b1638cf34322599d4bd42c002bf2e37c642d9dbc596366f91f237f410b4e90fc5&scene=21#wechat_redirect)

当然架构设计、全景图解系列还有很多,快来关注一起学习吧~
