# 安装

## 准备

CK 框架需要最小的依赖：Python 2.7+ 或 3.x, pip 和 Git。

````{tab} Linux
```bash
sudo apt-get install python3 python3-pip git wget
```
````

````{tab} MacOS
```bash
brew install python3 python3-pip git wget
```
````

````{tab} Windows
1. 从 git-for-windows.github.io 下载并安装 Git。

2. 从 www.python.org/downloads/windows 下载并安装任何 Python。
````

````{tab} Android (Linux host)
Android 需要交叉编译这些依赖项（在 Ubuntu 18.04 中测试，包括 Docker 和 Linux 的 Windows 10 子系统）。
```bash
sudo apt update
sudo apt install git wget libz-dev curl cmake
sudo apt install gcc g++ autoconf autogen libtool
sudo apt install android-sdk
sudo apt install google-android-ndk-installer
```
````

## CK 安装

```bash
pip install ck
```

注意，在 Windows 上，你还需要安装 `ctuning@ck-win` 存储库中的 CK 组件和二进制文件：

```bash
ck pull repo:ck-win
```

## Docker

在 [cTuning Docker 中心](https://hub.docker.com/u/ctuning) 用 CK 框架和 AI/ML CK工作流准备了几个 Docker 镜像。选择最相关的镜像并按如下方式运行：

```bash
docker run -p 3344:3344 -it {Docker image name from the above list} /bin/bash
```

## 带有模板的虚拟 CK 环境

建议您使用 [虚拟 CK 环境](https://github.com/mlcommons/ck-venv) 利用社区共享的各种自动化模板，如 MLPerf™ 推理。

详情请参阅https://github.com/octoml/venv。

## 自定义

请查看这个 [wiki](https://github.com/mlcommons/ck/wiki/Customization) 来了解更多关于如何定制你的 CK 安装。