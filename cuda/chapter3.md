# 第三章 CUDA C简介

### 1.基本概念

（1）主机和设备

在CUDA编程中，将CPU及系统称为主机（Host），将GPU称为设备（Device）。系统的内存称为主机内存，而GPU的内存称为设备内存。在CDUA程序中，正常情况下，主机和设备的代码只能访问各自的内存。

（2）核函数

在GPU设备上运行的函数。CUDA C为标准C增加了`__global__`修饰符，表示该函数是核函数，应该编译为在设备上运行的可执行文件。

### 2.简单的示例

```c
// add.cu
#include <stdio.h>
#include "../common/book.h"

__global__ void add(int a, int b, int *c){
    *c = a + b
}

int main()
{
    int c; // 主机上的c
    int *dev_c; // GPU的c
    HANDLE_ERROR(cudaMalloc((void**)&dev_c, sizeof(int))); //在GPU为dev_c分配内存

    // 执行核函数
    add<<<1,1>>>(2, 7, dev_c); 
	
    // 将GPU上的结果拷贝到Host上
    HANDLE_ERROR(cudaMemcpy(&c, dev_c, sizeof(int), cudaMemcpyDeviceToHost));

    print("2+7= %d\n", c);
    // 释放GPU上分配的内存
    cudaFree(dev_c);

    return 0;
}
```

（1）CUDA的程序后缀为`.cu`

（2）运行CUDA程序，需要使用`nvcc`编译器将程序编译称可执行文件：

```bash
nvcc add.cu -o add
./add
```

（3）尖括号语法

在主机代码中执行核函数时，要使用`<<<1, 1>>>`语法，这是CUDA Runtime（CUDA运行时）的参数，告诉Runtime划分的线程和线程块的数量

（4）cudaMalloc()

使用`cudaMalloc()`来分配GPU内存，类似`malloc()`。分配的指针可以作为参数传递给设备上执行的函数（比如核函数），也可以传递给主机上执行的函数（比如`cudaMemcpy()`），但只能在设备代码中对指针指向的内存进行读/写操作。

（4）cudaMemcpy()

`cudaMemcpy()`函数负责主机内存和设备内存间的通信，它可以将设备内存中的值拷贝到主机内存，或将主机内存上的值拷贝到设备内存

```
cudaMemcpy(目标位置， 源位置， 大小， Flag)
Flage的取值为：cudaMemcpyHostToDevice、cudaMemcpyDeviceToHost
```

### 3. 查询设备信息

```c
#include "../common/book.h"

int main(){
    cudaDeviceProp prop;

    int count;
    HANDLE_ERROR(cudaGetDeviceCount(&count));
    for(int i=0; i<count; i++){
        HANDLE_ERROR(cudaGetDeviceProperties(&prop, i));
        printf("--- General Information for device %d ---\n", i);
        printf("Name: %s\n", prop.name);
        printf("Compute capability: %d.%d\n", prop.major, prop.minor);
        printf("Clock rate: %d\n", prop.clockRate);

        printf("--- Memory Information for device %d ---\n", i);
        printf("Total global mem: %ld\n", prop.totalGlobalMem);
        printf("Total constant mem: %ld\n", prop.totalConstMem);
        printf("Max mem pitch: %ld\n", prop.memPitch);

        printf("Max threads per block: %d\n", prop.maxThreadsPerBlock);
        printf("Max thread dimensions: (%d, %d, %d)\n", prop.maxThreadsDim[0], prop.maxThreadsDim[1], prop.maxThreadsDim[2]);
        printf("Max grid dimensions: (%d, %d, %d)\n", prop.maxGridSize[0], prop.maxGridSize[1], prop.maxGridSize[2]);
        printf("\n");
    }

    return 0;
}
```

`cudaGetDeviceCount()`可以获得总的设备的数量，`cudaGetDeviceProperties()`获得设备的相关信息，可以根据设备信息选择自己需要的设备

```c
#include "../common/book.h"

int main()
{
    cudaDeviceProp prop;
    int dev;

    HANDLE_ERROR(cudaGetDevice(&dev));
    printf("ID for current CUDA device: %d\n", dev);

    memset(&prop, 0, sizeof(cudaDeviceProp));
    prop.major = 1;
    prop.minor = 3;
    HANDLE_ERROR(cudaChooseDevice(&dev, &prop));
    print("ID of CUDA device choosed: %d\n", dev);
    HANDLE_ERROR(cudaSetDevice(dev));

    return 0;
}
```

