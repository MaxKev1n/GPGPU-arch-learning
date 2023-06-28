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



