

前言：
- **EditorConfig**：管**基础格式**（缩进、换行、编码、行尾空格等）
- **ESLint/Prettier**：管**语法层面风格**（命名、分号、语句换行等）
- 二者搭配使用，实现完整代码风格管控

# 1. EditorConfig

轻量的**跨编辑器 / IDE 代码基础格式统一工具**，解决团队 / 跨设备开发时，不同编辑器格式规则不一致导致的代码风格混乱、Git 格式冲突问题。

作用如下：

- 统一所有支持工具的基础代码格式（缩进、换行符、编码等）；
- 支持按文件类型精细化配置规则；
- 轻量无侵入，仅需项目根目录放配置文件，编辑器装插件即可生效。

使用：

- 在项目根目录下新建文件 `.editorconfig` , 文件内容如下：

```toml
root = true

# ---------------------------------------------------------

# 所有文件的默认配置

# ---------------------------------------------------------

[*]

charset = utf-8

indent_style = space

indent_size = 2

end_of_line = lf

insert_final_newline = true

trim_trailing_whitespace = true

# ---------------------------------------------------------

# 针对不同语言的覆盖配置

# ---------------------------------------------------------

# Markdown 文件

# 虽然通常建议去除尾部空格，但在标准 Markdown 中，行尾 2 个空格代表换行

# 如果你的团队使用这一语法，请将 trim_trailing_whitespace 设为 false

[*.md]

trim_trailing_whitespace = false

max_line_length = off

# JSON, YAML, YML 文件

# 确保缩进为 2 个空格（即使全局设为 4，这里也强制为 2）

[*.{json,yaml,yml}]

indent_size = 2

# Makefile

# 必须使用 Tab 缩进，这是语法强制要求的

[Makefile]

indent_style = tab
```


