+++
title = 'Pre-commit 使用本地 Hook'
tags = ['Git', 'Pre-commit', 'Hook']
date = 2024-07-04T00:20:20+08:00
draft = false
+++

[Pre-commit](https://pre-commit.com/) 是一个用于管理和维护多种 Git Hook 的工具。它可以帮助我们在提交代码之前运行一些代码检查，以确保代码的质量。Pre-commit 支持多种语言和工具，包括 Python、JavaScript、Rust、Shell、Docker、Markdown 等等。一般来说，我们可以使用很多网上仓库中的 Hook，比如 [pre-commit-hooks](https://pre-commit.com/hooks.html) 里面的 Hook。当然，我们也可以自己编写 Hook，作为本地 Hook 使用。

## 安装 Pre-commit

首先，我们需要安装 Pre-commit。我们可以使用 pip 来安装 Pre-commit。

```bash
pip install pre-commit
```

## 初始化 Pre-commit

然后，我们需要在项目的根目录下初始化 Pre-commit。我们可以使用 `pre-commit` 命令来初始化 Pre-commit。

```bash
pre-commit install
```

这个命令会在项目的根目录下创建一个 `.pre-commit-config.yaml` 文件，这个文件是 Pre-commit 的配置文件，我们可以在这个文件中定义我们的 Hook。

## 使用 Pre-commit

最后，我们可以使用 Pre-commit。我们可以使用 `pre-commit run` 命令来运行 Pre-commit，它会自动运行我们的 Hook。

```bash
pre-commit run
```

这样，我们就可以在提交代码之前运行我们的 Hook 了。

## 本地 Hook

Pre-commit 支持本地 Hook，我们可以在项目中创建一个 `.pre-commit-config.yaml` 文件，然后在这个文件中定义我们的 Hook。这样，我们就可以在提交代码之前运行我们自己的 Hook 了。

### 创建 `.pre-commit-config.yaml` 文件

首先，我们需要在项目的根目录下创建一个 `.pre-commit-config.yaml` 文件。这个文件是 Pre-commit 的配置文件，我们可以在这个文件中定义我们的 Hook。

```yaml
repos:
  - repo: local
    hooks:
      - id: my-local-hook
        name: My Local Hook
        entry: scripts/my-local-hook.sh
```

在这个例子中，我们定义了一个本地 Hook，它的 id 是 `my-local-hook`，名字是 `My Local Hook`，入口是 `./scripts/my-local-hook.sh`。这个 Hook 的入口是一个 Shell 脚本，我们可以在这个脚本中定义我们的代码检查逻辑。

### 创建 `my-local-hook.sh` 文件

然后，我们需要在项目的根目录下创建一个 `scripts` 目录，并在这个目录下创建一个 `my-local-hook.sh` 文件。这个文件是我们的 Hook 的入口，我们可以在这个文件中定义我们的代码检查逻辑。

```bash
#!/bin/bash

echo "Running My Local Hook..."

# Add your code check logic here
```

在这个例子中，我们定义了一个简单的 Shell 脚本，它只是输出了一行信息。我们可以在这个脚本中添加我们的代码检查逻辑。

### 运行 Pre-commit

最后，我们可以运行 Pre-commit，它会自动运行我们的 Hook。

```bash
pre-commit run
```

这样，我们就可以在提交代码之前运行我们自己的 Hook 了。

## 注意事项

- entry 路径是相对于 `.pre-commit-config.yaml` 文件的路径。
- entry 脚本需要有可执行权限，可以使用 `chmod +x scripts/my-local-hook.sh` 命令添加可执行权限。
- entry 脚本不仅可以是 Shell 脚本，还可以是 Python 脚本、JavaScript 脚本等等。
- 可以在 entry 脚本中调用其他工具，比如 `flake8`、`black`、`isort` 等等，来进行代码检查。
