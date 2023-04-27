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
> **参考资料：**https://blog.csdn.net/qq_35082030/article/details/110387800
