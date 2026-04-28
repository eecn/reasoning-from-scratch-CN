# 在 Windows 上使用 `torch.compile()`

`torch.compile()` 依赖 *TorchInductor*，它会即时编译（JIT-compile）内核，并需要一个可用的 C/C++ 编译器工具链。

因此，在 Windows 上，使 `torch.compile` 正常工作所需的设置可能比 Linux 或 macOS 更复杂，后者通常除了安装 PyTorch 之外不需要额外步骤。

如果你是 Windows 用户，觉得使用 `torch.compile` 太麻烦或太复杂，不用担心，本仓库中的所有代码示例在不编译的情况下也能正常工作。

以下是我根据 [Daniel Kleine](https://github.com/d-kleine) 的建议和以下 [PyTorch 指南](https://docs.pytorch.org/tutorials/unstable/inductor_windows.html) 整理的一些技巧。

&nbsp;
## 1 基本设置（CPU 或 CUDA）

&nbsp;
### 1.1 安装 Visual Studio 2022

- 选择 **"使用 C++ 的桌面开发"** 工作负载。
- 确保包含 **英语语言包**（没有它，你可能会遇到 UTF-8 编码错误。）

&nbsp;
### 1.2 打开正确的命令提示符


从以下位置启动 Python：

**"x64 Native Tools Command Prompt for VS 2022"**

或

**"Visual Studio 2022 Developer Command Prompt"**。

或者，你可以通过运行以下命令手动初始化环境：

```bash
"C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Auxiliary\Build\vcvars64.bat"
```

&nbsp;
### 1.3 验证编译器是否可用

运行：

   ```bash
   cl.exe
   ```

如果你看到打印出版本信息，说明编译器已就绪。

&nbsp;
## 2 常见错误排查

&nbsp;
### 2.1 错误：`cl not found`

安装带有 "C++ build tools" 工作负载的 **Visual Studio Build Tools**，并从开发者命令提示符运行 Python。（详见 Microsoft [指南](https://learn.microsoft.com/en-us/cpp/build/vscpp-step-0-installation?view=msvc-170)）

&nbsp;
### 2.2 错误：`triton not found`（使用 CUDA 时）

手动安装 Windows 版 Triton：

```bash
pip install "triton-windows<3.4"
```

或者，如果你使用 `uv`：

```bash
uv pip install "triton-windows<3.4"
```

（如前所述，TorchInductor 进行 CUDA 内核编译时需要 triton。）



&nbsp;
## 3 附加说明

在 Windows 上，`cl.exe` 编译器只能在 Visual Studio Developer 环境中访问。这意味着在 Jupyter 等 notebook 中使用 `torch.compile()` 可能无法正常工作，除非 notebook 是从 Developer Command Prompt 启动的。

正如本文开头所述，还有一份 [PyTorch 指南](https://docs.pytorch.org/tutorials/unstable/inductor_windows.html)，一些用户在 Windows CPU 上运行 `torch.compile()` 时觉得很有帮助。但请注意，它引用的是 PyTorch 的不稳定分支，因此仅作参考使用。

**如果编译继续出现问题，请随时跳过它。它是一个不错的加分项，但对于跟随本书学习并非必需。**

