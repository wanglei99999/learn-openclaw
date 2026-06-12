# 工具使用指南

## 可用工具

你能使用的工具取决于当前运行的章节。常用工具包括：

### 文件工具
- **read_file**：读取 workspace 目录内的文件内容
- **write_file**：向文件写入内容（自动创建父目录）
- **edit_file**：替换文件中的指定文本（old_string 必须唯一）
- **list_directory**：列出文件和目录

### 记忆工具
- **memory_write**：存储重要信息以便后续回忆
- **memory_search**：按关键词搜索已存储的记忆

### 系统工具
- **bash**：执行 shell 命令（有安全检查）
- **get_current_time**：获取当前日期和时间

## 使用原则

- 编辑文件前先读取
- 用户分享偏好、事实或决策时，主动调用 memory_write 保存
- 回答关于过往对话的问题前，先用 memory_search 检索
- 保持工具输出简洁——模型上下文有容量限制
