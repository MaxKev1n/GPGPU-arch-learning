## cuda

**任务一：** 使用`cuda toolkit`编译`vecaddLoop.cu`得到`vecaddLoop.ptx`



**参考资料：** 

* https://zhuanlan.zhihu.com/p/33342065
* https://zhuanlan.zhihu.com/p/623756901



1. 使用`nvcc`的`-dryrun`等选项编译文件

```
/usr/local/cuda/bin/nvcc -dryrun --verbose --save-temps -lineinfo -g -arch=sm_80 vecaddLoop.cu 
 
 
#$ _NVVM_BRANCH_=nvvm
#$ _SPACE_= 
#$ _CUDART_=cudart
#$ _HERE_=/usr/local/cuda/bin
#$ _THERE_=/usr/local/cuda/bin
#$ _TARGET_SIZE_=
#$ _TARGET_DIR_=
#$ _TARGET_DIR_=targets/x86_64-linux
#$ TOP=/usr/local/cuda/bin/..
#$ NVVMIR_LIBRARY_DIR=/usr/local/cuda/bin/../nvvm/libdevice
#$ LD_LIBRARY_PATH=/usr/local/cuda/bin/../lib:
#$ PATH=/usr/local/cuda/bin/../nvvm/bin:/usr/local/cuda/bin:/home/chenzihang/.vscode-server/bin/695af097c7bd098fbf017ce3ac85e09bbc5dda06/bin/remote-cli:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
#$ INCLUDES="-I/usr/local/cuda/bin/../targets/x86_64-linux/include"  
#$ LIBRARIES=  "-L/usr/local/cuda/bin/../targets/x86_64-linux/lib/stubs" "-L/usr/local/cuda/bin/../targets/x86_64-linux/lib"
#$ CUDAFE_FLAGS=
#$ PTXAS_FLAGS=
#$ rm a_dlink.reg.c
#$ gcc -D__CUDA_ARCH_LIST__=800 -E -x c++ -D__CUDACC__ -D__NVCC__  "-I/usr/local/cuda/bin/../targets/x86_64-linux/include"    -D__CUDACC_VER_MAJOR__=12 -D__CUDACC_VER_MINOR__=0 -D__CUDACC_VER_BUILD__=76 -D__CUDA_API_VER_MAJOR__=12 -D__CUDA_API_VER_MINOR__=0 -D__NVCC_DIAG_PRAGMA_SUPPORT__=1 -include "cuda_runtime.h" -m64 -g "vecaddLoop.cu" -o "vecaddLoop.cpp4.ii" 
#$ cudafe++ --c++14 --gnu_version=90400 --display_error_number --orig_src_file_name "vecaddLoop.cu" --orig_src_path_name "/home/chenzihang/async_benchmarks/vecadd/vecaddLoop.cu" --allow_managed  --m64 --parse_templates --gen_c_file_name "vecaddLoop.cudafe1.cpp" --stub_file_name "vecaddLoop.cudafe1.stub.c" --gen_module_id_file --module_id_file_name "vecaddLoop.module_id" "vecaddLoop.cpp4.ii" 
#$ gcc -D__CUDA_ARCH__=800 -D__CUDA_ARCH_LIST__=800 -E -x c++  -DCUDA_DOUBLE_MATH_FUNCTIONS -D__CUDACC__ -D__NVCC__  "-I/usr/local/cuda/bin/../targets/x86_64-linux/include"    -D__CUDACC_VER_MAJOR__=12 -D__CUDACC_VER_MINOR__=0 -D__CUDACC_VER_BUILD__=76 -D__CUDA_API_VER_MAJOR__=12 -D__CUDA_API_VER_MINOR__=0 -D__NVCC_DIAG_PRAGMA_SUPPORT__=1 -include "cuda_runtime.h" -m64 -g "vecaddLoop.cu" -o "vecaddLoop.cpp1.ii" 
#$ cicc --c++14 --gnu_version=90400 --display_error_number --orig_src_file_name "vecaddLoop.cu" --orig_src_path_name "/home/chenzihang/async_benchmarks/vecadd/vecaddLoop.cu" --allow_managed   -arch compute_80 -m64 --no-version-ident -ftz=0 -prec_div=1 -prec_sqrt=1 -fmad=1 --include_file_name "vecaddLoop.fatbin.c" -generate-line-info -tused --module_id_file_name "vecaddLoop.module_id" --gen_c_file_name "vecaddLoop.cudafe1.c" --stub_file_name "vecaddLoop.cudafe1.stub.c" --gen_device_file_name "vecaddLoop.cudafe1.gpu"  "vecaddLoop.cpp1.ii" -o "vecaddLoop.ptx"
#$ ptxas -arch=sm_80 -m64  --generate-line-info "vecaddLoop.ptx"  -o "vecaddLoop.sm_80.cubin" 
#$ fatbinary --create="vecaddLoop.fatbin" -64 --ident="vecaddLoop.cu" --cmdline=" --generate-line-info " --cicc-cmdline="-generate-line-info -ftz=0 -prec_div=1 -prec_sqrt=1 -fmad=1 " "--image3=kind=elf,sm=80,file=vecaddLoop.sm_80.cubin" "--image3=kind=ptx,sm=80,file=vecaddLoop.ptx" --embedded-fatbin="vecaddLoop.fatbin.c" 
#$ gcc -D__CUDA_ARCH__=800 -D__CUDA_ARCH_LIST__=800 -c -x c++  -DCUDA_DOUBLE_MATH_FUNCTIONS "-I/usr/local/cuda/bin/../targets/x86_64-linux/include"   -m64 -g "vecaddLoop.cudafe1.cpp" -o "vecaddLoop.o" 
#$ nvlink -m64 --arch=sm_80 --register-link-binaries="a_dlink.reg.c"    "-L/usr/local/cuda/bin/../targets/x86_64-linux/lib/stubs" "-L/usr/local/cuda/bin/../targets/x86_64-linux/lib" -cpu-arch=X86_64 "vecaddLoop.o"  -lcudadevrt  -o "a_dlink.sm_80.cubin" --host-ccbin "gcc"
#$ fatbinary --create="a_dlink.fatbin" -64 --ident="vecaddLoop.cu " --cmdline=" --generate-line-info " --cicc-cmdline="-generate-line-info -ftz=0 -prec_div=1 -prec_sqrt=1 -fmad=1 " -link "--image3=kind=elf,sm=80,file=a_dlink.sm_80.cubin" --embedded-fatbin="a_dlink.fatbin.c" 
#$ gcc -D__CUDA_ARCH_LIST__=800 -c -x c++ -DFATBINFILE="\"a_dlink.fatbin.c\"" -DREGISTERLINKBINARYFILE="\"a_dlink.reg.c\"" -I. -D__NV_EXTRA_INITIALIZATION= -D__NV_EXTRA_FINALIZATION= -D__CUDA_INCLUDE_COMPILER_INTERNAL_HEADERS__  "-I/usr/local/cuda/bin/../targets/x86_64-linux/include"    -D__CUDACC_VER_MAJOR__=12 -D__CUDACC_VER_MINOR__=0 -D__CUDACC_VER_BUILD__=76 -D__CUDA_API_VER_MAJOR__=12 -D__CUDA_API_VER_MINOR__=0 -D__NVCC_DIAG_PRAGMA_SUPPORT__=1 -m64 -g "/usr/local/cuda/bin/crt/link.stub" -o "a_dlink.o" 
#$ g++ -D__CUDA_ARCH_LIST__=800 -m64 -g -Wl,--start-group "a_dlink.o" "vecaddLoop.o"   "-L/usr/local/cuda/bin/../targets/x86_64-linux/lib/stubs" "-L/usr/local/cuda/bin/../targets/x86_64-linux/lib"  -lcudadevrt  -lcudart_static  -lrt -lpthread  -ldl  -Wl,--end-group -o "a.out" 
```

