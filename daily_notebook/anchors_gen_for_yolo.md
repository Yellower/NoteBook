# YOLOv2/v3中anchors的设置

## 1.基本原理

作者利用 k-means 聚类方法在 训练集检测框上来寻找先验框（anchors的大小）。

标准的k-means方法用的是欧氏距离，但是这样会导致 larger boxes generate more error than smaller boxes. 我们想要得到的先验框，是能够带领我们得到更高的IOU的，如果用欧氏距离来衡量，可能会导致“大框优势”。所以作者使用了
$$
d(box, centroid) = 1-IOU(box, centroid) 
$$
作为k-means中“距离”的定义。IOU越大对应的“距离”越小。

**K-means原理**

1. 随机生成K个聚类中心
2. 计算每个点到聚类中心的距离，把每个点分配到距离最近的聚类中心
3. 根据分配的点更新聚类中心的坐标
4. 反复

## 2.代码和细节

1.不同版本yolo对应的anchros不同。yolov2的anchors相对特征图大小，yolov3的anchors相对原图大小

2.针对yolov3三个分支的情况，我个人理解是直接设置9个聚类中心，生成小的anchors对就卷积特征图最大的分支，大的anchors对就应卷积特征图最小的分支

3.程序输入的训练集中的bounding box是归一化后的，即$w = box_w/img_w, h= box_h/img_h$

4.程序需要设置送入网络的图片大小值，对应cofig中设置的大小

