### nvprof到ncu迁移

```
======== Warning: Skipping profiling on device 0 since profiling is not supported on devices with compute capability 7.5 and higher.
                  Use NVIDIA Nsight Compute for GPU profiling and NVIDIA Nsight Systems for GPU tracing and CPU sampling.
                  Refer https://developer.nvidia.com/tools-overview for more details.
```


#### `gld_throughput` -> `l1tex__t_bytes_pipe_lsu_mem_global_op_ld.sum.per_second`

用gld_throughput指标检查内核的内存读取效率

nvprof

```
$ nvprof --metrics gld_throughput ./sumMatrix 32 32
```

ncu

```
$ sudo /usr/local/NVIDIA-Nsight-Compute/ncu --metrics l1tex__t_bytes_pipe_lsu_mem_global_op_ld.sum.per_second ./sumMatrix 32 32
==PROF== Connected to process 25364 (/home/hexu/git/Professional.CUDA.C.Programming/CodeSamples/chapter03/sumMatrix)
==PROF== Profiling "sumMatrixOnGPU2D" - 1: 0%....50%....100% - 1 pass
sumMatrixOnGPU2D <<<(512,512), (32,32)>>> elapsed 189.383030 ms
==PROF== Disconnected from process 25364
[25364] sumMatrix@127.0.0.1
  sumMatrixOnGPU2D(float*, float*, float*, int, int), 2024-Aug-27 14:55:57, Context 1, Stream 7
    Section: Command line profiler metrics
    ---------------------------------------------------------------------- --------------- ------------------------------
    l1tex__t_bytes_pipe_lsu_mem_global_op_ld.sum.per_second                   Gbyte/second                          64.43
    ---------------------------------------------------------------------- --------------- ------------------------------
```

#### `gld_efficiency` -> `smsp__sass_average_data_bytes_per_sector_mem_global_op_ld.pct`

用gld_efficiency指标检测全局加载效率，即被请求的全局加载吞吐量占所需的全局加载吞吐量的比值。
它衡量了应用程序的加载操作利用设备内存带宽的程度。

nvprof

```
$ nvprof --metrics gld_efficiency ./sumMatrix 32 32 
```

ncu

```
$ sudo /usr/local/NVIDIA-Nsight-Compute/ncu --metrics smsp__sass_average_data_bytes_per_sector_mem_global_op_ld.pct ./sumMatrix 32 32
==PROF== Connected to process 26982 (/home/hexu/git/Professional.CUDA.C.Programming/CodeSamples/chapter03/sumMatrix)
==PROF== Profiling "sumMatrixOnGPU2D" - 1: 0%....50%....100% - 1 pass
sumMatrixOnGPU2D <<<(512,512), (32,32)>>> elapsed 286.754847 ms
==PROF== Disconnected from process 26982
[26982] sumMatrix@127.0.0.1
  sumMatrixOnGPU2D(float*, float*, float*, int, int), 2024-Aug-27 15:27:02, Context 1, Stream 7
    Section: Command line profiler metrics
    ---------------------------------------------------------------------- --------------- ------------------------------
    smsp__sass_average_data_bytes_per_sector_mem_global_op_ld.pct                        %                            100
    ---------------------------------------------------------------------- --------------- ------------------------------
```


#### `achieved_occupancy` -> `sm__warps_active.avg.pct_of_peak_sustained_active`

achieved_occupancy参数：每个sm在每个cycle能够达到的最大activewarp 占总warp的比例。

nvprof

```
$ nvprof --metrics achieved_occupancy ./sumMatrix 64 2
```

ncu

```
$ sudo /usr/local/NVIDIA-Nsight-Compute/ncu --metrics sm__warps_active.avg.pct_of_peak_sustained_active ./sumMatrix 64 2
==PROF== Connected to process 27152 (/home/hexu/git/Professional.CUDA.C.Programming/CodeSamples/chapter03/sumMatrix)
==PROF== Profiling "sumMatrixOnGPU2D" - 1: 0%....50%....100% - 1 pass
sumMatrixOnGPU2D <<<(256,8192), (64,2)>>> elapsed 166.579008 ms
==PROF== Disconnected from process 27152
[27152] sumMatrix@127.0.0.1
  sumMatrixOnGPU2D(float*, float*, float*, int, int), 2024-Aug-27 15:33:28, Context 1, Stream 7
    Section: Command line profiler metrics
    ---------------------------------------------------------------------- --------------- ------------------------------
    sm__warps_active.avg.pct_of_peak_sustained_active                                    %                          93.26
    ---------------------------------------------------------------------- --------------- ------------------------------
```
