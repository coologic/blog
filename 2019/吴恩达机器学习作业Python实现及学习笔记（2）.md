---
title: "吴恩达机器学习作业Python实现及学习笔记（2）"
tags:
  - 机器学习
  - 笔记
categories:
  - 人工智能
date: 2019-06-12 22:21:01
---

教学视频地址https://www.coursera.org/learn/machine-learning/

黄海广博士笔记：https://github.com/fengdu78/Coursera-ML-AndrewNg-Notes

作业原始文件、本人的Python版本作业答案：https://github.com/TechieLiang/AndrewNgMeachineLearningNotes

博客原始内容：https://github.com/TechieLiang/blog/tree/master/2019/

# machine-learning-ex1线性回归作业

此部分针对此作业基于Python实现，后续标题与作业PDF的标题顺序一致

### 常用包及帮助文档

NumPy用于多维矩阵运算：[NumPy中文文档](https://www.numpy.org.cn/)

Pandas用于数据读写支持多种数据格式：[Pandas API Reference](http://pandas.pydata.org/pandas-docs/stable/reference/index.html)、[Pandas 中文文档](https://www.pypandas.cn/)

Matplotlib用于数据可视化：[Matplotlib 中文文档](https://www.matplotlib.org.cn/)

# Simple Octave/MATLAB function 

Modify it to return a 5x5 identity matrix by filling in the following code. 获取一个5x5单位矩阵

```
def CreateEye():
    E5 = np.eye(5)  # eye(5)代表5阶单位阵
    print('这是一个五阶单位阵')
    print(E5)
```



