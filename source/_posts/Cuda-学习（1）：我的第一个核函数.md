title: CUDA学习（1）：我的第一个 CUDA 程序
author: emon100
date: 2025-05-19 01:34:06
tags:
---
# 为什么学习CUDA？

首先，当前几乎每一项新的计算机技术，无论是AI还是加密货币，都需要大量的算力。当前CPU发展停滞，越来越多的应用转而向GPU寻求算力。为了了解如何使用GPU计算，我希望学习一门异构计算的技术。

其次，当前CUDA是最火热的异构计算技术，无论是GPU保有量。还是网络上的资料因为接近20年的发展也是最丰富的，我认为从这门异构计算技术入门学习是没有问题的。

在说我目前最感兴趣的大模型领域，如果想要降低成本/提升速度，势必需要增加对硬件的利用率，这一块一定要做针对硬件级别的优化，因此我也希望通过学习CUDA，一探GPU架构设计与应用程序加速需要做的事情。


# 怎么学习

我计划的学习路线如下：

1. 首先先了解基础的 CUDA 程序编写，以及GPU的编程模式（NVIDIA GPU上的算子需要考虑的东西，如内存模型，计算模型，并行算法）

https://cnugteren.github.io/tutorial/pages/page1.html

我希望自己在1week内将这里面提到的习题完全做完。


2. [CS336: 从零开始构建语言模型！](https://www.bilibili.com/video/BV13SV9zdEhX?spm_id_from=333.788.videopod.sections&vd_source=17e0887adabbc84dcbb9ca464dc647fd)

然后是特化的部分，了解CUDA编程与LLM的加速之间的关系。



# 进入正题前：配置环境

个人使用了1台GTX 1060的笔记本，安装Ubuntu 24.0双系统来学习CUDA，搭建环境的过程不多赘述。还有就是你的全套工具链你得有一个比较稳定的科学上网环境，以及替换镜像源，否则很多事情会很麻烦。

我发现装环境中很多事情如果交给大二以下的我，是完成不了的，这个安装过程还是挺考验 Linux 操作系统的相关经验的。

装系统：https://www.bilibili.com/video/BV1Cc41127B9

装环境：https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=24.04&target_type=deb_local

注意，GPU driver支持的cuda version是什么你就装哪个版本的cuda-toolkit，否则容易出问题。

装完之后要配置一些环境变量，然后

# 学习时要注意的东西

cuda的编程模型：CPU发任务给GPU做，因此互相通信会很耗时。

可以参考的资料：

https://developer.nvidia.com/blog/even-easier-introduction-cuda/

https://zhuanlan.zhihu.com/p/34587739


# 第一个程序

写第一个程序的时候有很多东西由于不熟悉导致出错了也不知道这提醒了我们要积极的检查cuda报的错误。我们通过一个宏`gpuErrchk`和cuda相关的函数 `cudaGetLastError` 实现

https://stackoverflow.com/questions/14038589/what-is-the-canonical-way-to-check-for-errors-using-the-cuda-runtime-api


```c++

// main.cu 用GPU初始化一个数组

// to run: nvcc main.cu -o main; ./main

#include <iostream>
#include <cmath>
#include "cuda_runtime.h"

#define gpuErrchk(ans) { gpuAssert((ans), __FILE__, __LINE__); }
inline void gpuAssert(cudaError_t code, const char *file, int line, bool abort=true)
{
    if (code != cudaSuccess)
    {
        fprintf(stderr,"GPUassert: %s %s %d\n", cudaGetErrorString(code), file, line);
        if (abort) exit(code);
    }
}


// 你的在GPU上运行的函数，叫做kernel，用 __global__ 告诉NVCC
__global__ void initArrayGridStride(float *arr, float val, int n) {
    // 计算当前线程在整个网格中的唯一全局索引 (起始索引)
    int idx = blockIdx.x * blockDim.x + threadIdx.x;

    // 计算整个网格的总线程数 (步长)
    int stride = gridDim.x * blockDim.x;

    // 使用循环初始化元素，每次跳过一个网格的宽度
    for (int i = idx; i < n; i += stride) {
        arr[i] = val;
    }
}


int main() {
    int dev = 0;
    cudaDeviceProp devProp;
    cudaGetDeviceProperties(&devProp, dev);
    std::cout << "使用GPU device " << dev << ": " << devProp.name << std::endl;
    std::cout << "SM的数量：" << devProp.multiProcessorCount << std::endl;
    std::cout << "每个线程块的共享内存大小：" << devProp.sharedMemPerBlock / 1024.0 << " KB" << std::endl;
    std::cout << "每个线程块的最大线程数：" << devProp.maxThreadsPerBlock << std::endl;
    std::cout << "每个EM的最大线程数：" << devProp.maxThreadsPerMultiProcessor << std::endl;
    std::cout << "每个SM的最大线程束数：" << devProp.maxThreadsPerMultiProcessor / 32 << std::endl;

    
    int N = 1 << 28;
    int nBytes = N * sizeof(float);
    float *d_x;
    gpuErrchk(cudaMalloc(&d_x, nBytes));
    // each kernal launch **1** grid and gridDim describle how many blocks

    dim3 initGridDim(1);
    dim3 initBlockDim(1);
    initArrayGridStride<<<initGridDim, initBlockDim>>>(d_x, 10.0, N);
    cudaDeviceSynchronize();
    gpuErrchk(cudaGetLastError());
    
    
    float *z = (float *) malloc(N * sizeof(float));
    gpuErrchk(cudaMemcpy(z, d_x, nBytes, cudaMemcpyDeviceToHost));
    // 检查执行结果
    float maxError = 0.0;
    for (int i = 0; i < N; i++) {
        maxError = std::max(maxError, abs(z[i]-10));
        if (z[i] != 10.0) {
            std::cout << "Error: z[" << i << "] = " << z[i] << std::endl;
            break;
        }
    }
    std::cout << "max error: " << maxError << std::endl;
    cudaFree(d_x);
    free(z);

    return 0;
}


```

# CUDA 编程的奇妙之处

我发现官方推荐使用的方式叫做 [stride loops](https://developer.nvidia.com/blog/cuda-pro-tip-write-flexible-kernels-grid-stride-loops/). 和CPU编程时强调的内存局部性，各个线程做一大块内存不同，此stack-overflow有回答。

https://stackoverflow.com/questions/10297067/in-a-cuda-kernel-how-do-i-store-an-array-in-local-thread-memory

