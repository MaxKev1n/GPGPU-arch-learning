# MGPUSim Usage Document

## Hsaco Preparation

1. **Prepare for ROCm compilation toolchain (Docker *recommanded*) [1, 2]**

```bash
docker pull rocm/dev-ubuntu-20.04:6.3.3-complete
```

```bash
docker run -it -u root -v $(pwd):/mnt --privileged --device=/dev/kfd --device=/dev/dri --group-add video --ipc=host --shm-size 32G --name llmc-rocm rocm/dev-ubuntu-20.04:6.3.3-complete
```


2. Compile the hip source code

```bash
hipcc vectoradd.hip --save-temps --offload-arch=gfx90a -mcode-object-version=4 -g -O3 -fno-omit-frame-pointer -o vectoradd
```

The following contents is the meanings of compilation flags:

> --save-temps: generate the intermedaite files during the process of compilation.

> --offload-arch: specify the instruction set architecture, e.g. --offload-arch=gfx908.

> -mcode-object-version: specify the code object version of compiled executable file, e.g. -mcode-object-version=4. We recommend the programmer set the code object version to *4*, as we only implement code object version 2 and 4 in MGPUSim.

3. **List files in executable file `vectoradd`**

```bash
roc-obj-ls vectoradd
```

```bash
1       host-x86_64-unknown-linux--       file://vectoradd#offset=12288&size=0
1       hipv4-amdgcn-amd-amdhsa--gfx90a       file://vectoradd#offset=12288&size=31688
```

4. **Extract `.co` file from listed amdhsa file `file://vectoradd#offset=12288&size=31688`**

```bash
roc-obj-extract file://vectoradd#offset=12288&size=31688
```

```bash
vectoradd-offset12288-size71520.co
```

5. **Rename `.co` file to `.hsaco` file for MGPUSim**

``` bash
mv vectoradd-offset12288-size71520.co vectoradd_gfx90a.hsaco
```

6. **(optional) Disassemble hsaco file**

```bash
/opt/rocm-6.3.3/llvm/bin/llvm-objdump -d vectoradd.hsaco
```

## Important Issues

### Padding in the arguments of MGPUSim benchmark

In ROCm, the arguments of hip kernels have their own offset, size and alignment. We can use the following command to show the relevant information.

```bash
/opt/rocm-6.3.3/llvm/bin/llvm-readobj --notes vectoradd.hsaco
```

```assembly
File: vectoradd.hsaco
Format: elf64-amdgpu
Arch: amdgcn
AddressSize: 64bit
LoadName: <Not found>
Notes [
  NoteSection {
    Name: .note
    Offset: 0x238
    Size: 0x294
    Note {
      Owner: AMDGPU
      Data size: 0x280
      Type: NT_AMDGPU_METADATA (AMDGPU Metadata)
      AMDGPU Metadata: ---
amdhsa.kernels:
  - .agpr_count:     0
    .args:
      - .offset:         0
        .size:           4
        .value_kind:     by_value
      - .address_space:  global
        .offset:         8
        .size:           8
        .value_kind:     global_buffer
      - .address_space:  global
        .offset:         16
        .size:           8
        .value_kind:     global_buffer
      - .address_space:  global
        .offset:         24
        .size:           8
        .value_kind:     global_buffer
    .group_segment_fixed_size: 0
    .kernarg_segment_align: 8
    .kernarg_segment_size: 32
    .language:       OpenCL C
    .language_version:
      - 2
      - 0
    .max_flat_workgroup_size: 1024
    .name:           _Z9vectorAddiPiS_S_
    .private_segment_fixed_size: 0
    .sgpr_count:     38
    .sgpr_spill_count: 0
    .symbol:         _Z9vectorAddiPiS_S_.kd
    .vgpr_count:     8
    .vgpr_spill_count: 0
    .wavefront_size: 64
amdhsa.target:   amdgcn-amd-amdhsa--gfx90a
amdhsa.version:
  - 1
  - 1
...

    }
  }
]
```

As shown in the content, the sizes of each arguments are 4 bytes, 8 bytes, 8 bytes and 8 bytes, repectively. Thus, the offsets of each arguments are 0, 8, 16, 24. To implement a corresponding `vectoradd` benchmark in MGPUSim, we will firstly declare a `struct` to convert the kernel as a Go struct and pass the arguments into the simulated kernel as shown in the following code[3]. 

```go
type KernelArgs struct {
    N                   int32
    A              driver.GPUPtr
    B              driver.GPUPtr
    C               driver.GPUPtr
    HiddenGlobalOffsetX int64
    HiddenGlobalOffsetY int64
    HiddenGlobalOffsetZ int64
}
```

However, the size of a int32 variable `N` in Go is 4 bytes, which causes the offset of next varibale `A` equals to 4. Therefore, when we pass the declared Go struct argument to MGPUSim, the simulator will set the value of second arguemnt to the combination of the higher 4 bytes of variable `A` and the lower 4 bytes of varible `B`as the offset of second argument in code object is 8.

To solve this problem, we have to insert a 4 bytes padding varible `Padding` to zero-padding the meaningless 4 bytes between varible `N` and varible `A` as shown in the following code.

```go
type KernelArgs struct {
    N                   int32
    Padding       int32
    A              driver.GPUPtr
    B              driver.GPUPtr
    C               driver.GPUPtr
    HiddenGlobalOffsetX int64
    HiddenGlobalOffsetY int64
    HiddenGlobalOffsetZ int64
}
```


## Reference

