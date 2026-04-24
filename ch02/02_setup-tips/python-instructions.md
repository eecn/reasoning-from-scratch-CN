# Python 设置建议

本书中的代码大部分是自包含的，我已经尽量减少了外部依赖。然而，为了让本书保持易读性并控制在 2000 页以内，少量 Python 包是必需的。

本节介绍两种适合初学者的方法来安装所需的包，以便你能运行代码示例。

当然，安装和管理 Python 包还有很多其他方式。如果你是有经验的 Python 用户，已经有自己的设置或偏好，可以跳过本节。

如果以下两种方式都不适合你，请随时联系我们，例如，通过开启一个 [Discussion](https://github.com/rasbt/reasoning-from-scratch/discussions)。

&nbsp;
## 方式一：使用 `pip`（内置，到处可用）

如果你已经在使用较新版本的 Python，可以使用内置的 `pip` 安装器来安装包。

我在写本书时使用的是 Python 3.12。不过，3.11 和 3.10 等较旧版本也完全可以使用。你可以通过以下命令检查你的 Python 版本：

```bash
python --version
```

如果你使用的是 Python 3.9 或更老版本，建议从 [python.org](https://www.python.org/downloads/) 安装最新版本，或使用 [`pyenv`](https://github.com/pyenv/pyenv) 等工具来管理版本。但是，如果你要安装新的 Python 版本，请确保它被 PyTorch 支持，可以在 [PyTorch 官方网站](https://pytorch.org/get-started/locally/) 上查看推荐。PyTorch 通常比最新的 Python 发布版落后几个月，因此新发布的 Python 版本不会被立即支持或推荐。

要根据需要安装新包（例如 PyTorch 和 Jupyter Lab），运行：

```bash
pip install torch jupyterlab
```

或者，你可以通过 [`requirements.txt`](https://github.com/rasbt/reasoning-from-scratch/blob/main/requirements.txt) 文件一次性安装本书使用的所有必需 Python 包：

```bash
pip install -r https://raw.githubusercontent.com/rasbt/reasoning-from-scratch/refs/heads/main/requirements.txt
```


&nbsp;
## 方式二：使用 `uv`（更快且广受推荐）

虽然 `pip` 仍然是安装 Python 包的经典和官方方式，但 [`uv`](https://github.com/astral-sh/uv) 是一个现代且广受推荐的 Python 包管理器，它能自动：

- 创建和管理虚拟环境
- 快速安装包
- 维护锁定文件以实现可复现安装
- 支持类似 `pip` 的命令

&nbsp;
### 安装 `uv` 和 Python 包

要安装 `uv`，可以使用以下命令（也可参阅官方[安装](https://docs.astral.sh/uv/getting-started/installation/)页面以获取最新建议）。

&nbsp;
**macOS / Linux：**

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

&nbsp;
**Windows (PowerShell)：**

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

安装完成后，你可以像之前使用 `pip` 那样安装新的 Python 包，只需将 `pip` 替换为 `uv pip`。例如：

```bash
uv pip install torch jupyterlab
```

不过，如果你使用 `uv`（我自己也推荐并使用），更好的做法是使用原生 `uv` 语法而非 `uv pip`，如下所述。

&nbsp;
### 推荐的 `uv` 工作流

相比使用 `uv pip`，我推荐使用原生 `uv` 工作流。

首先，将 GitHub 仓库克隆到本地：



```bash
git clone https://github.com/rasbt/reasoning-from-scratch.git
```

接下来，进入该文件夹，例如在 Linux 和 MacOS 上：

```bash
cd reasoning-from-scratch
```

然后，由于该文件夹包含 `pyproject.toml` 文件，你就可以开始了：`uv` 会在你第一次运行脚本或打开 Jupyter Lab 时，自动为此 `reasoning-from-scratch` 项目创建一个（默认隐藏的）虚拟环境文件夹 (`.venv`)，并在其中安装所有依赖。

通常你不会用到，但一般来说，你可以通过 `uv add` 安装那些不在 `pyproject.toml` 需求列表中的额外包：


```bash
uv add llms_from_scratch
```

上述命令会将包添加到虚拟环境和 `pyproject.toml` 文件中。

&nbsp;
### 通过 `uv` 运行代码

本节介绍通过 `uv` 运行 Jupyter Lab 和 Python 脚本的命令。

要打开 Jupyter Lab，执行：

```python
uv run jupyter lab
```

Python 脚本可以通过以下命令运行：

```bash
uv run python script.py
```




> **进阶用法：** 本节介绍的是一种对 `pip` 用户来说比较熟悉的简单 `uv` 使用方式。如果你对更高级的用法感兴趣，请参阅[这份文档](https://github.com/rasbt/LLMs-from-scratch/tree/main/setup/01_optional-python-setup-preferences)以获取更详细的虚拟环境管理指南。
> 如果你是 macOS 或 Linux 用户并偏好原生 uv 命令，请参阅[这份教程](https://github.com/rasbt/LLMs-from-scratch/blob/main/setup/01_optional-python-setup-preferences/native-uv.md)。我还推荐查看 [uv 官方文档](https://docs.astral.sh/uv/)以获取更多信息。



&nbsp;
### JupyterLab 使用建议

如果你在 JupyterLab（而非 VSCode）中查看 notebook 代码，请注意 JupyterLab（在默认设置下）最近的版本存在滚动 bug。我的建议是进入 Settings -> Settings Editor，将 "Windowing mode" 更改为 "none"（如下图所示），这似乎可以解决该问题。


![Jupyter 显示异常 1](https://sebastianraschka.com/images/reasoning-from-scratch-images/bonus/setup/jupyter_glitching_1.webp)

<br>

![Jupyter 显示异常 2](https://sebastianraschka.com/images/reasoning-from-scratch-images/bonus/setup/jupyter_glitching_2.webp)

&nbsp;
## 有问题？

如果你有任何问题，请随时通过本 GitHub 仓库的 [Discussions](https://github.com/rasbt/reasoning-from-scratch/discussions) 论坛联系我们。
