# 尝试 CK

在本节将演示如何使用便携式 CK 和非虚拟化项目的工作流可以自动适应任何平台和用户环境，即自动检测目标平台特性和软件依赖关系，然后编译并运行一个给定的程序与任何兼容的数据集和模型在一个统一的方法。

```{hint}
这种方法也支持在 [ML&systems 会议上的可再生计划](https://ctuning.org/ae)，以共享可移植的工作流和[已发表的论文](https://cknowledge.io/reproduced-papers)。CK 的目标是让社区更容易复制研究技术，比较它们，在它们的基础上构建它们，并在生产中采用它们。
```

## Pull CK 库与通用程序工作流

现在，可以通过自动化方法获取 CK repo，实现协作性、可复制性和跨平台基准测试

```bash
ck pull repo:mlcommons@ck-mlops
ck pull repo:ctuning-datasets-min
```

CK 将使用不同的自动化操作、基准测试和 CK 格式的数据集自动拉取所有所需的 CK 存储库。你可以看到它们如下：

```bash
ck ls repo
```

默认情况下，CK 将所有 CK 存储库存储在用户空间 `$HOME/CK-REPOS` 中。但是，您可以使用环境变量 `CK_REPOS` 来更改它。

## 管理 CK 条目

现在可以看到所有的共享程序工作流的 CK 格式：

```bash
ck ls program
```

可以找到并研究一个给定程序（如图像角点检测（image-corner-detection））的CK格式，如下所示：

```bash
ck search program --tags=demo,image,corner-detection
```

可以在命令行中看到这个程序的 CK 元描述，如下所示：

```bash
ck load program:image-corner-detection
ck load program:image-corner-detection --min
```

