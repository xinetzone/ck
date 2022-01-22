# 最常用的用法(待更)

这里描述如何使用新的（空的）CK 库或现有的 CK 库创建和共享新的 CK 程序工作流、软件检测插件和包。可移植的 CK 程序工作流是最常用的 CK 自动化，用于编译、运行、验证和比较不同编译器、库、模型、数据集和平台上的不同算法和基准。

我们强烈建议您先查看 [CK 介绍](https://ck.readthedocs.io/en/latest/src/introduction.html) 和[入门指南](https://ck.readthedocs.io/en/latest/src/first-steps.html)。

你也可以检查以下真实世界的用例，它们是基于可移植和可定制的 CK 工作流程：

* [MLPerf&trade; benchmark automation](https://github.com/ctuning/ck-mlperf)
* [Reproducible ACM REQUEST tournaments](https://cKnowledge.org/request) to co-design Pareto-efficient AI/ML/SW/HW stacks
* [Reproducible quantum hackathons](https://cKnowledge.org/quantum)
* Student Cluster Competition automation at SuperComputing: [SCC18]( https://github.com/ctuning/ck-scc18 ), [general SCC]( https://github.com/ctuning/ck-scc )
* [reproducible and interactive paper for ML-based compilers]( https://cKnowledge.org/rpi-crowd-tuning )

## 新建 CK 存储库

在当前目录中初始化一个新的 CK 存储库（可以是现有的 Git repo）

如果您计划对 [已有的 CK 存储库](http://cknowledge.io/repos) 进行贡献，则可以跳过这一小节。否则，您需要手动创建一个新的 CK 存储库。

你需要选择一些用户友好的名称，例如 “my-new-repo”，然后在命令行（Linux、Windows 和 MacOS）中初始化当前目录下的 CK 库，如下所示：

```bash
ck init repo:my-new-repo
```

如果当前目录属于一个 Git repo, CK 会自动检测到该 URL。否则，你也需要在 CLI 中指定它：

```bash
ck init repo:my-new-repo --url={Git URL}
```

然后，你可以通过下面的命令找到 CK 创建的这个虚拟 CK 库的位置：

```bash
ck where repo:my-new-repo
```

````{note}
如果你想与社区或工作组共享你的存储库，以重用自动化和组件，你必须在 [GitHub](http://www.github.com/), [GitLab](http://gitlab.com/), [BitBucket](http://gitlab.com/) 或任何其他基于 git 的服务创建一个空的存储库。假设您在 https://github.com/my_name 创建了 my-new-repo。

然后，你可以使用 CK 提取这个存储库，如下所示：

```bash
ck pull repo --url=https://github.com/my_name/my-new-repo
```

然后 CK 会在本地创建 `my_name_repo` 存储库，添加默认的元信息，并将其标记为共享存储库（半自动同步内容与相关的 Git 存储库）：

```bash
ck where repo:my-new-repo
```
````

例如，你可以稍后提交并将这个仓库的更新推送回 Git，如下所示：

```bash
ck push repo:my-new-repo
```

建议你在从 GitHub 拉出你的虚拟库后立即进行第一次提交，以自动推送 ` .ckr.json` 文件与一些内部元信息和唯一 ID 返回到 GitHub。

请注意，你也可以使用 Git 命令直接从 CK 存储库目录提交和推送所有更新！不要忘记提交隐藏的 CK 目录 `*.cm/**`！

现在可以使用新创建的存储库作为数据库来添加、共享和重用新组件了。您也可以与您的同事或 [工件评估委员会](https://ctuning.org/ae) 共享此存储库，以统一的方式测试您的组件和工作流：

```bash
ck pull repo --url=https://github.com/my_name/my-new-repo
```

### 添加依赖

添加对其他存储库的依赖，以重用自动化操作和组件。

当你想重用现有的 CK 自动化操作和其他存储库的组件时，你需要在你的根 CK 存储库的 `.ckr.json` 文件中添加对所有这些存储库的依赖：

```
...
  "dict": {
    ...
    "repo_deps": [
      {
        "repo_uoa": "ck-env"
      },
      {
        "repo_uoa": "ck-autotuning"
      },
      {
        "repo_uoa": "ck-mlperf",
        "repo_url": "https://github.com/ctuning/ck-mlperf"
      }
    ] 
  }
```

每当有人拉出你的仓库，CK 将自动拉出所有其他需要的 CK 仓库与自动化操作！

### 添加新的程序工作流

现在，您可以添加新的 CK 工作流，以统一的方式编译和运行一些算法或基准。

因为 CK 的概念是关于用类似维基百科的通用 API 重用和扩展现有组件，我们建议你看看共享 CK 程序的[索引](https://cknowledge.io/programs)，以防有人已经为相同或类似的程序共享了 CK 工作流！

如果你发现了一个类似的程序，例如“图像角点检测”，你可以在新的 CK 库中创建一个该程序的工作副本，以便进一步编辑，如下所示：

```bash
ck pull repo:ctuning-programs 
ck cp program:image-corner-detection my-new-repo:program:my-copy-of-image-corner-detection
```

现在你在你的新存储库中有了一个 CK “图像-角点检测”程序条目的工作副本，它包含了关于如何编译和运行这个程序的源代码和 CK 元信息：

```bash
ck compile program:my-copy-of-image-corner-detection --speed
ck run program:my-copy-of-image-corner-detection
```

你可以在命令行中找到并探索新的 CK 条目，如下所示：

```bash
ck find program:my-copy-of-image-corner-detection
```

你会在这个目录中看到以下文件：

- `.cm/desc.json`：所有自动化操作中所有 I/O 类型的描述（默认为空：我们现在可以跳过它）
- `.cm/info.json`：此条目的来源（创建日期、作者、许可证等）
- `.cm/meta.json`：**关于如何编译、运行和验证这个程序的 main CK 元信息**
- `susan.c`：这个程序的源代码

再说一次，不要忘记在提交到 Git 时添加 `.cm` 目录，因为 `.cm` 文件在 Linux 中的 bash 中通常是不可见的！

如果没有找到类似的程序，可以使用共享程序模板创建一个新的程序，如下所示：

```bash
ck add my-new-repo:program:my-new-program
```

然后 CK 会要求你选择最接近的模板：

```bash
0) C program "Hello world" (--template=template-hello-world-c)
1) C program "Hello world" with compile and run scripts (--template=template-hello-world-c-compile-run-via-scripts)
2) C program "Hello world" with jpeg dataset (--template=template-hello-world-c-jpeg-dataset)
3) C program "Hello world" with output validation (--template=template-hello-world-c-output-validation)
4) C program "Hello world" with xOpenME interface and pre/post processing (--template=template-hello-world-c-openme)
5) C++ TensorFlow classification example (--template=image-classification-tf-cpp)
6) C++ program "Hello world" (--template=template-hello-world-cxx)
7) Fortran program "Hello world" (--template=template-hello-world-fortran)
8) Java program "Hello world" (--template=template-hello-world-java)
9) Python MXNet image classification example (--template=mxnet)
10) Python TensorFlow classification example (--template=image-classification-tf-py)
11) Python program "Hello world" (--template=template-hello-world-python)
12) image-classification-tflite (--template=image-classification-tflite)
13) Empty entry
```

如果你选择 “Python TensorFlow classification example”，CK 将在你的新存储库中创建一个可以工作的图像分类程序，该程序的软件依赖于 TensorFlow AI 框架和兼容模型。

因为它是一个 Python 程序，你不需要编译它：

```bash
ck run program:my-new-program
```

请注意，稍后您可以将以下键添加到 `meta.json` 文件中，从而使您自己的程序成为模板文件：

```json
 "template": "yes"
```

### 更新程序源代码

如果您发现一个具有所有必要的软件依赖项的类似程序，您现在可以为您自己的程序更新或更改它的源代码。

在这种情况下，您必须更新 `meta.json` 中的以下键：

- 添加源文件：

    ```
    "source_files": [
        "susan.c"
    ], 
    ```

- 指定运行程序的命令行（参见 `run_cmd_main`）：

    ```
    "run_cmds": {
        "corners": {
        "dataset_tags": [
            "image", 
            "pgm", 
            "dataset"
        ], 
        "ignore_return_code": "no", 
        "run_time": {
            "run_cmd_main": "$#BIN_FILE#$ $#dataset_path#$$#dataset_filename#$ tmp-output.tmp -c" 
        }
        }, 
        "edges": {
        "dataset_tags": [
            "image", 
            "pgm", 
            "dataset"
        ], 
        "ignore_return_code": "no", 
        "run_time": {
            "run_cmd_main": "$#BIN_FILE#$ $#dataset_path#$$#dataset_filename#$ tmp-output.tmp -e" 
        }
        }, 
    ```

请注意，可以有多个可能的命令行来运行这个程序。在这种情况下，当你运行这个程序时，CK 会问你使用哪一个。例如，这对执行 ML 模型训练（“训练”）、验证（“测试”）和分类（“分类”）很有用。

您还可以更新 ` meta.json` 键定制程序编译和执行：

```
"build_compiler_vars": {
    "XOPENME": ""
  }, 

  "compiler_env": "CK_CC", 

  "extra_ld_vars": "$<<CK_EXTRA_LIB_M>>$", 

  "run_vars": {
    "CT_REPEAT_MAIN": "1",
    "NEW_VAR":"123"
  }, 
```

注意，当你在命令行中以统一的方式运行一个给定的程序时，你可以更新环境变量，如下所示：

```bash
ck run program:my-new-program --env.OMP_NUM_THREADS=4 --env.ML_MODEL=mobilenet-v3
```

您还可以通过环境暴露不同的算法参数和优化，以应用可定制的 CK 自动调优器，如在这个 CK 请求工作流中使用的，以自动探索（协同设计）不同的 MobileNets 配置，速度，准确性和成本。

下面是 CK 程序 `meta.json` 中其他重要键的简要描述：

```
"run_cmds": {                

    "corners": {               # User key describing a given execution command line

      "dataset_tags": [        # dataset tags - will be used to query CK
        "image",               # and automatically find related entries such as images
        "pgm", 
        "dataset"
      ], 

      "run_time": {            # Next is the execution command line format
                               # $#BIN_FILE#$ will be automatically substituted with the compiled binary
                               # $#dataset_path#$$#dataset_filename#$ will be substituted with
                               # the first file from the CK dataset entry (see above example
                               # of adding new datasets to CK).
                               # tmp-output.tmp is and output file of a processed image.
                               # Basically, you can shuffle below words to set your own CMD

        "run_cmd_main": "$#BIN_FILE#$ $#dataset_path#$$#dataset_filename#$ tmp-output.tmp -c", 

        "run_cmd_out1": "tmp-output1.tmp",  # If !='', add redirection of the stdout to this file
        "run_cmd_out2": "tmp-output2.tmp",  # If !='', add redirection of the stderr to this file

        "run_output_files": [               # Lists files that are produced during
                                            # benchmark execution. Useful when program
                                            # is executed on remote device (such as
                                            # Android mobile) to pull necessary
                                            # files to host after execution
          "tmp-output.tmp", 
          "tmp-ck-timer.json"
        ],


        "run_correctness_output_files": [   # List files that should be used to check
                                            # that program executed correctly.
                                            # For example, useful to check benchmark correctness
                                            # during automatic compiler/hardware bug detection
          "tmp-output.tmp", 
          "tmp-output2.tmp"
        ], 

        "fine_grain_timer_file": "tmp-ck-timer.json"  # If XOpenME library is used, it dumps run-time state
                                                      # and various run-time parameters (features) to tmp-ck-timer.json.
                                                      # This key lists JSON files to be added to unified 
                                                      # JSON program workflow output
      },

      "hot_functions": [                 # Specify hot functions of this program
        {                                # to analyze only these functions during profiling
          "name": "susan_corners",       # or during standalone kernel extraction
          "percent": "95"                # with run-time memory state (see "codelets"
                                         #  shared in CK repository from the MILEPOST project
                                         #  and our recent papers for more info)
        }
      ] 

      "ignore_return_code": "no"         # Some programs have return code >0 even during
                                         # successful program execution. We use this return code
                                         # to check if benchmark failed particularly during
                                         # auto-tuning or compiler/hardware bug detection
                                         #  (when randomly or semi-randomly altering code,
                                         #   for example, see Grigori Fursin's PhD thesis with a technique
                                         #   to break assembler instructions to detect 
                                         #   memory performance bugs) 
    }, 
    ...
  }, 
```

在 Student Cluster Competition’18 的[这个例子](https://github.com/ctuning/ck-scc18/blob/master/program/seissol-netcdf/.cm/meta.json)中，您还可以检查如何在运行程序之前和之后使用预处理和后处理脚本。

## 更新软件依赖

如果你的新程序依赖于额外的软件依赖（编译器，库，模型，数据集），你必须首先在软件检测插件的在线索引中找到你需要的那些。然后，你可以在 ` meta.json` 中使用 `compile_deps` 或 `run_deps` 键来指定标签和版本：

```
  "compile_deps": {
    "compiler": {
      "local": "yes", 
      "name": "C compiler", 
      "sort": 10, 
      "tags": "compiler,lang-c"
    }, 
    "xopenme": {
      "local": "yes", 
      "name": "xOpenME library", 
      "sort": 20, 
      "tags": "lib,xopenme"
    }
  }, 
```

```
"run_deps": {
    "lib-tensorflow": {
      "local": "yes",
      "name": "TensorFlow library",
      "sort": 10,
      "tags": "lib,tensorflow",
      "no_tags":"vsrc"
    },
    "tensorflow-model": {
      "local": "yes",
      "name": "TensorFlow model (net and weights)",
      "sort": 20,
      "tags": "tensorflowmodel,native"
    }
  },
```

最低，你只需要添加一个新的 sub-key 如 “lib-tensorflow”，一个用户友好的名称如 “TensorFlow library”，一个或多个标记来指定您的软件检测插件从上面指数（CK将使用这些标签找到相关CK组件）和一个顺序依赖关系可以解决使用排序键。

你也可以用以下键选择版本范围：

```
 "version_from": [1,64,0], # inclusive
  "version_to": [1,66,0]    # exclusive
```

看看一个 [更复杂的 meta.json](https://github.com/dividiti/ck-caffe/blob/master/package/lib-caffe-bvlc-master-cuda-universal/.cm/meta.json) 的CUDA 包。

每当 CK 编译或运行程序时，它首先自动解析所有的软件依赖关系。CK 还会用自动生成的 `env.sh` 或 `env.bat` 批处理脚本在 CK 虚拟环境中注册所有检测到的软件或安装的包（请参见[开始指南](http://localhost:8000/ai/ck/docs/_build/html/src/first-steps.html)）。然后，根据上面的排序键一个接一个地加载这些脚本，以聚合所有所需的环境变量，并将它们传递给编译或执行脚本。你的脚本和算法可以使用所有这些环境变量来定制编译和执行，而不需要手动更改路径，也就是说，我们启用了可移植的工作流，可以自动适应用户环境。

## 重用或添加基本数据集

在 CK 工作流中开发了一种简单的机制来重用基本（小）数据集，例如单个图像。

您可以使用此[联机索引](https://cknowledge.io/c/dataset)找到已经共享的数据集。

如果您想在您的程序工作流中重用它们，您可以找到相关的一个，检查它的标签（参见 image-jpeg-0001 数据集的 [meta.json](https://github.com/ctuning/ck-autotuning/blob/master/dataset/image-jpeg-fgg/.cm/meta.json)），并将它们添加到您的程序元数据中，如下所示：

```
 "run_cmds": {
    "corners": {
      "dataset_tags": [
        "image", 
        "pgm", 
        "dataset"
      ], 
      "ignore_return_code": "no", 
      "run_time": {
        "run_cmd_main": "$#BIN_FILE#$ $#dataset_path#$$#dataset_filename#$ tmp-output.tmp -c" 
      }
    }, 
```

然后，CK 使用这些标志在所有获取的 CK 存储库中搜索所有数据集条目，当发现多个条目时，会询问用户使用哪个。然后 CK 会将 `$#dataset_path#$$#dataset_filename#$` 替换为所选条目中数据集的完整路径和文件。

这种方法可以在轻松共享和重用相关数据集的同时，消除特殊脚本中的硬连接路径。每当你拉一个新的存储库与 CK 数据集，他们可以被一个给定的程序工作流自动拾取！

例如，你可以在你的 CK 存储库中看到所有 pgm 图像，如下所示：

```bash
ck pull repo:ctuning-datasets-min
ck search dataset --tags=dataset,image,pgm
```

你可以在你的新存储库中添加一个新的数据集，如下所示：

```bash
ck add my-new-repo:dataset:my-new-dataset
```

您将被要求输入一些标签，并选择一个文件，该文件将被复制到新的 CK 条目。

注意，对于大型和复杂的数据集，如 ImageNet，我们使用 CK 包，它可以下载给定的数据集，甚至可以根据其他软件依赖关系处理它。例如，当使用 TensorFlow 或 PyTorch 或 MXNet 时，可能需要一个不同的过程。

## 添加新的 CK 软件检测插件

如果 CK 软件插件对于给定的代码、数据或模型不存在，你可以在自己的存储库或[已经存在的存储库](https://cknowledge.io/repos)中添加一个新的。

我们建议你使用这个[在线索引](http://cknowledge.io/soft)找到最接近的软件检测插件，拉出这个库，并在你的库中复制如下：

```bash
ck copy soft:lib.armcl my-new-repo:soft:lib.my-new-lib
```

或者

```bash
ck copy soft:dataset.imagenet.train my-new-repo:soft:my-new-data-set
```

或者，你可以添加一个新的软条目，并选择最相关的模板：

```bash
ck add my-new-repo:soft:my-new-data-set
```

然后必须更新 `.cm/meta.json` 中的相关键。你可以发现如下：

```bash
ck find soft:lib.my-new-lib
```

典型软件元描述：

```json
{
  "auto_detect": "yes",
  "customize": {
    "check_that_exists": "yes",
    "ck_version": 10,
    "env_prefix": "CK_ENV_LIB_ARMCL",
    "limit_recursion_dir_search": {
      "linux": 4,
      "win": 4
    },
    "soft_file": {
      "linux": "libarm_compute.a",
      "win": "arm_compute.lib"
    },
    "soft_path_example": {
    }
  },
  "soft_name": "ARM Compute Library",
  "tags": [
    "lib",
    "arm",
    "armcl",
    "arm-compute-library"
  ]
}
```

