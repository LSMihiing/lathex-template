# LatHeX 模板中文使用指南

这是一个简洁、易用的 LaTeX 报告和书籍模板。本文档旨在帮助 LaTeX 初学者理解和使用此模板。

## 预览

该模板提供了多种主题样式，您可以查看原作者提供的实际报告预览：

* **Dark Theme (暗色主题)**: [暗色封面](images/dark-title.png) | [暗色内页](images/dark-page.png)
* **Light Theme (亮色主题)**: [亮色封面](images/light-title.png) | [亮色内页](images/light-page.png)
* **Forta Theme (Forta 主题)**: [Forta 封面](images/forta-title.png) | [Forta 内页](images/forta-page.png)
  * 注意：Forta 主题使用了特定的 ttf 字体文件，`pdflatex` 可能无法正确处理，推荐使用 `lualatex` 或 `xetex`。

## 快速开始

### 1. 获取代码

首先，克隆本仓库到您的本地：

```bash
git clone https://github.com/LSMihiing/lathex-template.git
cd lathex-template
```

### 2. 编译示例文件

模板提供了一个示例文件 `demo/book/dark.tex` (暗色主题) 来演示其用法。编译步骤如下：

**推荐编译器**: `lualatex` 或 `xetex`。`pdflatex` 也能基本工作，但在 Forta 主题的字体处理上存在差异。

**编译命令 (以 `dark.tex` 为例)**:

编译 LaTeX 文档通常需要多次运行编译器，以确保交叉引用（如目录、参考文献、索引等）正确生成。

```bash
# 步骤 1: 首次编译主文件 (例如 dark.tex)
# --output-dir build/ 指定输出目录为 build/
lualatex --output-dir build/ demo/book/dark.tex

# 步骤 2: 生成索引 (如果您的文档包含索引)
# makeindex build/book.idx -s indexstyle.ist
# 注意：示例中似乎没有直接使用 makeindex 生成 .idx，而是可能通过宏包自动处理。
# 如果需要手动创建索引，确保你的 .tex 文件中有相关的索引命令，并且编译后生成了 .idx 文件。
# indexstyle.ist 是索引样式文件，如果项目中有提供，请使用。

# 步骤 3: 处理参考文献 (如果您的文档包含参考文献)
# 使用 biber 处理 .bcf 文件 (由 biblatex 生成)
# 需要在 demo/book/ 目录下执行，因为它会查找 dark.bcf 文件
cd demo/book/
biber dark 
# 或者指定完整路径: biber build/dark (如果 .bcf 文件在 build 目录)
# 返回上一级目录
cd ../..

# 步骤 4: 再次编译主文件，以包含索引和参考文献
lualatex --output-dir build/ demo/book/dark.tex

# 步骤 5: (可选) 有时可能需要第三次编译以确保所有交叉引用都正确
lualatex --output-dir build/ demo/book/dark.tex
```

**编译流程详解**:

1. **`lualatex --output-dir build/ demo/book/dark.tex`**:
   * `lualatex`: 调用 LuaLaTeX 编译器。它是 LaTeX 的一个现代化版本，支持 Unicode、OpenType 字体等特性。
   * `--output-dir build/`: 这个选项告诉编译器将所有生成的文件（包括 PDF、auxiliary 文件如 `.aux`, `.log`, `.toc` 等）都放置在 `build/` 目录下。这样做可以保持项目根目录的整洁。
   * `demo/book/dark.tex`: 这是要编译的主 LaTeX 文件。
   * 在第一次编译时，LaTeX 会读取你的 `.tex` 文件，处理内容和命令，并生成一个初始的 PDF。同时，它会记录下所有交叉引用的信息（如章节号、页码、参考文献引用等）到辅助文件（主要是 `.aux` 文件）中。目录信息会写入 `.toc` 文件，图形列表信息写入 `.lof` 文件，表格列表信息写入 `.lot` 文件。
