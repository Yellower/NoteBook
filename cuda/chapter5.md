# 第五章 线程协作

### 1. 线程

上一章中，介绍了向量求和的例子。在这个例子中，我们使用N个线程块对含N个元素的向量求和，每个线程块负责一个位置的计算，它的核函数调用如下：

```c
add<<<N, 1>>>(dev_a, dev_b, dev_c)
```

它表示运行时启动N个线程块，每个线程块包含1个线程。其实，我们也可以启动1个包含N个线程的线程块来计算。使用线程和使用线程块相比，在这个例子中并没有什么优势，但其他情况下，还是有一定的好处。

示例：向量求和-线程版本

```c
#include "../common/book.h"

#define N 10

// define add kernel
__global__ void add(int *a, int *b, int *c){
    int tid = threadIdx.x;
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

    add<<<1, N>>>(dev_a, dev_b, dev_c);

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

这里的代码与上一章的相比，只有第7、30两行不同，但却使用了N个线程，而非N个线程块

在上一章中，我们提到：线程块每一维的数量不超过65535。同样，线程的数量也有限制，每一维不超过512。因此，如果向量的元素数量特别大，则只使用线程或线程块会超出硬件的限制，此时可以结合线程块和线程

```c
// 当前线程的id
tid = threadIdx.x + blockIdx.x * blockDim.x；

// 核函数的调用
add<<<((N+127)/128, 128)>>>(dev_a, dev_b, dev_c)；
```

但是，如果$N > 65535 * 512$的话，还是会超出GPU硬件的限制，此时，可以将核函数修改为如下：

```c
__global__ void add(int *a, int *b, int *c){
    int tid = threadIdx.x + blockIdx.x * blockDim.x;
    while(tid < N)
        c[tid] = a[tid] + b[tid];
        tid += blockDim.x * gridDim.x; // 计算超出 thread数量*block数量 的部分
}
```

示例： 向量求和-任意长度版本

```c
#include "../common/book.h"

#define N (33*1024)

// define add kernel
__global__ void add(int *a, int *b, int *c){
    int tid = threadIdx.x + blockIdx.x * blockDim.x;
    while(tid < N)
    {
        c[tid] = a[tid] + b[tid];
        tid += blockDim.x * gridDim.x;
    }
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

    // 因为核函数可以处理任意长度，所以这里可以只启动较少的线程
    add<<<128, 128>>>(dev_a, dev_b, dev_c);

    // copy 'c' from GPU to CPU
    HANDLE_ERROR(cudaMemcpy(c, dev_c, N*sizeof(int), cudaMemcpyDeviceToHost));

    // 验证
    bool success = true;
    for(int i=0; i<N; i++)
    {
        if(a[i]+b[i] != c[i])
        {
            printf("Error: %d + %d != %d \n", a[i], b[i], c[i]);
            success = false;
        }
    }
    if(success)
        printf("We did it!\n");

    // free the memory allocated on the GPU
    cudaFree(dev_a);
    cudaFree(dev_b);
    cudaFree(dev_c);

    return 0;
}
```

### 2. 共享内存和同步

前面提到的，使用线程有额外的好处：

（1）在上面的示例中，使用线程和线程块结合的方式解决了线程块数量的限制；

（2）同一个线程块中的线程可以通过共享内存进行通信；

通过`__shared__`修饰符将一个变量声明为共享内存变量，GPU处理该变量时将它放入共享内存中

CUDA C编译器对共享内存中的变量和普通变量的处理方式不同。对于在GPU上启动的每个线程块，CUDA C编译器都会创建该变量的一个副本，线程块中的线程共享这块内存，但线程无法看到也不能修改其他线程块中的变量副本。也就是说，同一个线程块中的线程可以进行通信和协作。

同时，为了进行通信，我们需要付出相应的代价，即线程间的同步。例如，假设线程A将一个值写入到共享内存，并且我们希望线程B在之后对这个值进行操作，那么只有在线程A的写入操作完成后，线程B才能开始执行它的操作。CUDA C提供`__syncthreads()`函数实现对线程的同步。

示例：点积运算

```c
#include "../common/book.h"

#define imin(a, b) (a<b?a:b)

const int N = 33 * 1024;
const int threadsPerBlock = 256;
const int blocksPerGrid = imin(32, (N + threadsPerBlock - 1)/threadsPerBlock);

__global__ void dot(float *a, float *b, float *c)
{
    __shared__ float cache[threadsPerBlock];
    // 计算tid
    int tid = threadIdx.x + blockIdx.x * blockDim.x;
    int cachedIndex = threadIdx.x;

    float temp = 0;
    while(tid < N)
    {
        temp += a[tid] * b[tid];
        tid += blockDim.x * gridDim.x;
    }

    cache[cachedIndex] = temp;

    // 对同一个block中的线程进行同步
    __syncthreads();

    // 利用归约方法，对cache求和
    // threadsPerBlock必须是2的指数次幂
    int i = threadsPerBlock / 2;
    while(i != 0)
    {
        if(cachedIndex < i)
            cache[cachedIndex] += cache[cachedIndex + i];
        __syncthreads();
        i /= 2;
    }

    // 使用某一个线程更新结果
    if(cachedIndex == 0)
        c[blockIdx.x] = cache[0];
}

int main()
{
    float *a, *b, c, *partial_c;
    float *dev_a, *dev_b, *dev_partial_c;

    // 在CPU分配内存
    a = (float*) malloc(N*sizeof(float));
    b = (float*) malloc(N*sizeof(float));
    partial_c = (float*) malloc(blocksPerGrid*sizeof(float));

    HANDLE_ERROR(cudaMalloc((void**)&dev_a, N*sizeof(float)));
    HANDLE_ERROR(cudaMalloc((void**)&dev_b, N*sizeof(float)));
    HANDLE_ERROR(cudaMalloc((void**)&dev_partial_c, blocksPerGrid*sizeof(float)));

    for(int i=0; i<N; i++)
    {
        a[i] = i;
        b[i] = i*2;
    }

    HANDLE_ERROR(cudaMemcpy(dev_a, a, N*sizeof(float), cudaMemcpyHostToDevice));
    HANDLE_ERROR(cudaMemcpy(dev_b, b, N*sizeof(float), cudaMemcpyHostToDevice));
    dot<<<blocksPerGrid, threadsPerBlock>>>(dev_a, dev_b, dev_partial_c);

    // 将数组 c 从GPU拷贝到CPU
    HANDLE_ERROR(cudaMemcpy(partial_c, dev_partial_c, blocksPerGrid*sizeof(float), cudaMemcpyDeviceToHost));

    // 在CPU完成最终的求和
    c = 0;
    for(int i=0; i<blocksPerGrid; i++)
        c += partial_c[i];

    #define sum_squares(x) (x*(x+1)*(2*x+1)/6)
    printf("Does GPU value %.6g = %.6g?\n", c, 2 * sum_squares((float)(N-1)));

    // 释放GPU内存
    cudaFree(dev_a);
    cudaFree(dev_b);
    cudaFree(dev_partial_c);

    // 释放CPU内存
    free(a);
    free(b);
    free(partial_c);

    return 0;
}
```