```python
#  原地址：https://github.com/AlexeyAB/darknet/blob/master/scripts/gen_anchors.py
import argparse
import cv2
import numpy as np
import sys
import os
import random 
import math
 
def IOU(x,centroids):
    '''
    :param x: 某一个ground truth的w,h
    :param centroids:  anchor的w,h的集合[(w,h),(),...]，共k个
    :return: 单个ground truth box与所有k个anchor box的IoU值集合
    '''
    IoUs = []
    w, h = x  # ground truth的w,h
    for centroid in centroids:
        c_w,c_h = centroid   #anchor的w,h
        if c_w>=w and c_h>=h:   #anchor包围ground truth
            iou = w*h/(c_w*c_h)
        elif c_w>=w and c_h<=h:    #anchor宽矮
            iou = w*c_h/(w*h + (c_w-w)*c_h)
        elif c_w<=w and c_h>=h:    #anchor瘦长
            iou = c_w*h/(w*h + c_w*(c_h-h))
        else: #ground truth包围anchor     means both w,h are bigger than c_w and c_h respectively
            iou = (c_w*c_h)/(w*h)
        IoUs.append(iou) # will become (k,) shape
    return np.array(IoUs)
 
def avg_IOU(X,centroids):
    '''
    :param X: ground truth的w,h的集合[(w,h),(),...]
    :param centroids: anchor的w,h的集合[(w,h),(),...]，共k个
    '''
    n,d = X.shape
    sum = 0.
    for i in range(X.shape[0]):
        sum+= max(IOU(X[i],centroids))  #返回一个ground truth与所有anchor的IoU中的最大值
    return sum/n    #对所有ground truth求平均
 
def write_anchors_to_file(centroids,X,anchor_file,input_shape,yolo_version):
    '''
    :param centroids: anchor的w,h的集合[(w,h),(),...]，共k个
    :param X: ground truth的w,h的集合[(w,h),(),...]
    :param anchor_file: anchor和平均IoU的输出路径
    '''
    f = open(anchor_file,'w')
    
    anchors = centroids.copy()
    print(anchors.shape)
 
    if yolo_version=='yolov2':
        for i in range(anchors.shape[0]):
            #yolo中对图片的缩放倍数为32倍，所以这里除以32，
            # 如果网络架构有改变，根据实际的缩放倍数来
            #求出anchor相对于缩放32倍以后的特征图的实际大小（yolov2）
            anchors[i][0]*=input_shape/32.
            anchors[i][1]*=input_shape/32.
    elif yolo_version=='yolov3':
        for i in range(anchors.shape[0]):
            #求出yolov3相对于原图的实际大小
            anchors[i][0]*=input_shape
            anchors[i][1]*=input_shape
    else:
        print("the yolo version is not right!")
        exit(-1)
 
    widths = anchors[:,0]
    sorted_indices = np.argsort(widths)
 
    print('Anchors = ', anchors[sorted_indices])
        
    for i in sorted_indices[:-1]:
        f.write('%0.2f,%0.2f, '%(anchors[i,0],anchors[i,1]))
 
    #there should not be comma after last anchor, that's why
    f.write('%0.2f,%0.2f\n'%(anchors[sorted_indices[-1:],0],anchors[sorted_indices[-1:],1]))
    
    f.write('%f\n'%(avg_IOU(X,centroids)))
    print()
 
def kmeans(X,centroids,eps,anchor_file,input_shape,yolo_version):
    
    N = X.shape[0] #ground truth的个数
    iterations = 0
    print("centroids.shape",centroids)
    k,dim = centroids.shape  #anchor的个数k以及w,h两维，dim默认等于2
    prev_assignments = np.ones(N)*(-1)    #对每个ground truth分配初始标签
    iter = 0
    old_D = np.zeros((N,k))  #初始化每个ground truth对每个anchor的IoU
 
    while True:
        D = []
        iter+=1           
        for i in range(N):
            d = 1 - IOU(X[i],centroids)
            D.append(d)
        D = np.array(D) # D.shape = (N,k)  得到每个ground truth对每个anchor的IoU
        
        print("iter {}: dists = {}".format(iter,np.sum(np.abs(old_D-D))))  #计算每次迭代和前一次IoU的变化值
            
        #assign samples to centroids 
        assignments = np.argmin(D,axis=1)  #将每个ground truth分配给距离d最小的anchor序号
        
        if (assignments == prev_assignments).all() :  #如果前一次分配的结果和这次的结果相同，就输出anchor以及平均IoU
            print("Centroids = ",centroids)
            write_anchors_to_file(centroids,X,anchor_file,input_shape,yolo_version)
            return
 
        #calculate new centroids
        centroid_sums=np.zeros((k,dim),np.float)   #初始化以便对每个簇的w,h求和
        for i in range(N):
            centroid_sums[assignments[i]]+=X[i]         #将每个簇中的ground truth的w和h分别累加
        for j in range(k):            #对簇中的w,h求平均
            centroids[j] = centroid_sums[j]/(np.sum(assignments==j)+1)
        
        prev_assignments = assignments.copy()     
        old_D = D.copy()  

def load_dataset(path):
    '''
    输出：w h 在[0, 1]之间，为相对原图大小
    '''
    dataset = []

    #获得图片大小
    TRAIN_IMG_DIR = '/data/Clouds_Classify/train_images/'
    
    with open(path, 'r') as f:
        lines = f.readlines()
        print('example num:', len(lines))
        for line in lines:
            if len(line.strip().split()[1:]) != 0:
                labels = line.strip().split(' ')

                # img_path = os.path.join(TRAIN_IMG_DIR, labels[0])
                # img = cv2.imread(img_path)
                # h, w = img.shape[0], img.shape[1]
                h, w = 1400, 2100

                #print('h w:',h, w)
                for i in range(1,len(labels)):
                    bbox = labels[i].split(',')
                    #print(bbox)
                    dataset.append([(int(bbox[2])- int(bbox[0]))/w, (int(bbox[3]) - int(bbox[1]))/h])

    return np.array(dataset)
 
def main(argv):
    parser = argparse.ArgumentParser()
    parser.add_argument('-file', default = './data/clouds_dataset/clouds_train.txt',
                        help='path to filelist\n' )
    parser.add_argument('-output_file', default = 'anchors.txt', type = str,
                        help='Output anchor directory\n' )
    parser.add_argument('-num_clusters', default = 9, type = int, 
                        help='number of clusters\n' )
    '''
    需要注意的是yolov2输出的值比较小是相对特征图来说的，
    yolov3输出值较大是相对原图来说的，
    所以yolov2和yolov3的输出是有区别的
    '''
    parser.add_argument('-yolo_version', default='yolov2', type=str,
                        help='yolov2 or yolov3\n')
    parser.add_argument('-yolo_input_shape', default=512, type=int,
                        help='input images shape，multiples of 32. etc. 416*416\n')
    args = parser.parse_args()
    
    annotation_dims = load_dataset(args.file)
    print('data loaded!!!')
  
    eps = 0.005
 
    if args.num_clusters == 0:
        for num_clusters in range(1,11): #we make 1 through 10 clusters 
            anchor_file = args.output_file
 
            indices = [ random.randrange(annotation_dims.shape[0]) for i in range(num_clusters)]
            centroids = annotation_dims[indices]
            kmeans(annotation_dims,centroids,eps,anchor_file,args.yolo_input_shape,args.yolo_version)
            print('centroids.shape', centroids.shape)
    else:
        anchor_file = args.output_file
        indices = [ random.randrange(annotation_dims.shape[0]) for i in range(args.num_clusters)]
        centroids = annotation_dims[indices]
        kmeans(annotation_dims,centroids,eps,anchor_file,args.yolo_input_shape,args.yolo_version)
        print('centroids.shape', centroids.shape)
 
if __name__=="__main__":
    main(sys.argv)
```

