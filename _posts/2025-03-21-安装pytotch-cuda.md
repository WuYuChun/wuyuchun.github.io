---
layout: post
title:  "安装pytorch-cuda"
date:   2025-03-05 11:59 PM
excerpt:
tag:
- 深度学习
---


listi

[9:54 PM]

# 安装pytorch

## 安装Miniconda3

```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

bash Miniconda3-latest-Linux-x86_64.sh

# 一直回车 yes

```

## 查看当前电脑的显卡

```
nvidia-smi
Fri Mar 21 19:29:11 2025       
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 535.183.01             Driver Version: 535.183.01   CUDA Version: 12.2     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  NVIDIA GeForce RTX 3060        Off | 00000000:01:00.0 Off |                  N/A |
| 30%   35C    P8              14W / 170W |     16MiB / 12288MiB |      0%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
                                                                                         
+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|    0   N/A  N/A      1136      G   /usr/lib/xorg/Xorg                            4MiB |
|    0   N/A  N/A      2107      G   /usr/lib/xorg/Xorg                            4MiB |
+---------------------------------------------------------------------------------------+
```


## 安装cuda

下载 
```
sudo dpkg -i cuda-repo-ubuntu2004-12-2-local_12.2.0-535.54.03-1_amd64.deb

wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin

sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600

sudo cp /var/cuda-repo-ubuntu2004-12-2-local/cuda-73067DC7-keyring.gpg /usr/share/keyrings/

sudo apt-get -y install cuda


```


## 安装 cudnn

```
sudo dpkg -i cudnn-local-repo-ubuntu2004-8.9.7.29_1.0-1_amd64.deb

sudo cp /var/cudnn-local-repo-ubuntu2004-8.9.7.29/cudnn-local-*-keyring.gpg

sudo apt update

sudo apt install libcudnn8=8.9.7.29-1+cuda12.2
```


## conda 安装 pytorch

```
conda create -n pytorch
conda avictiave

$ conda install pytorch-gpu torchvision torchaudio pytorch-cuda=12.1 -c pytorch -c nvidia

```

## 验证

```
>>> import torch 
>>> torch.cuda.is_available()                     
True                                                    
>>> print(torch.version.cuda)       
11.8   
```


