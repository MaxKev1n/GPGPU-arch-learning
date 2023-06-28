# GPGPU-arch-learning

## MGPUSim

**推荐课程：**[W&M CSCI780-01 Spring 23 Computer Arch. Mdl and Sim (notion.site)](https://syifan.notion.site/W-M-CSCI780-01-Spring-23-Computer-Arch-Mdl-and-Sim-a5102523b841499dbbd0ff893f8495a6)

**MGPUSim**: [Akita / MGPUSim · GitLab](https://gitlab.com/akita/mgpusim)

**citation**:

```
@inproceedings{sun19mgpusim, 
    author = {Sun, Yifan and Baruah, Trinayan and Mojumder, Saiful A. and Dong, Shi and Gong, Xiang and Treadway, Shane and Bao, Yuhui and Hance, Spencer and McCardwell, Carter and Zhao, Vincent and Barclay, Harrison and Ziabari, Amir Kavyan and Chen, Zhongliang and Ubal, Rafael and Abell\'{a}n, Jos\'{e} L. and Kim, John and Joshi, Ajay and Kaeli, David}, 
    title = {MGPUSim: Enabling Multi-GPU Performance Modeling and Optimization}, 
    year = {2019}, 
    isbn = {9781450366694}, 
    publisher = {Association for Computing Machinery}, 
    address = {New York, NY, USA}, 
    url = {https://doi.org/10.1145/3307650.3322230}, 
    doi = {10.1145/3307650.3322230}, 
    booktitle = {Proceedings of the 46th International Symposium on Computer Architecture}, 
    pages = {197–209}, 
    numpages = {13}, 
    keywords = {simulation, multi-GPU systems, memory management}, 
    location = {Phoenix, Arizona}, 
    series = {ISCA '19} 
}
```



> ⚠️遇到../runner/timingplatform.go:19:2: gitlab.com/akita/noc/v3@v3.0.0-alpha.9: Get "https://proxy.golang.org/gitlab.com/akita/noc/v3/@v/v3.0.0-alpha.9.zip": dial tcp 172.217.163.49:443: i/o timeout类的问题？
>
> ```go
> go env -w GOPROXY=https://goproxy.io,direct
> ```



* **任务一：如何使用MGPUSim运行一个应用** ✅
* **任务二：如何使用MGPUSim运行一个GNN workload**
* **任务三：如何快速使用MGPUSim进行实验** ✅

---

## GCN

**推荐阅读资料**：

* https://tkipf.github.io/graph-convolutional-networks/



**参考代码**：https://github.com/tkipf/pygcn

**citation**

```
@article{kipf2016semi,
  title={Semi-Supervised Classification with Graph Convolutional Networks},
  author={Kipf, Thomas N and Welling, Max},
  journal={arXiv preprint arXiv:1609.02907},
  year={2016}
}
```



---

## CUDA

> ⚠️在HPC上使用**vscode**时可能遇到c++扩展无法正确工作：
>
> 1. 根据系统安装cuda（以CUDA 12.1为例）
>
> ```
> wget https://developer.download.nvidia.com/compute/cuda/12.1.1/local_installers/cuda_12.1.1_530.30.02_linux.run
> ```
>
> ```
> chmod 777 cuda_12.1.1_530.30.02_linux.run
> ```
>
> ```
> sh cuda_12.1.1_530.30.02_linux.run
> ```
>
> 2. 修改vscode的`c_cpp_properties.json`
>
> ```json
> {
>     "configurations": [
>         {
>             "name": "Linux",
>             "includePath": [
>                 "${workspaceFolder}/**",
>                 "/hpc/users/HKUST-GZ/zihangchen/cuda/cuda-11.4/include"
>             ],
>             "defines": [],
>             "compilerPath": "/usr/bin/gcc",
>             "cStandard": "c17",
>             "cppStandard": "gnu++14",
>             "intelliSenseMode": "linux-gcc-x64"
>         }
>     ],
>     "version": 4
> }
> ```
>
> 
>
> **参考资料**：https://blog.csdn.net/qq_35082030/article/details/110387800



* **任务一：** 使用`cuda toolkit`编译`vecaddLoop.cu`得到`vecaddLoop.ptx`✅

---

## Rodinia

**下载链接**：https://rodinia.cs.virginia.edu/doku.php?id=downloads

> ⚠️在HPC上使用`Rodinia`时可能遇到无法正常运行：
>
> 修改`rodinia_3.1/common/make.config`第二行
>
> ```makefile
> CUDA_DIR = /usr/local/cuda #original code
> ```
>
> ```makefile
> CUDA_DIR = /usr/local/cuda-11.4 #以module load cuda-11.4为例
> ```

---

## Performance Evaluation Methods

**citation:**

> Eeckhout, Lieven. “Computer Architecture Performance Evaluation Methods.” Synthesis Lectures on Computer Architecture, edited by Mark D Hill, Morgan & Claypool Publishers, 2010, doi:10.2200/S00273ED1V01Y201006CAC010.



### STP

We, first, define a program’s normalized progress as $NP_i=\frac{T_i^{SP}}{T_i^{MP}}$, with $T_i^{SP}$ and $T_i^{MP}$, the execution time undersingle-program mode (i.e.,the program runs in isolation) and multiprogram execution (i.e., the program co-runs with other programs), respectively. Given that a program runs slower under multiprogram execution, normalized progress is a value smaller than one. The intuitive understanding of normalized progress is that it represents a pro- gram’s progress during multiprogram execution. For example, an NP of 0.7 means that a program makes 7 milliseconds of single-program progress during a 10 millisecond time slice of multiprogram execution.

​		System throughput (STP) is then defined as the sum of the normalized progress rates across all jobs in the multiprogram job mix: $STP=\sum_{i=1}^nNP_i=\sum_{i=1}^n\frac{T_i^{SP}}{T_i^{MP}}$.
In other words, system throughput is the accumulated progress across all jobs, and thus it is a higher-is-better metric.



### ANTT

To define the average normalized turnaround time, we first define a program’s normalized turnaround time as $NTT_i=\frac{T_i^{MP}}{T_i^{SP}}$
Normalized turnaround time quantifies the user-perceived slowdown during multiprogram execution relative to single-program execution, and typically is a value larger than one. NTT is the reciprocal of NP. The average normalized turnaround time is defined as the arithmetic average across the programs’ normalized turnaround times: $ANTT=\frac{1}{n}\sum_{i=1}^nNTT_i=\frac{1}{n}\sum_{i=1}^n\frac{T_i^{MP}}{T_i^{SP}}$
ANTT is a lower-is-better metric. 

---

## Nsight Compute

### Nsight Compute UI

**参考资料：** https://developer.nvidia.com/tools-overview/nsight-compute/get-started

> **NCU 提供MacOS host版本**

<img src="nsight-compute/nsight-compute-ui.png" style="zoom: 50%;" />

* **任务一：如何生成Report并查看Source与PTX对应关系**✅



### Nsight Compute CLI

**参考资料：** https://docs.nvidia.com/nsight-compute/NsightComputeCli/index.html#command-line-options-console-output



* **任务一：如何查看kernel的occupancy**✅

* **任务二：如何查看cuda代码对应的sass代码**✅



---

## Occupancy

* **任务一：如何在cuda代码中计算theoretical occupancy**✅
