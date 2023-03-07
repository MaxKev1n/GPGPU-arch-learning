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



### 任务一：如何使用MGPUSim运行一个应用



### 任务二：如何使用MGPUSim运行一个GNN workload



### 任务三：如何快速使用MGPUSim进行实验

1. 创建实验所需文件夹，例如`MGPUSim_Exp`

```
mkdir -p MGPUSim_Exp
cd MGPUSim_Exp
```

2. 在实验文件夹中创建Go Module

```
go mod init github.com/YourName/MGPUSim_Exp
```

3. 将`MGPUSim`仓库中的`runner`文件夹移入并添加依赖

```
cp -r ../mgpusim/samples/runner ./
go mod tidy
```

4. 使用`sample`中的案例进行实验，以`relu`为例

```
cp -r ../mgpusim/samples/relu ./
cd relu
```



在进行实验之前需要对`relu/main.go`中的依赖进行修改，如下所示

<img src="./pictures/mgpusim_task3/1.png" style="zoom:67%;float:left" />

<img src="./pictures/mgpusim_task3/2.png" style="zoom:67%;float:left" />

```
go build
/relu -timing --length 65535 --report-all
```

