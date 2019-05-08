# Python Opencv的常用操作

## 1.模糊

```python
#均值滤波
img_Blur=cv2.blur(img01,(5,5))
#中值滤波
medianBlur=cv2.medianBlur(img01,5)
#高斯滤波
img_GaussianBlur=cv2.GaussianBlur(img01,(7,7),0)
```

## 2.锐化

```python
kernel = np.array([[0, -1, 0], [-1, 5, -1], [0, -1, 0]], np.float32) #锐化
dst = cv.filter2D(image, -1, kernel=kernel)
```

## 3.二值化

```python
#全局阈值
ret,th1 = cv2.threshold(GrayImage,127,255,cv2.THRESH_BINARY)
#自适应阈值 两种自适应方法：ADAPTIVE_THRESH_MEAN_C ADAPTIVE_THRESH_GAUSSINA_C
th2 = cv2.adaptiveThreshold(GrayImage,255,cv2.ADAPTIVE_THRESH_MEAN_C,cv2.THRESH_BINARY,3,5)
th3 = cv2.adaptiveThreshold(GrayImage,255,cv2.ADAPTIVE_THRESH_GAUSSIAN_C,cv2.THRESH_BINARY,3,5) #3为block的大小 5为c的值
```

详细内容:[python-opencv 图像二值化，自适应阈值处理](https://www.cnblogs.com/Anita9002/p/9077472.html)

## 4.基本操作

```python
＃读入BGR格式
img = cv2.imread(img_path) #读入图片 默认cv2.IMREAD_COLOR
img = cv2.imread(img_path, cv2.IMREAD_GRAYSCALE) #读入图片并转成灰度图像
#显示
cv2.imshow('img',img) 
cv2.waitKey(0)
#保存
cv2.imwrite(dest_path, img)
```