在 [GitHub](https://github.com/mlcommons/ck-mlops/tree/master/program/image-corner-detection) 上查看这个条目的所有来源和元描述可能会更方便。

还可以在这里看到 [这个](https://github.com/mlcommons/ck-mlops/blob/master/program/image-corner-detection/.cm/meta.json) CK 程序条目的 CK JSON 元描述。当在 CK 模块程序中调用自动化操作时，自动化代码将读取这个元描述，并相应地为不同的程序执行操作。

## 调用 CK 自动化操作

现在可以试着在你的平台上编译这个程序：

```bash
ck compile program:image-corner-detection --speed
```

CK 会调用模块“program”中的“compile”函数（你可以在 [GitHub](https://github.com/mlcommons/ck/blob/master/ck/repo/module/program/module.py#L3594) 上看到它，或者你可以在本地使用“ck find module:program”找到这个 CK 模块的源代码），读取图像角点检测的 JSON 元数据，并执行一个给定的操作。

````{hint}
可以通过以下方式获取给定动作的所有旗标：

```bash
ck compile program --help
```
````

可以通过在命令行中添加“-”来更新上面的任何键。如果忽略该值，CK 将默认使用“yes”。

当编译程序时，CK 将首先尝试自动检测平台的属性和所有必需的软件依赖项，比如已经安装在这个平台上的编译器和库。CK 使用 [多个插件](https://cknowledge.io/soft) 来描述如何检测不同的软件、模型和数据集。

用户可以在自己的 CK 库或已有的 CK 库中添加自己的插件。

您还可以从命令行手动执行软件检测。例如，你可以检测所有安装的 GCC 或 LLVM 版本：

```bash
ck detect soft:compiler.gcc
ck detect soft:compiler.llvm
```

检测到的软件与自动生成的环境脚本（`env.sh` 或 `env.bat`）一起注册到本地 CK 存储库中，为该软件指定不同的环境变量（路径、版本等）。

注册软件列表如下：

```bash
ck show env
ck show env --tags=compiler
```

你可以使用 CK 作为一个类似于 venv 和 Conda 的虚拟环境：

```bash
ck virtual env --tags=compiler,gcc
```

这种方法允许将 CK 工作流从硬连接的依赖项中分离出来，并自动插入所需的工作流。

你现在可以像下面这样运行这个程序：

```bash
ck run program:image-corner-detection
```

在运行程序时，CK 会收集并统一各种特征（执行时间、代码大小等）。这使得在不同的程序、数据集、模型和平台之间可以重用统一的基准测试。此外，我们可以继续改进这个通用的程序工作流程，以监控 CPU/GPU 频率，对收集到的特征进行统计分析，验证输出等：

```bash
ck benchmark program:image-corner-detection --repetitions=4 --record --record_uoa=ck_entry_to_record_my_experiment
ck replay experiment:ck_entry_to_record_my_experiment
```

````{hint}
CK 程序可以自动插入 CK 条目的不同数据集，这些数据集可以在不同的 repos 中被不同的用户共享（例如，当发布一篇新论文时）：

```bash
ck search dataset
ck search dataset --tags=jpeg
```
````

我们的目标是帮助研究人员重用这个通用的 CK 程序工作流，而不是在每个研究项目中从头重写复杂的基础设施。

## 安装丢失的包

请注意，如果给定的软件依赖关系没有被解析，CK 将尝试使用 CK 元包（参见 [cKnowledge.io](https://cknowledge.io/packages) 上的共享 CK 包列表）自动安装它。这些元包包含 JSON 元信息和脚本，用于在可能的情况下重用现有的构建工具和本地包管理器（make、cmake、[scons](https://scons.org/)、[spack](https://spack.io/)、[python-poetry](https://python-poetry.org/) 等），为给定的目标平台安装和重建给定的包。此外，CK 包管理器还可以安装非软件包，包括 ML 模型和数据集，同时确保可移植工作流的所有组件之间的兼容性！

可以列出系统上可用的 CK 包（CK 会在你系统上安装的所有 CK 库中搜索它们）：

```bash
ck search package --all
```

然后，可以尝试在你的系统上安装一个给定的 LLVM，如下所示：

```bash
ck install package --tags=llvm,v10.0.0
```

如果这个包被成功安装，CK 也会创建一个相关的 CK 环境：

```bash
ck show env --tags=llvm,v10.0.0
```

默认情况下，所有包都安装在用户空间（`$HOME/CK-TOOLS`）中。您可以使用 CK 环境变量 `CK_TOOLS` 更改此路径。你也可以要求 CK 直接在 CK 虚拟环境条目中安装包，如下所示：

```bash
ck set kernel var.install_to_env=yes
```

请注意，现在可以在系统上检测或安装同一工具的多个版本，这些版本可以被可移植的 CK 工作流获取和使用！

你可以运行一个 CK 虚拟环境来使用给定的版本，如下所示：

```bash
ck virtual env --tags=llvm,v10.0.0
```

你还可以同时运行多个虚拟环境，将不同版本的工具组合在一起：

```bash
ck show env
ck virtual env {UID1 from above list} {UID2 from above list} ...
```

CK 的另一个重要目标是调用所有自动化操作和便携式工作流在所有的操作系统和环境包括 Linnux，Windows，MacOS，Android（你可以通过添加 `-target_os =android23-arm64` 旗标来重新定位你的工作流程，当安装包或编译和运行你的程序）。这个想法是为所有的研究技术和工件提供一个统一的界面，与研究论文一起共享，使社区更容易上手！

## 参与 crowd-tuning

你甚至可以参与跨不同平台的多个程序和数据集的 [crowd-tuning](https://cknowledge.org/rpi-crowd-tuning)：

```bash
ck crowdtune program:image-corner-detection
ck crowdtune program
```

你可以在 [这里](https://cknowledge.org/repo-beta) 看到优化的实时记分板。

## 使用 CK python API

也可以使用 `ck.access` 函数直接从任何 Python（2.7+ 或 3.3+）运行 CK 自动化操作：

```python
import ck.kernel as ck

# Equivalent of "ck compile program:image-corner-detection --speed"
r=ck.access({'action':'compile', 'module_uoa':'program', 'data_uoa':'image-corner-detection', 
             'speed':'yes'})
if r['return']>0: return r # unified error handling 

print (r)

# Equivalent of "ck run program:image-corner-detection --env.OMP_NUM_THREADS=4
r=ck.access({'action':'run', 'module_uoa':'program', 'data_uoa':'image-corner-detection', 
             'env':{'OMP_NUM_THREADS':4}})
if r['return']>0: return r # unified error handling 

print (r)
```

## 尝试 CK MLPerf™ 工作流

请随意尝试更复杂的 [CK MLPerf 工作流](https://github.com/mlcommons/ck/blob/master/docs/mlperf-automation/README)，以跨不同的模型、数据集、框架和硬件对 ML 系统进行基准测试。

## 进一步的信息

正如您可能注意到的，CK 帮助将临时的研究项目转换为具有通用自动化操作和统一元描述的可重用组件的统一数据库。其目标是促进工件共享和重用，同时逐渐取代和统一所有单调和重复的研究任务！

请查看本 [指南](https://ck.readthedocs.io/en/latest/src/how-to-contribute.html)，了解如何添加自己的存储库、工作流和组件！
