# Test-Time Augmentation(TTA)使用

参考：[How to Use Test-Time Augmentation to Make Better Predictions](https://machinelearningmastery.com/how-to-use-test-time-augmentation-to-improve-model-performance-for-image-classification/)

1.原理

Test-time augmentation指的是将数据扩充（data augmentation）应用在测试集上，具体的操作为：利用不同的扩充方法，生成多份测试集，每份测试集数量是一样的，但经过不同的扩充方法（缩放、翻转、平移等），训练好的网络对每份测试集进行预测，最后对所有预测结果进行集成（ensemble）得到最终的预测结果。

集成的方法也很多：比如对一个测试样例所有扩充后的样本的预测的logits或prob求和（或平均），然后再取最大，或者使用投票的方法（个人观点）

2.如何选择正确的TTA配置

（1） 使用同一个训练好的模型，重复训练模型代价太高；

（2）在部分验证集或测试集上尝试不同的增强方法，找到验证集上效果最好的配置；