2. **`makeindex build/book.idx -s indexstyle.ist`** (如果使用索引):
   * `makeindex`: 这是标准的 LaTeX 索引处理程序。
   * `build/book.idx`: 这是 LaTeX 编译过程中生成的原始索引数据文件。你需要确保你的文档中使用了 `\makeindex` 命令，并且有 `\index{条目}` 这样的命令来标记索引条目。
   * `-s indexstyle.ist`: 指定一个索引样式文件 (`.ist`)。这个文件定义了索引的格式，例如如何排序、如何显示页码等。如果项目中没有提供 `.ist` 文件，可以省略 `-s` 选项，`makeindex` 会使用默认样式。
   * 此命令会读取 `.idx` 文件，并根据样式生成一个排序和格式化好的索引文件（通常是 `.ind` 文件）。
   * **注意**: 原项目的编译命令中提到了 `makeindex build/book.idx -s indexstyle.ist`。你需要检查 `build/` 目录下是否真的生成了 `book.idx` 文件。如果主文件是 `dark.tex`，那么索引文件更可能是 `dark.idx`。同时，`indexstyle.ist` 文件需要存在于 `makeindex` 可以找到的路径。
3. **`biber demo/book/dark.tex`** (或 `biber dark` 在 `demo/book/` 目录):
   * `biber`: 这是 `biblatex` 包推荐的参考文献处理后端。与传统的 BibTeX 不同，Biber 对 Unicode 有更好的支持，功能也更强大。
   * `demo/book/dark.tex` (或者直接用文件名 `dark`，如果 `dark.bcf` 文件在当前目录): Biber 会查找由 `biblatex` 生成的控制文件（`.bcf` 文件，例如 `dark.bcf`）。这个文件包含了所有参考文献的引用信息和书目数据库（`.bib` 文件）的路径。
   * Biber 会读取 `.bcf` 文件，然后从你的 `.bib` 文件中提取所需的参考文献条目，并根据你在 LaTeX 文档中设定的引用样式进行排序和格式化，最终生成一个 `.bbl` 文件。这个 `.bbl` 文件包含了格式化好的参考文献列表，LaTeX 会在下一次编译时将其读入。
   * **执行路径**: `biber` 通常需要在包含 `.bcf` 文件的目录中执行，或者你能正确指定 `.bcf` 文件的路径。如果 `.aux` 和 `.bcf` 文件都在 `build/` 目录下，那么应该是 `cd build/` 然后 `biber dark`，或者 `biber build/dark`。原作者的命令 `biber demo/book/dark.tex` 可能是假设 `biber` 能从主 `.tex` 文件推断出 `.bcf` 文件的位置，或者 `.bcf` 文件被配置输出到了 `demo/book/` 目录。你需要根据实际生成的文件位置调整。
4. **`lualatex --output-dir build/ demo/book/dark.tex`** (再次编译):
   * 在这次编译中，LaTeX 会读取之前步骤中更新的辅助文件。
   * 它会读入由 `biber` 生成的 `.bbl` 文件，从而将格式化好的参考文献列表插入到文档中。
   * 它会读入由 `makeindex` 生成的 `.ind` 文件（如果使用了索引），从而将索引插入到文档中。
   * 同时，它会根据第一次编译时记录的交叉引用信息来填充文档中的引用（例如，“参见第 X 章第 Y 页”）。目录、图表列表也会在这次编译中基本成型。
5. **`lualatex --output-dir build/ demo/book/dark.tex`** (可选的再次编译):
   * 有时，在第二次编译之后，某些交叉引用（特别是页码引用）可能仍然没有更新到最终状态。例如，如果参考文献列表的加入导致了后续内容的页码发生变化，那么文档中指向这些内容的引用页码就需要更新。
   * 第三次编译通常足以解决所有这些交叉引用的问题，确保文档中的所有编号和页码都是最终和正确的。

**总结编译顺序**:

一个完整的 LaTeX 编译流程通常是：
`LaTeX` -> `Biber` (或 `BibTeX`) -> `LaTeX` -> `LaTeX`
如果包含索引：
`LaTeX` -> `makeindex` -> `Biber` (或 `BibTeX`) -> `LaTeX` -> `LaTeX`

