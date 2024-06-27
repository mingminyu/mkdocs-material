# 安装

[MkDocs](https://www.mkdocs.org) 是一个用于生成静态页面的程序，而 Material for MkDocs 是 MkDocs 的一个非常受欢迎的主题。如果你熟悉 Python 的话，可以使用 `pip` 包管理器来安装 Material for MkDocs。如果不熟悉的话，建议还是使用 Docker 来安装。

!!! tip

    本教程部分翻译内容参考 [Material for MkDocs](https://mkdoc-material.llango.com/getting-started) 

## 使用 pip 安装

`mkdocs-material` 是 Python 中的库，我们可以通过 `pip` 来安装它，在终端中指以下命令:

=== "最新版本"

    ```sh
    pip install mkdocs-material
    ```

=== "9.x"

    ```sh
    pip install mkdocs-material=="9.*"
    ```

执行后将自动安装所有依赖包的兼容版本：`MkDocs`，`Markdown`，`Pygments` 和 `pymdown-extensions`。Material for MkDocs 始终致力于支持最新版本，因此无需单独安装这些软件包。

## 使用 Docker 安装

如果不熟悉 Python 中 `pip` 包管理工具，那么推荐使用 Docker 来安装 Material MkDocs 也是一个非常好的方式，官方镜像已经帮我们预先安装好了所有依赖，所以我们只需要通过 `docker pull` 命令拉取最新版本即可:

=== "最新版"

    ```
    docker pull squidfunk/mkdocs-material
    ```

=== "9.x"

    ```
    docker pull squidfunk/mkdocs-material:9
    ```

`mkdocs` 可执行文件作为入口点，且 `serve` 是默认的命令。如果不熟悉 Docker 也无需担心，后面的章节都会做详细的介绍。

以下插件与 Docker 镜像捆绑在一起：

- [mkdocs-minify-plugin](https://github.com/byrnereese/mkdocs-minify-plugin)
- [mkdocs-redirects](https://github.com/datarobot/mkdocs-redirects)

??? 如何在 Docker 镜像中添加插件

    Material for MkDocs 捆绑了有用和常用的插件，同时尽量不扩大官方镜像的大小。如果没有包含要使用的插件，请创建一个新的 Dockerfile 并使用自定义安装例程扩展官方的Docker 映像:

    === "Material for MkDocs"

        创建一个 `Dockerfile` 并在官方镜像上进行扩展:

        ``` Dockerfile title="Dockerfile"
        FROM squidfunk/mkdocs-material
        RUN pip install mkdocs-macros-plugin
        RUN pip install mkdocs-glightbox
        ```

    === "内测版本"

        克隆 Material MkDocs 内测版本的仓库，并在根目录下创建一个名为 `user-requirements.txt` 文件。 接着添加需要安装的组件，例如:

        ``` txt title="user-requirements.txt"
        mkdocs-macros-plugin
        mkdocs-glightbox
        ```
    
    接下来，使用以下命令构建镜像:

    ```
    docker build -t squidfunk/mkdocs-material .
    ```

    完成过就可以像使用官方的镜像一样使用新镜像。

## 使用 Git 安装

克隆 GitHub 上 Material for MkDocs 仓库，如果是想使用最最新的版本，可以尝试此种方法:

```
git clone https://github.com/squidfunk/mkdocs-material.git
```

接下来使用 `pip` 命令来安装:

```sh
pip install -e mkdocs-material
```
