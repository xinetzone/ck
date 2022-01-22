# CK specs

## CK 仓库

这里描述一个 CK 存储库的结构。为了更好地理解这个结构，您可能需要查看 CK 库，比如 [ck-env](https://github.com/ctuning/ck-env)。

注意，当你使用 [CK CLI 或 Python API](commands.md) 时，CK 会自动创建这个结构。

### 根文件

`.ckr.json`
: 该存储库的 JSON 元描述，包括 UID 和对其他存储库的依赖关系。这些信息中的大部分是在创建新的 CK 存储库时自动生成的。

```Json
{
  "data_alias": # repository name (alias) such as "ck-env"
  "data_name": # user-friendly repository name such as "CK environment"
  "data_uid": # CK UID for this repository (automatically generated)
  "data_uoa": # repository alias or repository UID if alias is empty
  "dict": {
    "desc": # user-friendly description of this repository
    "repo_deps": [
      {
        "repo_uoa": # repository name
        "url": # Git URL of this repo
      }
      ...
    ],
    "shared": # =="git" if repository is shared
    "url": # Git URL of this repository
  }
}
```

### 根目录（CK 模块）

CK 存储库的根可以包含任何子目录，以便用户逐步将他们的临时项目转换为 CK 格式。但是，如果一个目录与 CK 条目相关，它应该与一个相关的 CK 模块和 `.cm` 目录中的两个文件同名：

* *CK module name*
* *.cm/alias-a-{CK module name}*：包含 CK 模块的 UID
* *.cm/alias-u-{UID}*：包含 CK 模块名（别名）

`.cm` 中的这两个文件帮助 CK 理解，在 CK 存储库中的给定目录与某些 CK 条目相关联！它们还支持通过 UID 或别名对给定的 CK 条目进行快速处理。

然而，在将来，我们可能希望删除这些文件，并在 CK 提取存储库时执行自动索引(类似于Git)。看到[这张票](https://github.com/mlcommons/ck/issues/118)。

### CK 条目的子目录

如果 CK 存储库中的目录是一个有效的 CK 模块名，那么它可以包含与该 CK 模块相关联的 CK 条目。

如果 CK 条目没有名称（别名），它将被存储为 CK UID（16 个十六进制小写字符）：

* *UID*：一些工件的支架

如果 CK 条目有名称（别名），则 `.cm` 中会有文件：

* *CK entry name*：一些工件的支架
* *.cm/alias-a-{CK entry name}* : 包含 CK 条目的 UID
* *.cm/alias-u-{UID}* : 包含 CK 条目的名字（别名）

同样，这些 `.cm` 文件允许 CK 通过 UID 和别名在所有 CK 存储库中快速查找 CK 条目，而不需要进行任何索引。

### CK entry

每个有效的 CK 条目在 `.cm` 中至少有 3 个文件：

* *.cm/meta.json* : 一个给定 CK 条目的 JSON 元描述
* *.cm/info.json* : 给定 CK 条目的来源（创建日期、作者、版权、许可、CK 使用等）
* *.cm/desc.json* : 元描述规格(开发中)

这个条目还可以包含任何其他文件和目录（例如模型、数据集文件、算法、脚本、论文和任何其他工件）。
