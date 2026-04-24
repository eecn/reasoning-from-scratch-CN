# 测试

本目录包含仓库的 Python 测试套件。

## 本地运行

首先安装开发环境：

```bash
uv sync --group dev
```

### 1. 常规套件，跳过高开销测试（推荐）

推荐用于快速测试和新功能开发。

```bash
SKIP_EXPENSIVE=1 RUN_REAL_DOWNLOAD_TESTS=0 uv run pytest tests
```

运行单个测试文件：

```bash
SKIP_EXPENSIVE=1 RUN_REAL_DOWNLOAD_TESTS=0 uv run pytest tests/test_ch03.py
```


这是最接近默认 GitHub 测试矩阵的本地等效方式。

### 2. 常规套件加上高开销测试

有些代码默认被跳过，因为运行成本相对较高。建议在基本调试完成后运行这些测试。

```bash
SKIP_EXPENSIVE=0 RUN_REAL_DOWNLOAD_TESTS=0 uv run pytest tests
```

请注意，这会运行测试文件中由 `SKIP_EXPENSIVE` 保护的测试，但仍然排除下载大型模型检查点的真实网络/下载集成测试。

### 3. 仅下载测试

有些测试用于检查模型检查点文件是否可供下载以及服务器是否（仍然）正常工作。没有必要在本地或定期运行这些测试。这更适用于偶尔的测试。

运行这些下载测试，使用：

```bash
SKIP_EXPENSIVE=0 RUN_REAL_DOWNLOAD_TESTS=1 uv run pytest tests -k real_download
```

工作原理：

- `pytest tests` 从 `tests/` 目录收集测试
- `-k real_download` 仅保留名称包含 `real_download` 的测试

要运行更具针对性的示例，例如直接运行附录 D 的真实快照测试，使用：

```bash
SKIP_EXPENSIVE=0 RUN_REAL_DOWNLOAD_TESTS=1 uv run pytest tests/test_appendix_d.py -k real_download_1_7b
```

目前选择性启用的真实下载测试涵盖：

- `tests/test_ch03.py`：真实的 `math500_test.json` 下载和分词器下载
- `tests/test_ch06.py`：真实的数学训练集下载
- `tests/test_ch07.py`：真实的 GitHub 原始文件下载
- `tests/test_ch08.py`：真实的蒸馏（distillation）数据集和分词器下载
- `tests/test_appendix_d.py`：真实的 `Qwen/Qwen3-1.7B-Base` 快照下载


### 4. 全部运行（不推荐）

这会运行测试套件中的所有内容。请注意，这包括计算成本较高的测试（第 3 节）以及开销较大的下载测试（第 4 节）。

```bash
SKIP_EXPENSIVE=0 RUN_REAL_DOWNLOAD_TESTS=1 uv run pytest tests
```

在修改代码时不建议用于常规测试，因为文件下载非常耗费资源，没有必要定期运行。


## GitHub CI 中的运行内容

默认的 GitHub 测试矩阵运行常规套件并省略较重的测试：

- `.github/workflows/tests-linux.yml`
- `.github/workflows/tests-macos.yml`
- `.github/workflows/tests-windows.yml`
- `.github/workflows/basic-tests-pip.yml`

这些工作流设置 `SKIP_EXPENSIVE=1`，因此高开销测试在那里被跳过。原因是 GitHub CI 没有运行高开销测试所需的计算资源（如 GPU）。

真实网络/下载集成测试在单独的工作流中运行：

- `.github/workflows/real-download-tests.yml`

该工作流设置 `RUN_REAL_DOWNLOAD_TESTS=1` 并仅运行由 `-k real_download` 选中的测试。
它不属于默认的 PR/push 矩阵。它按周计划运行，也可以通过 `workflow_dispatch` 手动启动。
