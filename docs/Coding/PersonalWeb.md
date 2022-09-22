# 免费创建静态网站

## 参考资料

全程教程：[GitHub_Pages个人网站构建与发布]<https://www.bilibili.com/video/BV1hL4y1w72r/?spm_id_from=333.337.search-card.all.click&vd_source=81f507780c5f3724647df2b527363b64>

官方文档：[MkDocs]<https://www.mkdocs.org/>, [Material_for_Mkdocs]<https://squidfunk.github.io/mkdocs-material/>

Mkdocs构建示例：<https://yang-xijie.github.io/BLOG/Markdown/mkdocs-site/>

## 构建流程概览

1. GitHub创建仓库
2. 创建mkdocs.yml，用mkdocs提供的主题、边栏等设置
3. 创建docs文件夹，加入网站文本
4. 在GitHub上创建.github/workflow，生成website

## 安装mkdocs

详细说明：[Getting Started - MkDocs]<https://www.mkdocs.org/getting-started/#installation>

为了更方便地安装，电脑上需有一个python，可以检查电脑上否已有，并检查版本

```cpp
$ python --version
Python 3.8.2
$ pip --version
pip 20.0.2 from /usr/local/lib/python3.8/site-packages/pip (python 3.8)
```
如果没有pip的话，下载get_pip到电脑上，安装并升级

```cpp
python get-pip.py
pip install --upgrade pip
```

Install mkdocs by pip

```cpp
pip install mkdocs
$ mkdocs --version
mkdocs, version 1.2.0 from /usr/local/lib/python3.8/site-packages/mkdocs (Python 3.8)
```

## 创建新项目

Running the following command to create a new project by mkdocs

```cpp
mkdocs new my-project
cd my-project
```

Open mkdocs.yml in vs code, run the following code in terminal to generate a local website as editing reference

```cpp
$ mkdocs serve
INFO    -  Building documentation...
INFO    -  Cleaning site directory
[I 160402 15:50:43 server:271] Serving on http://127.0.0.1:8000
[I 160402 15:50:43 handlers:58] Start watching changes
[I 160402 15:50:43 handlers:60] Start detecting changes
```

## 编辑网页

We use the material for editing the appearance of website with name: material

The meaning of each func is explained in material for mkdocs: Installation - Material for MkDocs

## 添加网站文本内容

In order to link a page to a section, create a new document with the name index.md in the respective folder, and add it to the beginning of your navigation section in mkdocs.yml:

```markdown
nav:
  - Section:
    - section/index.md
    - Page 1: section/page-1.md
    ...
    - Page n: section/page-n.md
```

Create respective documents in folder docs

## 发布网站

Create a repository on GitHub page, upload the previous documents to the repository, and create a folder: '.github/workflow/anyName.yml'

Paste:

```markdown
name: publish site
on: # 在什么时候触发工作流
  push: # 在从本地main分支被push到GitHub仓库时
    branches:
      - main
  pull_request: # 在main分支合并别人提的pr时
    branches:
      - main
jobs: # 工作流的具体内容
  deploy:
    runs-on: ubuntu-latest # 创建一个新的云端虚拟机 使用最新Ubuntu系统
    steps:
      - uses: actions/checkout@v2 # 先checkout到main分支
      - uses: actions/setup-python@v2 # 再安装Python3和相关环境
        with:
          python-version: 3.x
      - run: pip install mkdocs-material # 使用pip包管理工具安装mkdocs-material
      - run: mkdocs gh-deploy --force # 使用mkdocs-material部署gh-pages分支
```

The website is generated on 'settings > pages '