对于此模板，一个可靠的编译顺序是：
1. `lualatex` (生成 .aux, .bcf, .idx 等)
2. `biber` (处理参考文献，生成 .bbl)
3. (可选) `makeindex` (处理索引，生成 .ind)
4. `lualatex` (读入 .bbl, .ind，更新目录和交叉引用)
5. `lualatex` (确保所有交叉引用都正确)

最终的 PDF 文件会生成在 `build/` 目录下 (例如 `build/dark.pdf`)。

## 模板结构

项目的目录结构如下：

```text
<项目根目录>
   |
   |-- demo/book/             # 示例文件，展示如何使用模板
   |   |-- bibliography/      # (可能) 存放 .bib 参考文献数据库文件 (示例中未直接显示，但通常会放在这里或主tex文件同级)
   |   |-- images/            # 示例文件使用的图片
   |   |-- sections/          # 示例文件的各个章节内容
   |   |   |-- part1/
   |   |   |-- appendices/
   |   |-- context.tex        # 示例文件的元数据 (作者, 标题等)
   |   |-- dark.tex           # 暗色主题的入口文件
   |   |-- forta.tex          # Forta主题的入口文件
   |   |-- light.tex          # 亮色主题的入口文件
   |   |-- main.tex           # 示例文件的主要内容结构 (引入章节等)
   |
   |-- build/                 # 编译后生成的 PDF 和临时文件存放于此
   |
   |-- images/                # README.md 中使用的预览图片
   |
   |-- template/              # 模板的核心定义文件
   |   |-- book/              # 书籍类型模板的特定定义
   |   |-- forta/             # Forta 主题的特定定义 (如字体)
   |   |-- generic/           # 通用模板定义 (所有类型和主题共享)
   |
   |-- .gitignore             # Git忽略文件配置
   |-- README.md              # (您正在阅读的) 项目说明文档
```

**关键文件和目录解释**:

* **`demo/book/`**: 这个目录是理解如何使用模板的最佳起点。
  * `dark.tex`, `light.tex`, `forta.tex`: 这些是不同主题的 LaTeX 入口文件。它们通常很简单，主要负责：
    1. 设置文档类型 (`\documentclass`)。
    2. 引入 `context.tex` 来加载文档元数据。
    3. 引入模板核心文件 (例如 `template/book/main.tex`)。
    4. 选择一个主题 (例如 `\togglethemedark`)。
    5. 引入实际的文档内容 (例如 `demo/book/main.tex`)。
  * `demo/book/main.tex`: 这个文件定义了书籍的整体结构，例如封面、目录、各个章节的引入。它通过 `\input{}` 命令将 `sections/` 目录下的各个 `.tex` 文件组合起来。
  * `demo/book/context.tex`: 在这里定义文档的作者、标题、日期等元数据。修改这些变量会自动更新到文档的相应位置。
  * `demo/book/sections/`: 存放书籍的实际内容，按部分 (part) 和章节 (chapter) 组织。这种模块化的方式使得管理大型文档更加容易。
* **`template/`**: 这是模板的核心所在。
  * `template/generic/`: 包含通用的 LaTeX 设置，如字体、页面几何、链接样式、列表样式、数学环境、代码高亮等。这些设置被所有特定类型的模板（如 `book`）共享。
  * `template/book/`: 包含专门为书籍类型设计的模板元素，如书籍的页面布局、标题样式（部分、章节、小节等）、封面设计、目录样式、背景和参考文献格式。
  * `template/forta/`: 包含 Forta 主题特有的设置，主要是字体文件和相关的字体配置。

## 如何使用和调整模板

### 1. 选择一个入口文件开始

从 `demo/book/` 目录中选择一个主题的入口文件作为起点，例如 `dark.tex`。您可以将其复制到您的新项目，或者直接修改它。

### 2. 修改元数据

打开 `demo/book/context.tex` (或者您复制后的版本)，修改以下命令的值：

```latex
\newcommand{\AUTHOR}{您的名字}
\newcommand{\CLIENT}{您的客户 (如果适用)}
\newcommand{\DATE}{当前日期}
\newcommand{\VERSION}{文档版本}
\newcommand{\TITLE}{您的书或报告的标题}
\newcommand{\SUBTITLE}{您的副标题 (可选)}
\newcommand{\SUBJECT}{文档主题}
\newcommand{\URL}{您的网址 (可选)}
```

