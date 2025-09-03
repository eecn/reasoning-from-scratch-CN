# Python Setup Recommendations Python设置推荐

The code in this book is largely self-contained, and I have made an effort to minimize external dependencies. However, to keep the book accessible, readable, and well under 2000 pages, a few Python packages are necessary.

本书中的代码大部分是自包含的，并且我已经尽力减少外部依赖。然而，为了使本书易于访问、可读，并且不超过2000页，需要一些Python包。

This section introduces two beginner-friendly methods for installing the required packages so you can run the code examples. 

本节介绍了两种易于入门的方法来安装所需的包，以便您可以运行代码示例。

There are, of course, many other ways to install and manage Python packages. If you are an experienced Python user and already have your own setup or preferences, feel free to skip this section.

当然，还有许多其他安装和管理Python包的方法。如果您是一位经验丰富的Python用户，并且已经有了自己的设置或偏好，请随时跳过本节。

If neither of the two options below works for you, please do not hesitate to reach out, for example, by opening a [Discussion](https://github.com/rasbt/reasoning-from-scratch/discussions).

如果以下两种选项都不适合您，请不要犹豫，通过打开[讨论](https://github.com/rasbt/reasoning-from-scratch/discussions)等渠道联系我们。

&nbsp;
## Option 1: Using `pip` (built-in, works everywhere) 使用`pip`（内置，适用于所有平台）

If you are using a recent version of Python already, you can install packages using the built-in `pip` installer. 

如果您已经使用的是最新版本的Python，可以使用内置的`pip`安装程序来安装包。

I used Python 3.12 for this book. However, older versions like 3.11 and 3.10 will also work fine. You can check your Python version by running:

我在本书中使用了Python 3.12。然而，像3.11和3.10这样的旧版本也可以很好地工作。您可以通过运行以下命令来检查Python版本：

```bash
python --version
```

If you are using Python 3.9 or older, consider installing the latest from [python.org](https://www.python.org/downloads/) or using a tool like [`pyenv`](https://github.com/pyenv/pyenv) to manage versions. However, if you are installing a new Python version, please make sure that it is supported by PyTorch by checking the recommendation on the [official PyTorch website](https://pytorch.org/get-started/locally/). PyTorch typically lags a few months behind the latest Python release, so newly released Python versions are not supported or recommended immediately.

如果您使用的是Python 3.9或更早版本，请考虑从[python.org](https://www.python.org/downloads/)安装最新版本，或者使用像[`pyenv`](https://github.com/pyenv/pyenv)这样的工具来管理版本。如果您正在安装新的Python版本，请通过查看[PyTorch官方网站](https://pytorch.org/get-started/locally/)上的推荐来确保PyTorch支持它。PyTorch通常比最新的Python发布晚几个月，所以新发布的Python版本不会被立即支持或推荐。

To install new packages, as needed, (for example, PyTorch and Jupyter Lab), run:

要安装新包（例如PyTorch和Jupyter Lab），请运行：

```bash
pip install torch jupyterlab
```

Alternatively, you can install all required Python package used in this book all once via the [`requirements.txt`](https://github.com/rasbt/reasoning-from-scratch/blob/main/requirements.txt) file:

或者，您可以通过[`requirements.txt`](https://github.com/rasbt/reasoning-from-scratch/blob/main/requirements.txt)文件一次性安装本书中使用的所有Python包：

```bash
pip install -r https://raw.githubusercontent.com/rasbt/reasoning-from-scratch/refs/heads/main/requirements.txt
```


&nbsp;
## Option 2: Use `uv` (faster and widely recommended) 使用`uv`（更快且广泛推荐）

While `pip` remains the classic and official way to install Python packages, [`uv`](https://github.com/astral-sh/uv) is a modern and widely recommended Python package manager that automatically:

虽然`pip`仍然是安装Python包的经典和官方方式，但[`uv`](https://github.com/astral-sh/uv)是一种现代且广泛推荐的Python包管理器，它可以自动：

- Creates and manages a virtual environment
- Installs packages quickly
- Keeps a lockfile for reproducible installs
- Supports `pip`-like commands

- 创建和管理虚拟环境
- 快速安装包
- 保持一个锁文件以实现可重复的安装
- 支持类似`pip`的命令

&nbsp;
### Installing `uv` and Python packages 安装`uv`和Python包

To install `uv`,  you can use the commands below (also see the official [Installation](https://docs.astral.sh/uv/getting-started/installation/) page for the latest recommendations).

要安装`uv`，您可以使用以下命令（还可以查看官方[安装](https://docs.astral.sh/uv/getting-started/installation/)页面以获取最新推荐）。

&nbsp;
**macOS / Linux:**

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

&nbsp;
**Windows (PowerShell):**

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Once installed, you can install new Python packages similar to how you would do it via `pip` as described in the previous section, except that you replace `pip` with `uv pip`. For example

安装完成后，您可以像上一节中通过`pip`安装新Python包一样安装新Python包，只是将`pip`替换为`uv pip`。例如：

```bash
uv pip install torch jupyterlab
```

However, if you are using `uv`, which I recommend and use myself, it's even better to use the native `uv` syntax instead of `uv pip`, as described below.

但是，如果您使用的是`uv`（我推荐并自己使用），最好使用本节下面描述的本机`uv`语法，而不是`uv pip`。

&nbsp;
### Recommended `uv` workflow 推荐的`uv`工作流程

Instead of using `uv pip`, I recommend and use the native `uv` worklow.

First, clone the GitHub repository to your local machine:

我推荐并使用本机`uv`工作流程，而不是使用`uv pip`。
首先，将GitHub仓库克隆到本地计算机：



```bash
git clone https://github.com/rasbt/reasoning-from-scratch.git
```

Next, navigate into this folder, e.g., on Linux and MacOS:

然后，导航到这个文件夹，例如在Linux和MacOS上：

```bash
cd reasoning-from-scratch
```

Then, since this folder contains a `pyproject.toml` file, you are already good to go: `uv` will automatically create a (by default invisible) virtual environment folder (`.venv`) for this `reasoning-from-scratch` project into which it installs all the dependencies the first time you run a script or open Jupyter Lab.

然后，由于这个文件夹包含一个`pyproject.toml`文件，您已经准备好了：`uv`将在您第一次运行脚本或打开Jupyter Lab时自动为这个`reasoning-from-scratch`项目创建一个（默认情况下不可见的）虚拟环境文件夹（`.venv`），并将所有依赖项安装到其中。

You will probably not need it but in general, you can install additional packages, which are not already part of the requirements listed in `pyproject.toml`, via `uv add`:

您可能不需要它，但一般来说，您可以通过`uv add`安装`pyproject.toml`中未列出的其他依赖项：


```bash
uv add llms_from_scratch
```

注：这里应该使用```uv add reasoning_from_scratch```

The above command will then add the package to the virtual environment and `pyproject.toml` file.

上面的命令将把包添加到虚拟环境中，并更新`pyproject.toml`文件。

&nbsp;
### Running code via `uv` 通过`uv`运行代码

This section describes the `uv` commands to run Jupyter Lab and Python scripts.

本节描述了通过`uv`运行Jupyter Lab和Python脚本的命令。

To open Jupyter Lab, execute:

要打开Jupyter Lab，执行：

```python
uv run jupyter lab
```

Python scripts can be run via:

Python脚本可以通过以下方式运行：

```bash
uv run python script.py
```




> **Advanced usage:** This section describes a simple way to use `uv` that looks familiar to `pip` users. If you are interested in more advanced usage, please see [this document](https://github.com/rasbt/LLMs-from-scratch/tree/main/setup/01_optional-python-setup-preferences) for more explicit instructions on managing virtual environments in `uv`. 
> If you are a macOS or Linux user and prefer the native uv commands, please refer to [this tutorial](https://github.com/rasbt/LLMs-from-scratch/blob/main/setup/01_optional-python-setup-preferences/native-uv.md). I also recommend checking the [official uv documentation](https://docs.astral.sh/uv/) for additional information.

> **高级用法：**本节描述了一种使用`uv`的方法，对`pip`用户来说很熟悉。如果您对更高级的用法感兴趣，请参阅[此文档](https://github.com/rasbt/LLMs-from-scratch/tree/main/setup/01_optional-python-setup-preferences)以获取有关在`uv`中管理虚拟环境的更明确的说明。
> 如果您是macOS或Linux用户，并更喜欢本机`uv`命令，请参阅[本教程](https://github.com/rasbt/LLMs-from-scratch/blob/main/setup/01_optional-python-setup-preferences/native-uv.md)。我建议您查看[官方uv文档](https://docs.astral.sh/uv/)以获取更多信息。



&nbsp;
## Questions? 问题？

If you have any questions, please don't hesitate to reach out via the [Discussions](https://github.com/rasbt/reasoning-from-scratch/discussions) forum in this GitHub repository.

如果您有任何问题，请随时通过此GitHub存储库中的[讨论](https://github.com/rasbt/reasoning-from-scratch/discussions)论坛与我们联系。
