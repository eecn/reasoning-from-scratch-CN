
# GPU Cloud Resources GPU云资源

This section describes cloud alternatives for running the code presented in this book.

这个部分描述了运行本书中代码的云替代方案。

While the code can run on conventional laptops and desktop computers without a dedicated GPU, cloud platforms with NVIDIA GPUs can substantially improve the runtime of the code, especially in chapters 5 to 7.

虽然代码可以在没有专用GPU的常规笔记本电脑和台式电脑上运行，但具有NVIDIA GPU的云平台可以显著提高代码的运行时间，特别是在第5章到第7章中。

&nbsp;

## Using Lightning Studio 使用Lightning Studio

For a smooth development experience in the cloud, I recommend the [Lightning AI Studio](https://lightning.ai/) platform, which allows users to set up a persistent environment and use both VSCode and Jupyter Lab on cloud CPUs and GPUs.

为了在云中实现流畅的开发体验，我推荐[Lightning AI Studio](https://lightning.ai/)平台，该平台允许用户在云CPU和GPU上设置持久环境，并使用VSCode和Jupyter Lab。

Once you start a new Studio, you can open the terminal and execute the following setup steps to clone the repository and install the dependencies:

一旦你启动了一个新的工作室，你可以在终端中执行以下设置步骤来克隆仓库并安装依赖项：

```bash
git clone https://github.com/rasbt/reasoning-from-scratch.git
cd reasoning-from-scratch
pip install -r requirements.txt
```

(In contrast to Google Colab, these only need to be executed once since the Lightning AI Studio environments are persistent, even if you switch between CPU and GPU machines.)

(与Google Colab相比，这些只需要执行一次，因为Lightning AI Studio环境是持久的，即使你在CPU和GPU机器之间切换也是如此。)

Then, navigate to the Python script or Jupyter Notebook you want to run. Optionally, you can also easily connect a GPU to accelerate the code's runtime, for example, when you are pretraining the LLM in chapter 5 or finetuning it in chapters 6 and 7.

然后，导航到你想要运行的Python脚本或Jupyter Notebook。可选地，你还可以轻松地将GPU连接到加速代码的运行时间，例如，当你第5章中预训练LLM或在第6章和第7章中微调它时。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/README/studio.webp" alt="1" width="700">

&nbsp;

## Using Google Colab 使用Google Colab

To use a Google Colab environment in the cloud, head over to [https://colab.research.google.com/](https://colab.research.google.com/) and open the respective chapter notebook from the GitHub menu or by dragging the notebook into the *Upload* field as shown in the figure below.

要在云中使用Google Colab环境，请前往[https://colab.research.google.com/](https://colab.research.google.com/)，并从GitHub菜单中打开相应的章节笔记本，或者将笔记本拖到*Upload*字段中，如图所示。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/README/colab_1.webp" alt="1" width="700">


Also make sure you upload the relevant files (dataset files and .py files the notebook is importing from) to the Colab environment as well, as shown below.

另外，确保你将相关的文件（数据集文件和笔记本从中导入的.py文件）也上传到Colab环境中，如下所示。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/README/colab_2.webp" alt="2" width="700">


You can optionally run the code on a GPU by changing the *Runtime* as illustrated in the figure below.

你也可以通过改变*Runtime*来选择在GPU上运行代码，如图所示。

<img src="https://sebastianraschka.com/images/LLMs-from-scratch-images/setup/README/colab_3.webp" alt="3" width="700">


&nbsp;
## Questions? 问题？

If you have any questions, please don't hesitate to reach out via the [Discussions](https://github.com/rasbt/reasoning-from-scratch/discussions) forum in this GitHub repository.

如果你有任何问题，请通过这个GitHub仓库中的[讨论](https://github.com/rasbt/reasoning-from-scratch/discussions)论坛与我联系。