### 3. 组织您的内容

* **创建章节文件**: 在 `demo/book/sections/` 目录下（或您自定义的目录结构中），为您的每个章节创建独立的 `.tex` 文件。例如，`chapter1.tex`, `chapter2.tex` 等。
* **编写内容**: 在这些章节文件中编写您的 LaTeX 内容。
* **引入章节**: 打开 `demo/book/main.tex` (或您复制后的版本)，参照示例，使用 `\input{}` 命令引入您的章节文件。您可以调整 `\part{}` 和 `\chapter{}` 的结构来组织您的文档。

  例如，要添加一个新的章节 "我的第一章"，内容在 `sections/my_first_chapter.tex`：
  ```latex
  % ... 其他内容 ...
  \part{第一部分} % 如果需要分部分
  \chapter{我的第一章} \label{ch:myfirstchapter} % \label 用于交叉引用
  \input{demo/book/sections/my_first_chapter} % 注意，通常不需要 .tex 后缀
  % ... 其他章节 ...
  ```

### 4. 调整主题和样式 (进阶)

如果您想更深入地自定义模板，可以探索 `template/` 目录下的文件。

* **切换主题**:
  在您的主入口文件 (如 `dark.tex`) 中，可以看到类似 `\togglethemedark` 的命令。模板可能定义了其他主题切换命令，例如 `\togglethemelight` 或 `\togglethemeforta`。您可以尝试修改这些命令来改变文档的整体外观。这些命令通常定义在 `template/generic/themes.tex` 或特定模板的主文件中。
* **修改颜色**:
  颜色通常在主题相关的 `.tex` 文件中定义，例如 `template/generic/themes.tex` 或 `template/book/background.tex` 等。您可以查找 `\definecolor` 命令并修改其 RGB 或其他颜色模型的值。
* **修改字体**:
  * 通用字体设置可能在 `template/generic/fonts.tex` 中。
  * Forta 主题的字体在 `template/forta/fonts.tex` 中定义，并使用了 `template/forta/fonts/` 目录下的 `.ttf` 文件。
  * 您可以使用 `fontspec` 包 (如果使用 `lualatex` 或 `xetex`) 来加载系统安装的字体或项目中的字体文件。
* **修改页面布局**:
  页面边距、页眉页脚等设置通常在 `template/generic/geometry.tex` 和 `template/generic/headers.tex` (通用设置) 以及 `template/book/page.tex` (书籍特定设置) 中。这些文件通常使用 `geometry` 和 `fancyhdr` 等宏包。
* **修改标题样式**:
  章节、小节等标题的格式定义在 `template/book/titles.tex` 中，可能使用了 `titlesec` 或类似的宏包。
* **修改封面**:
  封面设计在 `template/book/cover.tex` 中。这部分可能包含复杂的 TikZ 代码或图像排版。

**LaTeX 基础知识提示**:

