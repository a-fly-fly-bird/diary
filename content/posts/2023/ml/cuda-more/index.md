+++
title = '英伟达官网 CUDA、CUDA toolkit 和 Conda PyTorch 里 CUDA、CUDA toolkit的关系'
date = 2023-12-16T23:59:11+08:00
draft = false
categories = ['ML/DL']
tags = ['Deep Learning', 'CUDA', 'PyTorch']
+++


参考：

[显卡，显卡驱动,nvcc, cuda driver,cudatoolkit,cudnn到底是什么？](https://zhuanlan.zhihu.com/p/91334380)

[一文讲清楚CUDA、CUDA toolkit、CUDNN、NVCC关系_健0000的博客-CSDN博客](https://blog.csdn.net/qq_41094058/article/details/116207333)

# CUDA 和 CUDA toolkit

CUDA并不是软件，而是**一个并行计算平台和编程模型，能够使得使用GPU进行通用计算变得简单和优雅。**

我们平时说的安装驱动，然后安装CUDA和cudnn，实际上指的是安装CUDA toolkit和cudnn。

> 传统上，安装 NVIDIA Driver 和 CUDA Toolkit 的步骤是分开的，但实际上我们可以直接安装 CUDA Toolkit，系统将自动安装与其版本匹配的 NVIDIA Driver。

安装网址：

[NVIDIA CUDA Toolkit 11.7 Downloads](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=20.04&target_type=deb_local)

CUDA Toolkit由以下组件组成：

[CUDA 12.1 Release Notes](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html#major-components)

> Compiler: CUDA-C和CUDA-C++编译器NVCC位于bin/目录中。它建立在NVVM优化器之上，而NVVM优化器本身构建在LLVM编译器基础结构之上。Tools: 提供一些像profiler,debuggers等工具，这些工具可以从bin/目录中获取Libraries: 下面列出的部分科学库和实用程序库可以在lib/目录中使用，它们的接口在include/目录中可获取。CUDA Samples: 演示如何使用各种CUDA和library API的代码示例。CUDA Driver: 运行CUDA应用程序需要系统至少有一个具有CUDA功能的GPU和与CUDA工具包兼容的驱动程序。

# cudnn

> NVIDIA CUDA® 深度神经网络库 (cuDNN) 是一个 GPU 加速的**深度神经网络**基元库，能够以高度优化的方式实现标准例程（如前向和反向卷积、池化层、归一化和激活层）。
> 
> 全球的深度学习研究人员和框架开发者都依赖 cuDNN 来实现高性能 GPU 加速。借助 cuDNN，研究人员和开发者可以专注于训练神经网络及开发软件应用，而不必花时间进行低层级的 GPU 性能调整。cuDNN 可加速广泛应用的深度学习框架，包括 **[Caffe2](https://caffe2.ai/)**、**[Chainer](https://chainer.org/)**、**[Keras](https://keras.io/)**、**[MATLAB](https://www.mathworks.com/solutions/deep-learning.html)**、**[MxNet](https://mxnet.incubator.apache.org/)**、**[PaddlePaddle](https://github.com/PaddlePaddle/Paddle)**、**[PyTorch](https://pytorch.org/)** 和 **[TensorFlow](https://www.tensorflow.org/)**。

这个toolkit里面不是自带的。所以需要手动安装。

# Conda PyTorch 里 CUDA、CUDA toolkit

> CUDA Toolkit (Nvidia)： CUDA完整的工具安装包，其中提供了 Nvidia 驱动程序、开发 CUDA 程序相关的开发工具包等可供安装的选项。包括 CUDA 程序的编译器、IDE、调试器等，CUDA 程序所对应的各式库文件以及它们的头文件。

```
    CUDA Toolkit (PyTorch)： CUDA不完整的工具安装包，其主要包含在使用 CUDA 相关的功能时所依赖的动态链接库。**不会安装驱动程序**。

    对于 Pytorch 之类的深度学习框架而言，其在大多数需要使用 GPU 的情况中只需要使用 CUDA 的动态链接库支持程序的运行(**Pytorch 本身与 CUDA 相关的部分是提前编译好的)**，就像常见的可执行程序一样，不需要重新进行编译过程，只需要其所依赖的动态链接库存在即可正常运行。

```

# conda安装的cudatoolkit, cudnn与在主机上安装的cuda, cudnn有何关系

首先明确了以下概念：

- 本地安装的Nvidia CUDA Toolkit 是 conda 里 toolkit 的超集
- 运行PyTorch只需要安装驱动和conda里的toolkit即可

## 查找可用的cuda路径

1、环境变量CUDA_HOME 或 CUDA_PATH 2、/usr/local/cuda 3、which nvcc的上级上级目录 （which nvcc 会在环境变量PATH中找） 4、如果上述都不存在，则torch.utils.cpp_extension.CUDA_HOME为None，会使用conda安装的cudatoolkit，其路径为cudart 库文件目录的上级目录（此时可能是通过 conda 安装的 cudatoolkit，一般直接用 conda install cudatoolkit，就是在这里搜索到 cuda 库的）。

## 实践

根据实践，当同时存在conda里的toolkit和本地的toolkit时，会优先调用conda里的环境。

参考：

[conda安装的cudatoolkit, cudnn与在主机上安装的cuda, cudnn有何关系？ - 知乎](https://www.zhihu.com/question/344950161/answer/819075473)

# 其他

参考：

[Pytorch 使用不同版本的 cuda - yhjoker - 博客园](https://www.cnblogs.com/yhjoker/p/10972795.html)