[1] https://rocm.docs.amd.com/projects/install-on-linux/en/latest/install/detailed-install.html

[2] https://rocm.docs.amd.com/projects/install-on-linux/en/latest/how-to/docker.html

[3] https://github.com/sarchlab/mgpusim/blob/v4/doc/prepare_benchmarks.md#run-a-kernel

## Appendix

### Source Code of Vector Add

```c++
#include <hip/hip_runtime.h>
#include <iostream>

// HIP kernel for vector addition
__global__ void vectorAdd(int N, int* A, int* B, int* C) {
    int idx = blockIdx.x * blockDim.x + threadIdx.x;
    if (idx < N) {
        C[idx] = A[idx] + B[idx];
    }
}

int main() {
    int N = 1024; // Size of vectors
    size_t size = N * sizeof(int);

    // Allocate host memory
    int* h_A = (int*)malloc(size);
    int* h_B = (int*)malloc(size);
    int* h_C = (int*)malloc(size);

    // Initialize host vectors
    for (int i = 0; i < N; i++) {
        h_A[i] = i;
        h_B[i] = i * 2;
    }

    // Allocate device memory
    int *d_A, *d_B, *d_C;
    hipMalloc(&d_A, size);
    hipMalloc(&d_B, size);
    hipMalloc(&d_C, size);

    // Copy vectors from host to device
    hipMemcpy(d_A, h_A, size, hipMemcpyHostToDevice);
    hipMemcpy(d_B, h_B, size, hipMemcpyHostToDevice);

    // Define block size and grid size
    int blockSize = 256;
    int gridSize = (N + blockSize - 1) / blockSize;

    // Launch the vector addition kernel
    hipLaunchKernelGGL(vectorAdd, dim3(gridSize), dim3(blockSize), 0, 0, N, d_A, d_B, d_C);

    // Copy result from device to host
    hipMemcpy(h_C, d_C, size, hipMemcpyDeviceToHost);

    // Verify the result
    bool success = true;
    for (int i = 0; i < N; i++) {
        if (h_C[i] != h_A[i] + h_B[i]) {
            success = false;
            break;
        }
    }

    if (success) {
        std::cout << "Vector addition successful!" << std::endl;
    } else {
        std::cout << "Vector addition failed!" << std::endl;
    }

    // Free device memory
    hipFree(d_A);
    hipFree(d_B);
    hipFree(d_C);

    // Free host memory
    free(h_A);
    free(h_B);
    free(h_C);

    return 0;
}
```

### Assembly Code of Vector Add

```assembly
vectoradd.hsaco:        file format elf64-amdgpu

Disassembly of section .text:

0000000000001700 <_Z9vectorAddiPiS_S_>:
        s_load_dword s0, s[4:5], 0x4                               // 000000001700: C0020002 00000004
        s_load_dword s1, s[6:7], 0x0                               // 000000001708: C0020043 00000000
        s_mov_b32 s33, 0                                           // 000000001710: BEA10080
        s_waitcnt lgkmcnt(0)                                       // 000000001714: BF8CC07F
        s_and_b32 s0, s0, 0xffff                                   // 000000001718: 8600FF00 0000FFFF
        s_mul_i32 s8, s8, s0                                       // 000000001720: 92080008
        v_add_u32_e32 v0, s8, v0                                   // 000000001724: 68000008
        v_cmp_gt_i32_e32 vcc, s1, v0                               // 000000001728: 7D880001
        s_and_saveexec_b64 s[0:1], vcc                             // 00000000172C: BE80206A
        s_cbranch_execz 25                                         // 000000001730: BF880019 <_Z9vectorAddiPiS_S_+0x98>
        s_load_dwordx4 s[0:3], s[6:7], 0x8                         // 000000001734: C00A0003 00000008
        s_load_dwordx2 s[4:5], s[6:7], 0x18                        // 00000000173C: C0060103 00000018
        v_ashrrev_i32_e32 v1, 31, v0                               // 000000001744: 2202009F
        v_lshlrev_b64 v[0:1], 2, v[0:1]                            // 000000001748: D28F0000 00020082
        s_waitcnt lgkmcnt(0)                                       // 000000001750: BF8CC07F
        v_mov_b32_e32 v3, s3                                       // 000000001754: 7E060203
        v_add_co_u32_e32 v2, vcc, s2, v0                           // 000000001758: 32040002
        v_addc_co_u32_e32 v3, vcc, v3, v1, vcc                     // 00000000175C: 38060303
        v_mov_b32_e32 v5, s1                                       // 000000001760: 7E0A0201
        v_add_co_u32_e32 v4, vcc, s0, v0                           // 000000001764: 32080000
        v_addc_co_u32_e32 v5, vcc, v5, v1, vcc                     // 000000001768: 380A0305
        global_load_dword v6, v[4:5], off                          // 00000000176C: DC508000 067F0004
        global_load_dword v7, v[2:3], off                          // 000000001774: DC508000 077F0002
        v_mov_b32_e32 v2, s5                                       // 00000000177C: 7E040205
        v_add_co_u32_e32 v0, vcc, s4, v0                           // 000000001780: 32000004
        v_addc_co_u32_e32 v1, vcc, v2, v1, vcc                     // 000000001784: 38020302
        s_waitcnt vmcnt(0)                                         // 000000001788: BF8C0F70
        v_add_u32_e32 v2, v7, v6                                   // 00000000178C: 68040D07
        global_store_dword v[0:1], v2, off                         // 000000001790: DC708000 007F0200
        s_endpgm                                                   // 000000001798: BF810000
```
