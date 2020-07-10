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

### 5.由带角度的矩形框得到它的四个顶点
```
# box是带旋转角度的矩形框，box:（x_center, y_center), (w, h), angle 其中angle是0-360度
cv2.boxPoints(box)
```
示例：
```
def get_rotate_box(box):
    """ 
    根据旋转角度得到旋转后的矩形框
    arg:
        box:[x, y, w, h, rotation, class]
    return:
        rotate_box:[x1, y1, x2, y2, x3, y3, x4, y4, class]
    """
    angle = box[-2]
    x_center = int(box[0] + box[2] / 2)
    y_center = int(box[1] + box[3] / 2)
    box = ((x_center, y_center), (box[2], box[3]), angle)

    points = cv2.boxPoints(box)
    points = points.astype(np.int32)
    #print(points)

    # 按顺时针排序
    points = sorted(list(points), key=lambda x: x[0])

    index_1, index_2, index_3, index_4 = 0, 1, 2, 3

    if points[0][1] < points[1][1]:
        index_1 = 0 
        index_4 = 1 
    else:
        index_1 = 1 
        index_4 = 0 

    if points[2][1] < points[3][1]:
        index_2 = 2 
        index_3 = 3 
    else:
        index_2 = 3
        index_3 = 2

    box = np.array([points[index_1], points[index_2], points[index_3], points[index_4]])
    
    return box
```
### 6.绘制旋转矩形框
```
box = ((x_center, y_center), (w, h), angle)

# 得到旋转矩形框的四个顶点，并取整
points = cv2.boxPoints(box).astype(np.int32)
cv2.drawContours(img, [points], -1, (0, 255, 0), 2)
```

### 7. cv2.fillPoly()
cv2.contourArea()