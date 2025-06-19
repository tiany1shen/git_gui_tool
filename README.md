# git_gui_tool

一个在命令行使用的Git-GUI小工具

[TOC]

# 推荐项目目录结构

```
git_gui_tool/
  ├── git_executor.py         # Git 命令执行器模块
  ├── gui_app.py              # Tkinter GUI 主程序
  ├── cli_launcher.py         # 命令行启动入口
  ├── tests/                  # 测试代码目录
  └── README.md
```

# 分级开发 ToDo List

- [ ] 1. 环境搭建
    - [ ] 安装 Python
    - [ ] 确认 Tkinter 已随 Python 安装
- [ ] 2. GitExecutor 模块开发
    - [ ] 实现 get_status()，确保能正确获取 Git 状态
    - [ ] 实现 add_files()
    - [ ] 实现 get_branches()
    - [ ] 实现 checkout_branch()
    - [ ] 实现 commit_changes()
    - [ ] 为每个函数编写简单测试代码，验证功能
- [ ] 3. GUI 界面模块开发（逐个功能）
    - [ ] 主窗口：搭建基本骨架，包含三个按钮
    - [ ] Add 界面
        - [ ] 显示文件列表（先用模拟数据，再连接 GitExecutor）
        - [ ] 实现文件多选和暂存逻辑
        - [ ] 点击"暂存"按钮后，调用 GitExecutor.add_files()，关闭窗口
    - [ ] Checkout 界面
        - [ ] 显示分支列表
        - [ ] 实现分支选择和切换逻辑
        - [ ] 点击"切换"按钮后，调用 GitExecutor.checkout_branch()，关闭窗口
    - [ ] Commit 界面
        - [ ] 实现文本输入框和提交按钮
        - [ ] 点击"提交"按钮后，调用 GitExecutor.commit_changes()，关闭窗口
        - [ ] 错误和成功信息反馈：确保 GUI 能显示 Git 命令的输出或错误信息
- [ ] 4. CLI Launcher 开发
    - [ ] 编写 Python 脚本作为程序入口点
    - [ ] 根据用户选择的操作，实例化并启动对应 GUI 界面
    - [ ] 处理 GUI 退出后的结果，并打印到命令行
- [ ] 5. 打包与部署（可选）
    - [ ] 使用 PyInstaller 或 cx_Freeze 打包为可执行文件
    - [ ] 创建 shell 脚本（如 my-git-gui.sh 或 my-git-gui.bat）启动可执行文件

# 开发日志

## 技术栈
- Python 3
- Tkinter

## 核心组件设计
整个工具可以分为几个主要部分：

### Git 命令执行器 (GitExecutor)
这是一个独立的模块，负责执行所有的 Git 命令行操作，并返回结果。

- 职责：
    - 执行 git status --porcelain 获取文件状态。
    - 执行 git add <file> 暂存文件。
    - 执行 git branch 获取分支列表。
    - 执行 git checkout <branch> 切换分支。
    - 执行 git commit -m "message" 提交。
    - 处理 Git 命令执行过程中的标准输出、标准错误和返回码。
- 实现要点：
    - 使用 subprocess.run()，设置 capture_output=True 和 text=True 来捕获输出。
    - 捕获 CalledProcessError 来处理 Git 命令执行失败的情况。
    - 为不同的 Git 命令编写封装函数，例如 get_status(), add_files(files), get_branches(), checkout_branch(branch_name), commit_changes(message)。

### GUI 界面模块 (GitGuiApp)
这是用户将直接交互的部分，负责显示界面和收集用户输入。

- 职责：
    - 主窗口： 包含选择操作的按钮或菜单（Add, Checkout, Commit）。
    - Add 界面：
        - 显示文件列表（通过 GitExecutor.get_status() 获取）。
        - 每个文件旁边有复选框，供用户选择。
        - 一个"暂存"按钮。
    - Checkout 界面：
        - 显示分支列表（通过 GitExecutor.get_branches() 获取）。
        - 使用列表框或下拉菜单供用户选择。
        - 一个"切换"按钮。
    - Commit 界面：
        - 一个多行文本框用于输入提交信息。
        - 一个"提交"按钮。
        - 显示操作结果或错误提示。
- 实现要点：
    - 使用 tkinter.Tk() 创建主窗口。
    - 使用 tkinter.Frame 来组织不同的操作界面（或使用 tkinter.Toplevel 创建独立的小窗口）。
    - 使用 tkinter.Checkbutton 用于文件选择。
    - 使用 tkinter.Listbox 或 tkinter.ttk.Combobox 用于分支选择。
    - 使用 tkinter.Text 用于提交信息输入。
    - 按钮点击事件绑定到相应的 GitExecutor 方法。
    - 操作完成后，关闭 GUI 窗口并返回结果。

## 命令行接口 (CLI Launcher)
这是用户在终端中调用的部分，负责启动 GUI。

- 职责：
    - 解析命令行参数（如果需要）。
    - 启动 Tkinter GUI 应用程序。
    - 在 GUI 退出后，捕获其返回的结果（例如，操作是否成功，以及 Git 命令的输出）。
    - 将结果打印回命令行。
- 实现要点：
    - 使用 sys.argv 或 argparse 处理命令行参数。
    - 实例化 GitGuiApp 并调用其 mainloop() 方法。
    - GUI 应用程序可以通过设置一个公共变量或使用事件机制来传递操作结果给 CLI Launcher。
