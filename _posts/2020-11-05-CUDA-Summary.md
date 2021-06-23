---
layout: post
title: CUDA Summary
date: 2020-11-05
categories: [technology]
tags: [book]
comments: false
---



```c++
// Vector Cosine of CUDA
__global__ void VCos(int n, float* x, float* y) {
  int ix = blockIdx.x * blockDim.x + threadIdx.x;
  if (ix < n)
    y[ix] = cos(x[ix]);
}

int main() {
  float* host_x = (float*)malloc(n * sizeof(float));
  float* host_y = (float*)malloc(n * sizeof(float));
  float* dev_x;
  float* dev_y;
  const int n = 1024;
  
  cudaMalloc(&dev_x, n * sizeof(float));
  cudaMalloc(&dev_y, n * sizeof(float));
  
  cudaMemcpy(dev_x, host_x, n * sizeof(float), cudaMemcpyHostToDevice);
  cudaMemcpy(dev_y, host_y, n * sizeof(float), cudaMemcpyHostToDevice);
  
  const int blk = (n+255)/256;
  VCos<<<blk, 256>>>(n, dev_x, dev_y);
  
  cudaMemcpy(host_y, dev_y, n * sizeof(float), cudaMemcpyDeviceToHost);
  cudaFree(dev_x);
  cudaFree(dev_y);
  free(host_x);
  free(host_y);
  
  return 0;
}
```



```c++
// Array Reduction
// No shared memory bank conflict
__global__ void VCos(int n, float* x) {
  int tid = threadIdx.x;
  for (unsigned int s = blockDim.x/2; s > 0; s >>= 1) {
    x[tid] += x[tid+s];
  }
  __synchthreads();
}

static const int arraySize = 10000;
static const int blockSize = 1024;

__global__ void sumCommSingleBlock(const int *a, int *out) {
    int idx = threadIdx.x;
    int sum = 0;
    for (int i = idx; i < arraySize; i += blockSize)
        sum += a[i];
    __shared__ int r[blockSize];
    r[idx] = sum;
    __syncthreads();
    for (int size = blockSize/2; size>0; size/=2) { //uniform
        if (idx<size)
            r[idx] += r[idx+size];
        __syncthreads();
    }
    if (idx == 0)
        *out = r[0];
}

sumCommSingleBlock<<<1, blockSize>>>(dev_a, dev_out);

// Ref: https://sodocumentation.net/cuda/topic/6566/parallel-reduction--e-g--how-to-sum-an-array-
// https://developer.download.nvidia.com/assets/cuda/files/reduction.pdf
```

