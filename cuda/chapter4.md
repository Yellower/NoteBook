# 第四章 CUDA C并行编程

### 1. 示例：向量求和

```c
#include "../common/book.h"

#define N 10

// define add kernel
__global__ void add(int *a, int *b, int *c){
    int tid = blockIdx.x;
    if(tid < N)
        c[tid] = a[tid] + b[tid];
}

int main(){
    int a[N], b[N], c[N];
    int *dev_a, *dev_b, *dev_c;

    // allocate the memory on gpu
    HANDLE_ERROR(cudaMalloc((void**)&dev_a, N*sizeof(int)));
    HANDLE_ERROR(cudaMalloc((void**)&dev_b, N*sizeof(int)));
    HANDLE_ERROR(cudaMalloc((void**)&dev_c, N*sizeof(int)));

    for(int i=0; i<N; i++){
        a[i] = -i;
        b[i] = i * i;
    }

    // copy the arrays 'a' and 'b' to the GPU
    HANDLE_ERROR(cudaMemcpy(dev_a, a, N*sizeof(int), cudaMemcpyHostToDevice));
    HANDLE_ERROR(cudaMemcpy(dev_b, b, N*sizeof(int), cudaMemcpyHostToDevice));

    add<<<N, 1>>>(dev_a, dev_b, dev_c);

    // copy 'c' from GPU to CPU
    HANDLE_ERROR(cudaMemcpy(c, dev_c, N*sizeof(int), cudaMemcpyDeviceToHost));

    for(int i=0; i<N; i++){
        printf("%d + %d = %d\n", a[i], b[i], c[i]);
    }

    // free the memory allocated on the GPU
    cudaFree(dev_a);
    cudaFree(dev_b);
    cudaFree(dev_c);

    return 0;
}
```

示例的流程如下：

​	（1）创建主机变量`a,b,c`数组，并为它们分配对应大小的设备内存`dev_a, dev_b, dev_c`;

​	（2）为`a,b`数组初始化，并将值拷贝到GPU上（`cudaMemcpy()`）；

​	（3）执行核函数`add<<<N, 1>>>(dev_a, dev_b, dev_c)`;

​	（4）将结果从GPU拷贝回主机上；

* 尖括号语法

  `add<<<N, 1>>>(dev_a, dev_b, dev_c)`

  尖括号中有两个参数，这两个参数将传递给CUDA Runtime，告诉Runtime如何启动核函数。第一个参数表示设备在执行核函数是使用的线程块（block）的数量，第二个参数表示每个线程块中的线程（thread）的数量。

  假设指定的是`kernel<<<2,1>>>()`，那么可以认为Runtime会创建核函数的两个副本，并且以并行的方式运行它们。

* 线程块

  线程块的数量可以是一维、二维或三维的，每一维的最大数量不能超过65535。

  我们将线程块的集合称维Grid。通过`blockIdx`变量知道当前正在运行的是哪一个线程块。

  ```c
  __global__ void add(int *a, int *b, int *c){
      int tid = blockIdx.x;
      if(tid < N)
          c[tid] = a[tid] + b[tid];
  }
  ```

  在核函数中，我们通过`blockIdx.x`得到当前的线程块id，根据id计算向量中不同下标的元素。因此，每个线程块负责向量中一个位置的计算。

### 2. 示例：Julia Set

```

```

