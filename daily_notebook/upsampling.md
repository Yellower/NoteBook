# 上采样

常用的有两种方法：resize和转置卷积

### 转置卷积

参考：[抽丝剥茧，带你理解转置卷积（反卷积）](https://blog.csdn.net/tsyccnh/article/details/87357447)

卷积的输出大小：
$$
out = ceil((n-k+2*p)/stride)+1
$$
n：表示特征图的宽高

k：卷积核的尺寸

p：padding在大小

转置卷积的输出大小：
$$
out = (n-1)*stride-2*p+k
$$
