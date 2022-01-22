# CK CLI 和 API

大多数 CK 功能都是使用带有 [自动化操作](https://cknowledge.io/actions) 和相关 [CK 条目（组件）](https://cknowledge.io/browse) 的 [CK 模块](https://cknowledge.io/modules) 实现的。

这里我们描述了 CK 用于管理存储库、模块和操作的主要功能。记住，你可以在命令行中看到给定自动化操作的所有旗标，如下所示：

```bash
ck {action} {CK module} --help
```

您可以为自动化操作设置任意键的值，如下所示：

```bash
ck {action} ... key=value
ck {action} ... -key=value
ck {action} ... --key=value
ck {action} ... --key=value
```

如果忽略该值，CK 将使用 “yes” 字符串。

你也可以使用 JSON 或 YAML 文件作为给定操作的输入：

```bash
ck {action} ... @input.json
ck {action} ... @input.yaml
```

## 通过 CLI 管理 CK 存储库

- 自动化操作是使用内部的 CK 模块 [repo](https://cknowledge.io/c/module/repo) 实现的。
- 在 [cKnowledge.io 平台](https://cknowledge.io/c/module/repo/#api) 查看所有自动化操作及其 API 的列表。

### 在当前路径初始化新的 CK 存储库

```bash
ck init repo
```

CK 会询问用户的 repo 名称，也会尝试从 `.git/config` 检测 Git URL。

额外的选项：

```bash
ck init repo:{CK repo name}
ck init repo --url={Git URL with the CK repository}
ck init repo --url={Git URL with the CK repository} --deps={list of CK repos}
```

例子：

```bash
ck init repo:asplos21-artifact123 --url=https://github.com/ctuning/ck-asplos21-artifact123 --deps=ck-autotuning
ck init repo:mlperf-submission --url=https://github.com/ctuning/ck-mlperf-submission321 --deps=ck-mlperf
```

### 使用 Git URL 获取现有的存储库

```bash
ck pull repo --url={Git URL with the CK repository}
```

### 从 cTuning GitHub 拉现有的存储库

```bash
ck pull repo:{CK repo name}
```

在本例中，CK 将使用 `https://github.com/ctuning/{CK repo name}`

### 以 zip 文件的形式下载存储库

```bash
ck add repo --zip={URL to the zip file with the CK repository}
```

### 从 Git 更新所有本地 CK 库

```bash
ck pull all
```

### 在本地创建一个虚拟 CK 存储库

快速模式与最小的问题：

```bash
ck add repo:{user-friendly name} --quiet
```

高级模式与许多问题配置存储库：

```bash
ck add repo:{user-friendly name}
```

### 从当前目录导入现有的本地存储库

```bash
ck import repo --quiet
```

### 从本地目录导入现有的本地存储库

```bash
ck import repo --path={full path to the local CK repository} --quiet 
```

### 列出本地 CK 库

```bash
ck ls repo
```

或者

```bash
ck list repo
```

### 删除给定的 CK 存储库

取消注册 CK 仓库，但不要删除内容（你可以稍后再次导入它，以重用自动化操作和组件）：

```bash
ck rm repo:{CK repo name from above list}
```

或者

```bash
ck remove repo:{CK repo name from above list}
```

或者

```bash
ck delete repo:{CK repo name from above list}
```

完全删除 CK 存储库的内容：

```bash
ck rm repo:{CK repo name from above list} --all
```

### 找到到给定 CK 存储库的路径

```bash
ck where repo:{CK repo name}
```

或者

```bash
ck find repo:{CK repo name}
```

### 将给定的 CK 存储库打包为 zip 文件

```bash
ck zip repo:{CK repo name}
```

### 将 zip 文件中的 CK 条目添加到现有的 CK 存储库

到本地存储库：

```bash
ck unzip repo:{CK repo name} --zip={path to a zip file with the CK repo}
```

到给定的存储库：

```bash
ck unzip repo:{CK repo name} --zip={path to a zip file with the CK repo}
```

## 通过 CLI 管理 CK 条目

CK 库基本上是一个 CK 模块和条目的数据库。管理 CK 条目的 CK 内部命令如下：

```bash
ck help
```

CID 是以下格式的 Collective 标识符：

- {CK module name or UID}:{CK entry name or UID}
- {CK repo name or UID}:{CK module name or UID}:{CK entry name or UID}

请注意，在适当的时候，CID 允许通配符！

下面是管理 CK 模块和表项最常用的命令。

### 列出所有本地 CK 库中的 CK 模块

```bash
ck ls module
```

或者

```bash
ck list module
```

显示完整的 CID（repository:module:entry）：

```bash
ck ls module --all
```

### 从所有本地 CK 存储库列出一些带有通配符的 CK 模块

```bash
ck ls module:{wildcard}
```

例子：

```bash
ck ls module:re* --all
```

```
 default:module:repo
 ck-analytics:module:report
 ...
```

### 在给定的存储库中列出给定 CK 模块的 CK 条目

```bash
ck ls {CK repo}:{CK module}:
```

或者

````bash
ck ls {CK repo}:{CK module}:*
````

通配符：

```bash
ck ls {CK repo}:{CK module}:{wildcard for CK entries}
```

例子：

```bash
ck ls ctuning-datasets-min:dataset:*jpeg*dnn*
```

```
  ctuning-datasets-min:dataset:image-jpeg-dnn-cat
  ctuning-datasets-min:dataset:image-jpeg-dnn-cat-gray
  ctuning-datasets-min:dataset:image-jpeg-dnn-computer-mouse
  ctuning-datasets-min:dataset:image-jpeg-dnn-cropped-panda
  ctuning-datasets-min:dataset:image-jpeg-dnn-fish-bike
  ctuning-datasets-min:dataset:image-jpeg-dnn-snake-224
  ctuning-datasets-min:dataset:image-jpeg-dnn-surfers
```

### 通过标签搜索 CK 条目

```bash
ck search {CK module} --tags={list of tags separated by comma}
```

或

```bash
ck search {CK module}:{wildcard for CK entries} --tags={list of tags separated by comma}
```

或者

```bash
ck search {CK repo}:{CK module}:{wildcard for CK entries} --tags={list of tags separated by comma}
```

例子：

```bash
ck search dataset --tags=jpeg
ck search dataset:*dnn* --tags=jpeg
```

### 通过字符串搜索 CK 条目

您可以通过 JSON 元描述中键值的给定字符串的出现来搜索 CK 条目

```bash
ck search {CK module} --search_string={string with wildcards}
```

请注意，CK 支持 [ElasticSearch](https://www.elastic.co/) 对所有 CK JSON 元描述的透明索引，以支持快速搜索和强大的查询。这个模式在我们的 [cKnowledge.io 平台](https://cknowledge.io/) 中使用。请参阅以下网页，了解如何用 ES 配置你的 CK 安装：

- https://github.com/mlcommons/ck/wiki/Customization
- https://github.com/mlcommons/ck/wiki/Indexing-entries
- https://github.com/mlcommons/ck/wiki/Searching-entries

### 查找到给定 CK 条目的路径

```bash
ck find {CK module}:{CK entry}
```

或者

```bash
ck find {CK repo}:{CK module}:{CK entry}
```

例子：

```bash
ck find module:repo
ck find dataset:image-jpeg-dnn-snake-224
ck find ctuning-datasets-min:dataset:image-jpeg-dnn-snake-224
```

### 显示一个给定条目的 JSON 元描述

```bash
ck load {CK module}:{CK entry} --min
```

或

```bash
ck load {CK repo}:{CK module}:{CK entry} --min
```




### Delete a given CK entry

```bash
ck rm {CK module}:{CK entry (can be with wildcard)}
```
或
```bash
ck rm {CK repo}:{CK module}:{CK entry can be with wildcard}
```
或

```bash
ck remove {CK repo}:{CK module}:{CK entry can be with wildcard}
```
或
```bash
ck delete {CK repo}:{CK module}:{CK entry can be with wildcard}
```

例子：
```bash
ck rm ctuning-datasets-min:dataset:image-jpeg-dnn-snake-224
ck rm dataset:*dnn*
```


### Create an empty CK entry

Create a CK entry in a *local* repository (CK scratch-pad):

```bash
ck add {CK module}:{CK entry name}
```

Create CK entry in a given repository:
```bash
ck add {CK repo}:{CK module}:{CK entry name}

If CK entry name is omitted, CK will create an entry with a UID:
```bash
ck add {CK module}
```

例子：
```
ck add tmp

  Entry  (2eab7af343d399d1, /home/fursin/CK-REPOS/local/tmp/2eab7af343d399d1) added successfully!

ck add tmp:xyz

  Entry xyz (44812ba5445a0a52, /home/fursin/CK-REPOS/local/tmp/xyz) added successfully!
```

Note that CK always generate Unique IDs for all entries!




### Rename a given CK entry

```bash
ck ren {CK module}:{CK entry} :{new CK entry name}
```
or
```bash
ck ren {CK repo}:{CK module}:{CK entry} :{new CK entry name}
```
or
```bash
ck rename {CK repo}:{CK module}:{CK entry} :{new CK entry name}
```

Note that CK keeps the same global UID for a renamed entry to be able to always find it!

例子：
```bash
ck ren ctuning-datasets-min:dataset:image-jpeg-dnn-snake-224 :image-jpeg-dnn-snake
```




### Move a given CK entry to another CK repository


```bash
ck mv {CK repo}:{CK module}:{CK entry name} {CK new repo}::
```
or
```bash
ck move {CK repo}:{CK module}:{CK entry name} {CK new repo}::
```

例子：
```
ck mv ctuning-datasets-min:dataset:image-jpeg-dnn-computer-mouse local::
```




### Copy a given CK entry


With a new name within the same repository:
```bash
ck cp {CK repo}:{CK module}:{CK entry name} ::{CK new entry name}
```

With a new name in a new repository:


```bash
ck cp {CK repo}:{CK module}:{CK entry name} {CK new repo}::{CK new entry name}
```

例子：
```
ck cp ctuning-datasets-min:dataset:image-jpeg-dnn-computer-mouse local::new-image
```






## CLI to manage CK actions

All the functionality in CK is implemented as automation actions in CK modules.

All CK modules inherit default automation actions from the previous section to manage associated CK entries.

A new action can be added to a given CK module as follows:

```bash
ck add_action {module name} --func={action name}
```

CK will ask you a few questions and will create a dummy function in the given CK module.
You can immediately test it as follows:
```bash
ck {action name} {module name}
```

It will just print the input as JSON to let you play with the command line 
and help you understand how CK converts the command line parameters
into the dictionary input for this function.

Next, you can find this module and start modifying this function:
```bash
ck find module:{module name}
```

For example, you can add the following Python code inside
this function to load some meta description of the entry ''my data''
when you call the following action:

```bash
ck {action name} {module name}:{my data}
```

```Python
    def {action name}(i):

    action=i['action']     # CK will substitute 'action' with {action name}
    module=i['module_uoa'] # CK will substitute 'module_uoa' with {module name}
    data=i['data_uoa']     # CK will substitute 'data_uoa' with {my data}

    # Call CK API to load meta description of a given entry
    # Equivalent to the command line: "ck load {module name}:{data name}"
    r=ck.access({'action':'load',
                 'module_uoa':work['self_module_uid'], # Load the UID of a given module
                 'data_uoa':data})
    if r['return']>0: return r # Universal error handler in the CK

    meta=r['dict']      # meta of a given entry
    info=r['info']      # provenance info of this entry
    path=r['path']      # local path to this entry
    uoa=r['data_uoa']   # Name of the CK entry if exists. Otherwise UID.
    uid=r['data_uid']   # Only UID of the entry
```

Note that *uoa* means that this variable accepts Unique ID or Alias (user-friendly name).

Here *ck* is a CK kernel with various productivity functions
and one unified *access* function to all CK modules and actions
with unified dictionary (JSON) I/O.

You can find JSON API for all internal CK actions from the previous section that manage CK entries
from the command line as follows:
```bash
 ck get_api --func={internal action}
```

For example, you can check the API of the "load" action as follows:
```bash
 ck get_api --func=load
```

For non-internal actions, you can check their API as follows:
```bash
 ck {action name} {module name} --help
```

You can also check them at the [cKnowledge.io platform](https://cKnowledge.io/modules).

When executing the following command

```bash
 ck my_action my_module --param1=value1 --param2 -param3=value3 param4 @input.json ...
```
CK will convert the above command line parameters to the following Python dictionary ''i'' 
for a given action:

```Python
 i={
     "action":"my_action",
     "module_uoa":"my_module",
     "param1":"value1",
     "param2":"yes",
     "param3":"value3"

     #extra keys merged from the input.json

     ...
 }
```

Note that when adding a new action to a given module, CK will also create a description
of this action inside the *meta.json* of this module. You can see an example
of such descriptions for the internal CK module "repo" [here](https://github.com/mlcommons/ck/blob/master/ck/repo/module/repo/.cm/meta.json).
When CK calls an action, it is not invoked directly from the given Python module 
but CK first checks the description, tests inputs, and then passes the control to the given Python module.

Also note that we suggest not to use aliases (user-friendly names) inside CK modules
but CK UIDs. The reason is that CK names may change while CK UIDs stay persistent.
We specify dependencies on other CK modules in the *meta.json* of a given module
using the *module_deps* key. See an example in the CK module *program*:
* [program meta.json](https://github.com/ctuning/ck-autotuning/blob/master/module/program/.cm/meta.json#L118)
* [how it is used in the CK module program](https://github.com/ctuning/ck-autotuning/blob/master/module/program/module.py#L479)

Such approach also allows us to visualize the growing knowledge graph:
[interactive graph]( https://cKnowledge.io/kg1 ), 
[video](https://youtu.be/nabXHyot5is).

Finally, a given CK module has an access to the 3 dictionaries:
* *cfg* - this dictionary is loaded from the *meta.json* file from the CK module
* *work* - this dictionary has some run-time information:

  * self_module_uid: UID of the module
  * self_module_uoa: Alias (user-friendly name of the module) or UID
  * self_module_alias: Alias (user-friendly name of the module) or empty
  * path: path to the CK module
* *ck.cfg* - CK global [cfg dictionary](https://github.com/mlcommons/ck/blob/master/ck/kernel.py#L48) 
  that is updated at run-time with the meta description of the "kernel:default" entry.
  This dictionary is used to customize the local CK installation.



## CK Python API

One of the goals of the CK framework was to make it very simple for any user to access any automation action.
That is why we have developed just one [unified Python "access" function](https://ck.readthedocs.io/en/latest/src/ck.html#ck.kernel.access) 
that allows one to access all automation actions with a simple I/O (dictionary as input and dictionary as output).

You can call this function from any Python script or from CK modules as follows:

```Python
import ck.kernel as ck

i={'action': # specify action
   'module_uoa': # specify CK module UID or alias

   check keys from a given automation action for a given CK module (ck action module --help)
  }

r=ck.access(i)

if r['return']>0: return r # if used inside CK modules to propagate to all CK callers
#if r['return']>0: ck.err(r) # if used inside Python scripts to print an error and exit

#r dictionary will contain keys from the given automation action.
# See API of this automation action (ck action module --help)
```

Such approach allows users to continue extending different automation actions by adding new keys
while keeping backward compatibility. That's how we managed to develop 50+ modules with the community
without breaking portable CK workflows for our ML&systems R&D.

At the same time, we have implemented a number of "productivity" functions
in the CK kernel that are commonly used by many researchers and engineers.
For example, you can load JSON files, list files in directories, copy strings to clipboards.
At the same time, we made sure that these functions work in the same way across
different Python versions (2.7+ and 3+) and different operating systems 
thus removing this burden from developers.

You can see the list of such productivity functions [here](https://ck.readthedocs.io/en/latest/src/ck.html).
For example, you can [load a json file](https://ck.readthedocs.io/en/latest/src/ck.html#ck.kernel.load_json_file) 
in your script or CK module in a unified way as follows:

```Python
import ck.kernel as ck

r=ck.load_json_file({'json_file':'some_file.json'})
if r['return']>0: ck.err(r)

d=r['dict']

d['modify_some_key']='new value'

r=ck.save_json_to_file({'json_file':'new_file.json', 'dict':d, 'sort_keys':'yes'})
if r['return']>0: ck.err(r)

```

## 更多资源

* [CK wiki](https://github.com/mlcommons/ck/wiki)
