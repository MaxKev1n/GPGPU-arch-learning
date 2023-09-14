# Accel-Sim

* https://github.com/accel-sim/accel-sim-framework
* https://accel-sim.github.io

## Script Running 

### **Accel-Sim SASS Frontend and Simulation Engine**

#### run the workloads in Accel-Sim's SASS traces-driven mode

```bash
./util/job_launching/run_simulations.py -B $BENCHMARK_NAME -C $GPUCONFIG -T ./hw_run/traces/device-<device-num>/<cuda-version>/ -N $LAUNCHNAME
```

Eg.

```
./util/job_launching/run_simulations.py -B rodinia_2.0-ft -C QV100-SASS -T ./hw_run/rodinia_2.0-ft/11.0 -N myTest
```



**options introduction**

* `-B`：`--benchmark_list`，计划运行的一系列benchmark suit，在`apps/define-*.yml`中可以查看benchmark suit的名字
* `-C`：`--configs_list`，计划运行的GPU配置，在`configs/define-*.yml`中可以查看GPU配置
  * **Basefile Configs**包括*TITANX*，*TITANXX*，*RTX3070*，*RTX2060*，*QV100*，*QV100_SASS*等，为了保证配置的正确性，建议指定运行的config类型，例如`QV100-SASS or QV100-PTX`，同时Accel-sim提供一些可选的config，为了使用这些配置，需要使用`-`添加在Basefile Config后，例如`TITANX-L1ON-PTXPLUS`
* `-N`：`--launch_name`，用于命名该次启动，并确定Logfile的命名，如果没有确定该次启动的名字，那么该次启动将会被自动命名为当前的日期或者时间
* `-T`：`--trace_dir`，在*trace-driven mode*下将该选项传递给simulator，该选项应当是所有需要运行的trace file的根目录



#### run the workloads in Accel-Sim's PTX mode

```bash
./util/job_launching/run_simulations.py -B $BENCHMARK_NAME -C $GPUCONFIG -N $LAUNCHNAME
```

Eg.

```bash
./util/job_launching/run_simulations.py -B rodinia_2.0-ft -C QV100-PTX -N myTest
```



**options introduction**

* `-B`：`--benchmark_list`，计划运行的一系列benchmark suit，在`apps/define-*.yml`中可以查看benchmark suit的名字
* `-C`：`--configs_list`，计划运行的GPU配置，在`configs/define-*.yml`中可以查看GPU配置
  * **Basefile Configs**包括*TITANX*，*TITANXX*，*RTX3070*，*RTX2060*，*QV100*，*QV100_SASS*等，为了保证配置的正确性，建议指定运行的config类型，例如`QV100-SASS or QV100-PTX`，同时Accel-sim提供一些可选的config，为了使用这些配置，需要使用`-`添加在Basefile Config后，例如`TITANX-L1ON-PTXPLUS`
* `-N`：`--launch_name`，用于命名该次启动，并确定Logfile的命名，如果没有确定该次启动的名字，那么该次启动将会被自动命名为当前的日期或者时间