2. 使用`gcc`和`cudafe++`等编译工具生成部分中间文件

```
gcc -D__CUDA_ARCH_LIST__=800 -E -x c++ -D__CUDACC__ -D__NVCC__  "-I/usr/local/cuda/bin/../targets/x86_64-linux/include"    -D__CUDACC_VER_MAJOR__=12 -D__CUDACC_VER_MINOR__=0 -D__CUDACC_VER_BUILD__=76 -D__CUDA_API_VER_MAJOR__=12 -D__CUDA_API_VER_MINOR__=0 -D__NVCC_DIAG_PRAGMA_SUPPORT__=1 -include "cuda_runtime.h" -m64 -g "vecaddLoop.cu" -o "vecaddLoop.cpp4.ii"
```

```
/usr/local/cuda/bin/cudafe++ --c++14 --gnu_version=90400 --display_error_number --orig_src_file_name "vecaddLoop.cu" --orig_src_path_name "/home/chenzihang/async_benchmarks/vecadd/vecaddLoop.cu" --allow_managed  --m64 --parse_templates --gen_c_file_name "vecaddLoop.cudafe1.cpp" --stub_file_name "vecaddLoop.cudafe1.stub.c" --gen_module_id_file --module_id_file_name "vecaddLoop.module_id" "vecaddLoop.cpp4.ii"
```

```
gcc -D__CUDA_ARCH__=800 -D__CUDA_ARCH_LIST__=800 -E -x c++  -DCUDA_DOUBLE_MATH_FUNCTIONS -D__CUDACC__ -D__NVCC__  "-I/usr/local/cuda/bin/../targets/x86_64-linux/include"    -D__CUDACC_VER_MAJOR__=12 -D__CUDACC_VER_MINOR__=0 -D__CUDACC_VER_BUILD__=76 -D__CUDA_API_VER_MAJOR__=12 -D__CUDA_API_VER_MINOR__=0 -D__NVCC_DIAG_PRAGMA_SUPPORT__=1 -include "cuda_runtime.h" -m64 -g "vecaddLoop.cu" -o "vecaddLoop.cpp1.ii"
```

