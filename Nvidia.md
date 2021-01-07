# Ubuntu
## Uninstall
### Nvidia Drive
```
sudo apt-get --purge remove nvidia*
sudo apt autoremove
```
### Cuda
```
sudo apt-get --purge remove "*cublas*" "cuda*"
sudo apt autoremove
```
## Install
### Nvidia Drive
```
ubuntu-drivers devices
select driver version
apt-get install nvidia-driver-440-server

nvidia-smi 
Mon Dec 21 13:12:14 2020       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 450.80.02    Driver Version: 450.80.02    CUDA Version: 11.0     |
|-------------------------------+----------------------+------------------
```
### Cuda
```
download cuda package
https://developer.nvidia.com/zh-cn/cuda-downloads

cd /cudapackage
sudo sh cuda_9.0.176_384.81_linux.run
choose yes or no depending on the situation
        
echo export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-9.0/lib64 >> ~/.bashrc
echo export PATH=$PATH:/usr/local/cuda-9.0/bin >> ~/.bashrc
echo export CUDA_HOME=$CUDA_HOME:/usr/local/cuda-9.0 >> ~/.bashrc

nvcc -V
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2018 NVIDIA Corporation
Built on Sat_Aug_25_21:08:01_CDT_2018
Cuda compilation tools, release 10.0, V10.0.130

rm -r /cudapackage
```
### Cudnn
```
download cudnn package
cd /cudnnpackage
tar -xf cudnn-10.0-linux-x64-v7.6.4.38.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include/
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64/
sudo chmod a+r /usr/local/cuda/include/cudnn.h
sudo chmod a+r /usr/local/cuda/lib64/libcudnn*

cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
#define CUDNN_MAJOR 7
#define CUDNN_MINOR 6
#define CUDNN_PATCHLEVEL 4
--
#define CUDNN_VERSION (CUDNN_MAJOR * 1000 + CUDNN_MINOR * 100 + CUDNN_PATCHLEVEL)

#include "driver_types.h"
```
# Tip
## cuda和nvidia的版本对应关系 
[参考链接](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html)
## tensflow与cuda以及cudnn版本对应关系
[参考链接](https://tensorflow.google.cn/install/source)
