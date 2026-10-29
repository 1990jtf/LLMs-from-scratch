# 在 Apple Silicon 上修复 `uv sync` 的 torch 安装

`uv sync` 已经可以完成，当前环境装上的是 torch 2.15.0，并且可以正常导入。

## 失败原因

通用依赖解析把 Intel Mac 的 `torch<2.6` 上限也套到了这台 Apple Silicon 上，于是选中了 `torch==2.5.1`。这个版本给 Python 3.13 只发布了 `manylinux1_x86_64` 的 wheel，没有 macOS arm64 包。Apple Silicon 上对应的 wheel 从 torch 2.6 才开始有。

## `pyproject.toml` 里的调整

1. Python 3.13 和 3.14 改为要求 `torch>=2.6`。Intel Mac 的 `<2.6` 只保留在 Python 3.12 及以下，因为 3.13 起已经没有 Intel Mac 的 torch wheel。
2. 增加 `tool.uv.required-environments`，让 arm64 单独解析，不再继承 Intel Mac 的版本上限。
