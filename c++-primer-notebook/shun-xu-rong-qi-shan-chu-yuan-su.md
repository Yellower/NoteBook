# 顺序容器-删除元素

编辑时间：2019.4.11

**注意：在删除元素之前，必须保证它们是存在的**

```C++
** 删除操作 **
c.pop_back() #删除尾元素，返回void
c.pop_front() #删除首元素,返回void
c.erase(p) #删除迭代器p指向的元素，返回指向下一元素的迭代器
c.erase(b,e) #删除范围内的元素
c.clear() #删除所有元素，返回void
```

##### 删除前检查元素是否存在，特别是pop_back pop_front

```c++
while(!ilist.empty()){
    process(ilist.front());
    ilist.pop_front();
}
```

##### 接受迭代器范围的删除操作

```c++
elem1 = slist.erase(elem1, elem2) #调用后，elem1=elem2
```

如果`elem1=elem2`，则不会删除任何元素，因为这样的迭代器范围为空