3. 使用`cicc`生成`ptx`文件

```
/usr/local/cuda/nvvm/bin/cicc --c++14 --gnu_version=90400 --display_error_number --orig_src_file_name "vecaddLoop.cu" --orig_src_path_name "/home/chenzihang/async_benchmarks/vecadd/vecaddLoop.cu" --allow_managed   -arch compute_80 -m64 --no-version-ident -ftz=0 -prec_div=1 -prec_sqrt=1 -fmad=1 --include_file_name "vecaddLoop.fatbin.c" -generate-line-info -tused --module_id_file_name "vecaddLoop.module_id" --gen_c_file_name "vecaddLoop.cudafe1.c" --stub_file_name "vecaddLoop.cudafe1.stub.c" --gen_device_file_name "vecaddLoop.cudafe1.gpu"  "vecaddLoop.cpp1.ii" -o "vecaddLoop.ptx"
```

---

**任务二：** 理解`vecaddLoop.ptx`



**参考资料：** https://docs.nvidia.com/cuda/parallel-thread-execution/index.html#source-format

---

**任务三：** 使用`asm()`对`cube.cu`中的kernel插入Inline PTX Assembly



**代码地址：** https://github.com/parcomp-group/async_benchmarks/blob/main/PTX-samples/cube.cu



**参考资料：** 

* https://docs.nvidia.com/cuda/inline-ptx-assembly/index.html

* https://docs.nvidia.com/cuda/parallel-thread-execution/index.html#source-format




### 以`ld.shared.f32`为例

`ld`的一种常用语法格式为`ld.{ss}.type d, [a]`，意思为在指定状态空间中，从源地址操作数`a`指定的位置加载数据存储到寄存器变量`d`

* 常用`ss`包括`const`， `global`， `shared`以及 `param`等
* 常用`type`包括`b8`，`u32`，`s64`，`f32`等

因此，`ld.shared.f32 %0, [%1]`的意思为从地址为`%1`的内存空间取一个`f32`的值存储到`%0`寄存器中。`asm()`函数的格式为`asm("template-string" : "constraint"(output) : "constraint"(input));`。函数中的`template-string`为需要插入的PTX指令，`constraint`则是对于插入的PTX指令中的变量类型的描述。例如，给定代码中的`=f`意为`.f32`类型的寄存器并且允许写入（如果为`+f`，则意为可读可写）。因此，`asm volatile("ld.shared.f32 %0, [%1];" : "=f"(val2) : "r"(smem_offset_addr));`意为从以`smem_offset_addr`为地址的shared memory内存空间中取出`f32`类型的值并存储到`val2`中。值得注意的是，为了避免编译器对所写PTX指令的优化，可以在`asm`关键字后加上`volatile`关键字。

```
float val2;
int smem_offset_addr;
asm volatile("ld.shared.f32 %0, [%1];" : "=f"(val2) : "r"(smem_offset_addr));
```

> 1. compiler前端并不会解析asm的template string，因此无法传递操作数修饰符
> 2. 如果想要在PTX指令中使用`%`符号，则需要在asm中使用`%%`



### 原始的Cube kernel代码以及修改后的代码

```
__global__ void cube(float *d_in, float *d_out) {
    int idx = threadIdx.x;
    __shared__ float sdata[100];
    sdata[idx] = d_in[idx];
    __syncthreads();
    d_out[idx] = sdata[idx] * sdata[idx] * sdata[idx];
}
```

```
__global__ void cubePTX(float *d_in, float *d_out) {
    int idx;
    asm(".shared .align 4 .b32 __smem[400];");
    asm volatile("mov.u32 %0, %%tid.x;" : "=r"(idx));

    float val;
    asm volatile("ld.global.f32 %0, [%1];" : "=f"(val) : "l"(d_in + idx));

    int idxInBytes;
    asm volatile("shl.b32 %0, %1, 2;" : "=r"(idxInBytes) : "r"(idx));

    int smem_addr;
    asm volatile("mov.u32 %0, __smem;" : "=r"(smem_addr));

    int smem_offset_addr;
    asm volatile("add.s32 %0, %1, %2;" : "=r"(smem_offset_addr) : "r"(smem_addr), "r"(idxInBytes));
    asm volatile("st.shared.f32 [%0], %1;" : : "r"(smem_offset_addr), "f"(val));
    asm volatile("bar.sync 0;");

    float val2;
    asm volatile("ld.shared.f32 %0, [%1];" : "=f"(val2) : "r"(smem_offset_addr));

    float temp1, temp2;
    asm volatile("mul.f32 %0, %1, %2;" : "=f"(temp1) : "f"(val2), "f"(val2));
    asm volatile("mul.f32 %0, %1, %2;" : "=f"(temp2) : "f"(temp1), "f"(val2));
    asm volatile("st.global.f32 [%0], %1;" : : "l"(d_out + idx), "f"(temp2));
}
```
