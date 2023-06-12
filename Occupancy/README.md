## Occupancy

**参考资料：** 

* [Occupancy的定义](https://docs.nvidia.com/cuda/cuda-c-best-practices-guide/index.html#occupancy)

* [Cuda Runtime Occupancy Api](https://docs.nvidia.com/cuda/cuda-runtime-api/group__CUDART__OCCUPANCY.html#group__CUDART__OCCUPANCY)

* [Achieved Occupancy in Nsight](https://docs.nvidia.com/gameworks/content/developertools/desktop/analysis/report/cudaexperiments/kernellevel/achievedoccupancy.htm)
* [Occupancy in C++ Best Practices](https://docs.nvidia.com/cuda/cuda-c-best-practices-guide/index.html#occupancy)



计算**Theoretical Occupancy**的Sample

```cuda
// calculate theoretical occupancy
  int maxActiveBlocks;
  cudaOccupancyMaxActiveBlocksPerMultiprocessor( &maxActiveBlocks, 
                                                 MyKernel, blockSize, 
                                                 0);

  int device;
  cudaDeviceProp props;
  cudaGetDevice(&device);
  cudaGetDeviceProperties(&props, device);

  float occupancy = (maxActiveBlocks * blockSize / props.warpSize) / 
                    (float)(props.maxThreadsPerMultiProcessor / 
                            props.warpSize);

  printf("Launched blocks of size %d. Theoretical occupancy: %f\n", 
         blockSize, occupancy);
```

