# 自动化 ML&systems R&D

在发布 CK 之后，我们开始与社区合作，逐步将 ML&systems 研发中最常见和重复的任务[自动化](introduction.md#how-ck-supports-collaborative-and-reproducible-mlsystems-research) （参见[期刊文章](https://arxiv.org/pdf/2011.01149.pdf)和 [FastPath 的 20 次演示](https://doi.org/10.5281/zenodo.4005773)）。

我们开始使用统一的 API 和 I/O 添加以下 CK 模块和操作。

## 平台及环境检测

这些 CK 模块自动化并统一了对用户平台和环境的不同属性的检测。

* *module:os* [[API](https://cknowledge.io/c/module/platform/#api)] [[components](https://cKnowledge.io/c/os)]
* *module:platform* [[API](https://cknowledge.io/c/module/platform/#api)]
* *module:platform.os* [[API](https://cknowledge.io/c/module/platform.os/#api)]
* *module:platform.cpu* [[API](https://cknowledge.io/c/module/platform.cpu/#api)]
* *module:platform.gpu* [[API](https://cknowledge.io/c/module/platform.gpu/#api)]
* *module:platform.gpgpu* [[API](https://cknowledge.io/c/module/platform.gpgpu/#api)]
* *module:platform.nn* [[API](https://cknowledge.io/c/module/platform.nn/#api)]

例子：

```bash
ck pull repo:mlcommons@ck-mlops

ck detect platform
ck detect platform.gpgpu --cuda
```

## 软件检测

该 CK 模块使用 CK 名称、UID 和 tag，在给定的平台上自动检测给定的软件或文件（数据集、模型、库、编译器、框架、工具、脚本）：

* *module:soft* [[API](https://cknowledge.io/c/module/soft/#api)] [[components](https://cKnowledge.io/c/soft)]

它有助于理解用户平台和环境，以便准备可移植的工作流。

例子：

```bash
ck detect soft:compiler.python
ck detect soft --tags=compiler,python
ck detect soft:compiler.llvm
ck detect soft:compiler.llvm --target_os=android23-arm64
```

## 虚拟环境

* *module:env* [[API](https://cknowledge.io/c/module/env/#api)]

每当使用软件检测插件找到一个给定的软件或文件时，CK 会在本地 CK 存储库中使用 `env.sh` (Linux/MacOS) 或 `env.bat` (Windows) 创建一个新的“env”组件。

这个环境文件包含多个具有唯一名称的环境变量，通常从 `CK_` 开始，带有自动检测到的有关给定软件的信息，如版本和源文件路径、二进制文件、包含文件、库等。

这允许您检测和使用不同软件的多个版本，这些版本可以轻松地并行地在您的系统上共存。

例子：

```bash
ck detect soft:compiler.python
ck detect soft --tags=compiler,python
ck detect soft:compiler.llvm

ck show env 
ck show env --tags=compiler
ck show env --tags=compiler,llvm
ck show env --tags=compiler,llvm --target_os=android23-arm64

ck virtual env --tags=compiler,python
```

## Meta 包

当一个给定的软件在我们的系统上没有被检测到时，我们通常希望安装不同版本的相关包。

这就是为什么我们开发了以下 CK 模块，可以自动安装缺少的包（模型、数据集、工具、框架、编译器等）：

* *module:package* [[API](https://cknowledge.io/c/module/package/#api)] [[components](https://cKnowledge.io/c/package)]

这是一个元包管理器，它提供了一个统一的 API，可以使用现有的构建工具和包管理器为给定的目标(包括移动和边缘设备)自动下载、构建和安装包。

以上所有模块现在都可以支持可移植的工作流，这些工作流可以自动适应基于[软依赖](https://cknowledge.io/solution/demo-obj-detection-coco-tf-cpu-benchmark-linux-portable-workflows/#dependencies)的给定环境。

例子：

```bash
ck install package --tags=lib,tflite,v2.1.1
ck install package --tags=tensorflowmodel,tflite,edgetpu
```

请参阅定制给定包的变体示例：[lib-tflite](https://github.com/ctuning/ck-tensorflow/tree/master/package/lib-tflite)。

## 脚本

我们还为 ad-hoc 脚本提供了一个抽象：

* *module:script* [[API](https://cknowledge.io/c/module/script/#api)] [[components](https://cKnowledge.io/c/script)]

请参阅一个 CK 组件的例子，它带有一个用于提交 MLPerf™ 基准测试的脚本：[GitHub](https://github.com/ctuning/ck-mlperf/tree/master/script/mlperf-inference-v0.7.image-classification)。

## 可移植程序管道（工作流）

接下来，我们实现了一个 CK 模块，提供了一个通用 API 来编译、运行和验证程序，同时自动适应任何平台和环境：

* *module:program* [[API](https://cknowledge.io/c/module/program/#api)] [[components](https://cKnowledge.io/c/program)]

用户描述 CK 程序元数据中 CK 包的依赖关系，以及构建、预处理、运行、后处理和验证给定程序的命令。

例子：

```bash
ck pull repo:mlcommons@ck-mlops

ck compile program:image-corner-detection --speed
ck run program:image-corner-detection --repeat=1 --env.OMP_NUM_THREADS=4

```

## 可再生的实验

我们开发了一个抽象来 record 和 reply 实验，使用下面的 CK 模块：

* *module:experiment* [[API](https://cknowledge.io/c/module/experiment/#api)] [[components](https://cKnowledge.io/c/experiment)]

当运行 CK 程序时，该模块记录所有已解析的依赖关系、输入和输出，从而允许保存所有来源的实验，并稍后在相同或不同的机器上重播它们：

```bash
ck benchmark program:image-corner-detection --record --record_uoa=my_experiment

ck find experiment:my_experiment

ck replay experiment:my_experiment

ck zip experiment:my_experiment
```

## 仪表盘

既然我们可以统一地记录所有的实验，我们也可以统一地将它们可视化。这就是为什么我们开发了一个简单的 web 服务器，可以帮助创建可定制的仪表盘：

* *module:web* [[API](https://cknowledge.io/c/module/web/#api)]

查看此类仪表板的示例：

* [view online at cKnowledge.io platform](https://cKnowledge.io/reproduced-results)
* [view locally (with or without Docker)](https://github.com/ctuning/ck-mlperf/tree/master/docker/image-classification-tflite.dashboard.ubuntu-18.04)

## 互动的文章

我们 CK 的目标之一是自动化（重新）生成可复制的文章。我们已经在树莓派基金会的这个概念验证项目中[验证了这种可能性](https://cKnowledge.org/rpi-crowd-tuning)。

我们计划开发一个 GUI，以使生成这样的论文的过程更友好的用户！

## Jupyter 笔记本

从 Jupyter 和 Colab 笔记本中使用 CK 是可能的。我们提供了一个抽象来在 CK 库中共享 Jupyter 笔记本：

* *module:jnotebook* [[API](https://cknowledge.io/c/module/jnotebook/#api)] [[components](https://cKnowledge.io/c/jnotebook)]

您可以在这里看到一个使用 CK 命令处理 MLPerf™ 基准测试结果的 [Jupyter 笔记本示例](https://nbviewer.jupyter.org/urls/dl.dropbox.com/s/5yqb6fy1nbywi7x/medium-object-detection.20190923.ipynb)。

## Docker

我们提供了一个抽象来构建、pull 和运行 Docker 映像：

* *module:docker* [[API](https://cknowledge.io/c/module/docker/#api)] [[components](https://cKnowledge.io/c/docker)]

你可以在这里看到 [Docker 映像的例子](https://github.com/ctuning/ck-mlperf/tree/master/docker)，使用统一的 CK 命令来自动化 MLPerf™ 基准测试。

# 进一步信息

在过去的几年里，我们将所有的工作流程和组件从我们过去的 ML&systems 研发，包括迈立普和 [cTuning.org 项目](https://github.com/ctuning/reproduce-milepost-project) 转换为 CK 格式。

目前 ML&systems 研发中有 [150+ 具有操作自动化](https://cKnowledge.io/modules) 的 CK 模块，可以自动抽取许多繁琐重复的任务，包括模型训练与预测、通用自动调优、ML/SW/HW 协同设计、模型测试与部署、论文生成等：

* [可移植 CK 工作流的高级概述](https://cknowledge.org/high-level-overview.pdf)
* [多目标自动调优和机器学习技术协同研究的集体知识工作流（与树莓派基金会合作）]( https://cKnowledge.org/report/rpi3-crowd-tuning-2017-interactive )
* [与学术和工业合作伙伴的主要基于 CK 的项目总结]( https://cKnowledge.org/partners.html )
* [cKnowledge.io 平台文档]( https://cKnowledge.io/docs )

如果您有任何反馈或想了解更多关于我们的计划，请不要犹豫[联系我们](https://cKnowledge.org/contacts.html)！
