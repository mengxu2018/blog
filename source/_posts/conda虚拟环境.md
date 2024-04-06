---
title: conda虚拟环境
date: 2024-04-03 22:51:52
tags: python
---

通过conda创建一个新的虚拟环境非常直观，以下是一些基本步骤：
前提是从miniconda官网下载安装这个软件

1. **打开终端/命令行**：
   - 对于Windows用户，请打开Anaconda Prompt或Anaconda PowerShell Prompt。
   - 对于macOS/Linux用户，请在终端中操作。

2. **创建虚拟环境**：
   要创建一个名为`myenv`的新虚拟环境（这里只是一个示例名称，你可以替换为你想要的任何名称），请输入以下命令：
   ```
   conda create --name myenv
   ```

   如果你想在这个环境中指定一个特定版本的Python，可以在命令后面加上相应的版本号，例如：
   ```
   conda create --name myenv python=3.9
   ```
   这将创建一个基于Python 3.9版本的虚拟环境。

3. **确认并安装依赖**：
   在上述命令执行过程中，conda可能会询问你是否同意安装相关的依赖包，输入`y`然后回车确认即可。

4. **激活虚拟环境**：
   创建完成后，要激活该虚拟环境，输入：
   ```
   conda activate myenv
   ```
   （在较旧版本的conda中，激活命令可能是 `source activate myenv`，但在2024年的版本中，应该使用不带`source`的命令）。

5. **检查环境状态**：
   激活后，你可以查看当前活动环境来确认是否已切换到新创建的虚拟环境：
   ```
   conda env list
   ```
   或者观察命令提示符前缀，通常它会被更改为当前虚拟环境的名称。

6. **在虚拟环境中安装其他包**：
   在虚拟环境中安装任何额外的包（比如numpy或pandas），只需使用conda或pip命令：
   ```
   conda install numpy
   # 或者
   pip install pandas
   ```

7. **退出虚拟环境**：
   当你完成工作并希望回到系统的基础环境或其他环境时，运行：
   ```
   conda deactivate
   ```

这样，你就成功地使用conda创建并管理了一个用于隔离不同项目的Python虚拟环境。在虚拟环境中，所有安装的软件包都不会影响到全局或其它虚拟环境。