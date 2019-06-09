title: " Qt-排序\t\t"
tags:
  - qt
url: 96.html
id: 96
categories:
  - Qt
date: 2017-11-10 19:34:57
---
```
void qsort(void *base,int nelem,int width,int (*fcmp)(const void *,const void *));//1
void qSort(RandomAccessIterator begin, RandomAccessIterator end, LessThan lessThan);//2
void qSort(RandomAccessIterator begin, RandomAccessIterator end);//3
```
1、要求传入起始指针，总长度，单元素空间占用大小`(sizeof(A[i]))`，判断函数。判断函数参数类型为`const void *`，使用需要在函数内自行转换为对应类型，返回值为整数型，升序排序时正表示参数1大于参数2,0表示相等，负表示小于。 2、范例如下
```
QList<TTT *> mlist;
    qSort(mlist.begin(),mlist.end(),cmp);
    bool cmp(const TTT *a, const TTT *b) {
    return a->num()>b->num()?true:false;
}
```