* **命令 (Commands)**: 以反斜杠 `\` 开头，例如 `\chapter`, `\textbf`。
* **环境 (Environments)**: 以 `\begin{environment_name}` 开始，以 `\end{environment_name}` 结束，例如 `\begin{itemize} ... \end{itemize}`。
* **宏包 (Packages)**: 通过 `\usepackage{package_name}` 引入，用于扩展 LaTeX 的功能，例如 `\usepackage{graphicx}` 用于插入图片，`\usepackage{amsmath}` 用于高级数学公式。模板已经为您引入了许多必要的宏包。
* **注释**: 以 `%` 开头的行是注释，LaTeX 会忽略它们。
* **编译**: LaTeX 代码需要编译才能生成 PDF。如前所述，通常需要多次编译。
* **错误处理**: 如果编译出错，LaTeX 会在日志文件 (`.log`) 中提供错误信息。仔细阅读错误信息通常能帮助定位问题。

## 更多 LaTeX 知识

对于 LaTeX 初学者，以下是一些有用的资源：

* [LaTeX Wikibook](https://en.wikibooks.org/wiki/LaTeX): 非常全面的 LaTeX 指南。
* [Overleaf 学习中心](https://www.overleaf.com/learn): Overleaf 是一个在线 LaTeX 编辑器，其学习中心有很多入门教程。
* [一份（不太）简短的 LaTeX 2ε 介绍 (lshort)](https://ctan.org/pkg/lshort-zh-cn): 经典的中文 LaTeX 入门文档。

希望这份指南能帮助您顺利使用此 LatHeX 模板！

---

## 原始仓库说明 (GitHub README.md 内容翻译)

## LatHeX 模板

> 一个简洁、无忧的 LaTeX 报告和书籍模板。

您可以在一份[真实世界的报告][forta-evasion-report]中预览该模板。

### 原始仓库预览

| 暗色主题                                       | 亮色主题                                         | Forta 主题                                         |
| :----------------------------------------------: | :-------------------------------------------------: | :-------------------------------------------------: |
| [![暗色主题封面][demo-dark-title]][demo-dark] | [![亮色主题封面][demo-light-title]][demo-light] | [![Forta 主题封面][demo-forta-title]][demo-forta] |
| [![暗色主题内页][demo-dark-page]][demo-dark]        | [![亮色主题内页][demo-light-page]][demo-light]        | [![Forta 主题内页][demo-forta-page]][demo-forta]        |

### 原始仓库用法

该模板通过仓库中的示例文档进行演示：

```bash
# 获取代码
git clone https://github.com/apehex/lathex-template.git && cd lathex-template/

# 编译
lualatex --output-dir build/ demo/book/dark.tex
makeindex build/book.idx -s indexstyle.ist
biber demo/book/dark.tex
lualatex --output-dir build/ demo/book/dark.tex
```

推荐的编译器是 `lualatex` 和 `xetex`，尽管 `pdflatex` 大部分情况下也能工作。
唯一的区别在于 `Forta` 主题的字体：它导入了 `ttf` 文件，而 `pdflatex` 无法处理这些文件。

### 原始仓库模板结构

项目具有以下树形结构：

```text
< 项目根目录 >
   |
   |-- bibliography/          # 参考文献
   |
   |-- build/                 # 编译后的 pdf 及临时文件存放处
   |
   |-- images/                # 文档中使用的资源文件
   |
   |-- sections/              # 所有独立章节
      |
      |-- part1/              # 第一部分的章节
      |
      |-- appendices/         # 文档末尾的附录
   |
   |-- template/              # 实际的模板定义，独立于其呈现的文档
   |
   |-- context.tex            # 可选脚本，包含元数据 (作者, 修订版, 日期等)
   |
   |-- main.tex               # 将所有部分组装成一个文档的脚本
```

### 原始仓库致谢

Solidity 的语法高亮由 [Sergei Tikhomirov][solidity-syntax-highlighting] 制作。

模板的其余部分完全是原创工作。
但它仍然植根于下面提到的流行的 LaTeX 脚本。

#### Legrand Orange Book

该模板始于 [Legrand Orange 模板][legrand-orange-book]。

#### Latexdraw.com

封面页始于 [Latexdraw][latexdraw-cover-pages] 中的模板。

### 原始仓库许可证

本作品根据 GNU [aGPL v3](LICENSE) 许可证授权。

---

[demo-dark]: ../build/dark.pdf
[demo-forta]: ../build/forta.pdf
[demo-light]: ../build/light.pdf
[demo-dark-page]: ../images/dark-page.png
[demo-forta-page]: ../images/forta-page.png
[demo-light-page]: ../images/light-page.png
[demo-dark-title]: ../images/dark-title.png
[demo-forta-title]: ../images/forta-title.png
[demo-light-title]: ../images/light-title.png
[forta-evasion-report]: https://github.com/apehex/web3-evasion-techniques/blob/main/report/forta.pdf
[latexdraw-cover-pages]: https://latexdraw.com/tikz-cover-pages-gallery/
[legrand-orange-book]: https://www.latextemplates.com/template/legrand-orange-book
[solidity-syntax-highlighting]: https://github.com/s-tikhomirov/solidity-latex-highlighting
