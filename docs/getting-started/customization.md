# 自定义

<head>
    <style>
        .github {
            color: #EAFF4D
        }
    </style>
</head>

:fontawesome-brands-youtube:{ .github }

使用 Material MkDocs 可以让基于 MkDocs 构建的网站变得更加漂亮。然而有时候，可能需要对主题做些许改动才能符合我们自己的需求。

## 添加资源文件

MkDocs 提供了多种方法来自定义一个主题，我们可以在 `docs` 目录中添加 CSS 和 JS 文件，来对 Material MkDocs 的默认样式进行一些调整。

### 添加 CSS

如果想调整颜色或者一些元素间的距离等，可以通过单独添加 CSS 样式来实现。最简单的方法是在 `docs` 目录下添加CSS文件，类似与这种结构：

```{ .sh .no-copy }
.
├─ docs/
│  └─ stylesheets/
│     └─ extra.css
└─ mkdocs.yml
```

接下来添加如下内容到 mkdocs.yml 文件中:

```yaml
extra_css:
  - stylesheets/extra.css
```

??? tip "在图标中应用自定义 CSS 样式"
    1. 在 Markdown 文件头部直接定义 CSS 央视内容，然后使用 `:material-github:{ .github }`。

        ```css
        <head>
            <style>
                .github {
                    color: "#EAFF4D"
                }
            </style>
        </head>
        ```

    2. 直接在图标后面添加 `{ style="xxx"}` 来自定义 CSS 样式

        ```
        :fontawesome-brands-youtube:{ style="color: #EE0F0F" }
        ```



### 添加 JavaScript

如果你想新增一些语言的高亮语法或者添加自定义的主题逻辑，可以在 `docs` 文目录下添加 JavaScript 文件:

```{ .sh .no-copy }
.
├─ docs/
│  └─ javascripts/
│     └─ extra.js
└─ mkdocs.yml
```

接下来添加如下内容到 `mkdocs.yml` 文件中:

``` yaml
extra_javascript:
  - javascripts/extra.js
```


??? tip "如何集成第三方 JavaScript 库"
    
    您可能只想在浏览器完全加载页面后运行 JavaScript 代码，这意味着你需要创建一个回调函数，来响应 Material MkDocs 暴露出来的 `document$` 可观察变量上的事件。如果您你想使用 ** 即时加载 ** (instant loading)，那么使用 `document$` 可观察变量就尤为重要，因为它不会导致浏览器页面刷新，但可观察变量上的订阅者能够收到通知。

    ``` javascript
    document$.subscribe(function() {
      console.log("Initialize third-party libraries here")
    })
    ```

    `document$` 属于是 [可观察的RxJS](https://rxjs.dev/api/index/class/Observable
    )，你可以调用 `subscribe()` 函数任意多次来附加不同的功能。

## 主题扩展

如果想要更改最终生成的 HTML 文件的内容（例如，添加或删除部分内容），此时需要对主题进行扩展。MkDocs 支持[主题扩展](https://www.mkdocs.org/user-guide/customizing-your-theme/#using-the-theme-custom_dir)，这是一种覆写部分 Material MkDocs 非常简易的方法，并不需要 fork 整个仓库。此外，该方法也能保证 Material MkDocs 可以非常容易地升级到最新版。

### 设置主题以及主题结构

我们只需要在 `mkdocs.yml` 中正常启用 Material MkDocs，并创建一个 `overrides` 新目录，然后在 `mkdocs.yml` 通过 [`custom_dir`](https://www.mkdocs.org/user-guide/configuration/#custom_dir) 参数引用该文件夹：

```yaml
theme:
  name: material
  custom_dir: overrides
```

!!! warning "主题扩展的必要条件"

    由于 `custom_dir` 参数是在主题扩展进程进行调用的，所以我们需要使用 `pip` 方式来安装 Material MkDocs，并且需要设置 mkdocs.yml 文件中的 [`name`](https://www.mkdocs.org/user-guide/configuration/#name) 参数。如果是使用 `git` 方式安装的 Material MkDos 的话，这并不会起作用。

`overrides` 目录中的文件结构必须与原始主题保持一致，因为 overrides 目录中的任何文件都将替换原始主题同名文件。此外，也可以将其他资源文件放置在 overrides 目录中。

``` { .sh .no-copy }
.
├─ .icons/                             # Bundled icon sets
├─ assets/
│  ├─ images/                          # Images and icons
│  ├─ javascripts/                     # JavaScript files
│  └─ stylesheets/                     # Style sheets
├─ partials/
│  ├─ integrations/                    # Third-party integrations
│  │  ├─ analytics/                    # Analytics integrations
│  │  └─ analytics.html                # Analytics setup
│  ├─ languages/                       # Translation languages
│  ├─ actions.html                     # Actions
│  ├─ alternate.html                   # Site language selector
│  ├─ comments.html                    # Comment system (empty by default)
│  ├─ consent.html                     # Consent
│  ├─ content.html                     # Page content
│  ├─ copyright.html                   # Copyright and theme information
│  ├─ feedback.html                    # Was this page helpful?
│  ├─ footer.html                      # Footer bar
│  ├─ header.html                      # Header bar
│  ├─ icons.html                       # Custom icons
│  ├─ language.html                    # Translation setup
│  ├─ logo.html                        # Logo in header and sidebar
│  ├─ nav.html                         # Main navigation
│  ├─ nav-item.html                    # Main navigation item
│  ├─ pagination.html                  # Pagination (used for blog)
│  ├─ palette.html                     # Color palette toggle
│  ├─ post.html                        # Blog post excerpt
│  ├─ progress.html                    # Progress indicator
│  ├─ search.html                      # Search interface
│  ├─ social.html                      # Social links
│  ├─ source.html                      # Repository information
│  ├─ source-file.html                 # Source file information
│  ├─ tabs.html                        # Tabs navigation
│  ├─ tabs-item.html                   # Tabs navigation item
│  ├─ tags.html                        # Tags
│  ├─ toc.html                         # Table of contents
│  ├─ toc-item.html                    # Table of contents item
│  └─ top.html                         # Back-to-top button
├─ 404.html                            # 404 error page
├─ base.html                           # Base template
├─ blog.html                           # Blog index page
├─ blog-archive.html                   # Blog archive index page
├─ blog-category.html                  # Blog category index page
├─ blog-post.html                      # Blog post page
└─ main.html                           # Default page
```

### 覆写部分内容

如果想要覆盖部分内容，可以在 overrides 目录中使用相同名称的文件。例如，想要替换原始的 footer.html，那么就在`overrides/partials` 目录中创建一个 footer.html 文件即可：

```{ .sh .no-copy }
.
├─ overrides/
│  └─ partials/
│     └─ footer.html
└─ mkdocs.yml
```

MkDocs 在渲染主题时将使用新的局部文件，当然，任何文件都可以采用这种方式。

### 覆写模板 block { #overriding-blocks data-toc-label="Overriding blocks" }

除了覆盖部分内容，我们还可以覆盖（和扩展）模板内部定义或者特定功能封装的模板块(block)。如果想要覆盖一个`block`，需要 overrides 目录中创建一个 main.html 文件：

```{ .sh .no-copy }
.
├─ overrides/
│  └─ main.html
└─ mkdocs.yml
```

举例说明，如果要覆写页面的标题，那么需要在 main.html 中添加以下内容：

```jinja linenums="1"
{% extends "base.html" %}

{% block htmltitle %}
  <title>Lorem ipsum dolor sit amet</title>
{% endblock %}
```

如果你想要在 `block` 中 ** 添加 ** 一些内容而不是直接替换它，那么我们可以通过在 block 中添加 `{{ super() }}` 来包含原始内容，这对于第三方脚本尤其有用。

```django linenums="1" hl_lines="5"
{% extends "base.html" %}

{% block scripts %}
  <!-- Add scripts that need to run before here -->
  {{ super() }}
  <!-- Add scripts that need to run afterwards here -->
{% endblock %}
```

以下是 Material MkDocs 提供一些 block:

| Block 名称        | 描述                                         |
| :---------------- | :---------------------------------------------- |
| `analytics`       | Wraps the Google Analytics integration          |
| `announce`        | Wraps the announcement bar                      |
| `config`          | Wraps the JavaScript application config         |
| `container`       | Wraps the main content container                |
| `content`         | Wraps the main content                          |
| `extrahead`       | Empty block to add custom meta tags             |
| `fonts`           | Wraps the font definitions                      |
| `footer`          | Wraps the footer with navigation and copyright  |
| `header`          | Wraps the fixed header bar                      |
| `hero`            | Wraps the hero teaser (if available)            |
| `htmltitle`       | Wraps the `<title>` tag                         |
| `libs`            | Wraps the JavaScript libraries (header)         |
| `outdated`        | Wraps the version warning                       |
| `scripts`         | Wraps the JavaScript application (footer)       |
| `site_meta`       | Wraps the meta tags in the document head        |
| `site_nav`        | Wraps the site navigation and table of contents |
| `styles`          | Wraps the style sheets (also extra sources)     |
| `tabs`            | Wraps the tabs navigation (if available)        |

## 主题开发

Material MkDocs 使用 [TypeScript]、[RxJS] 和 [SASS] 等技术，通过[自定义构建][^1]流程进行组合。如果想要进行更彻底的更改，那么需要在主题源码中进行调整并重新进行编译。

[^1]:
    在 :material-tag:{ style="color: lightblue" } 7.0.0 版本前，Material MkDocs 是使用 WebPack 进行构建的，由于加载器和插件的不兼容，导致构建偶尔会出现中断。因此，我们决定将 WebPack 替换成基于 [RxJS] 的更精简的解决方案，这样可以精简掉项目的 500 多个依赖项（减少约 30%）。

  [TypeScript]: https://www.typescriptlang.org/
  [RxJS]: https://github.com/ReactiveX/rxjs
  [SASS]: https://sass-lang.com

### 环境搭建

首先，克隆我们想要使用的版本仓库。值得注意的是，如果是想要克隆内部版本，那么首先需要成为赞助商后，才能获得访问权限。


=== "Material MkDocs"

    ```sh
    git clone https://github.com/squidfunk/mkdocs-material
    cd mkdocs-material
    ```

=== "内部版本"

    我们需要首先获取 Github 访问令牌，并设置 `$GH_TOKEN` 环境变量。

    ```sh
    git clone https://${GH_TOKEN}@github.com/squidfunk/mkdocs-material-insiders.git # (1)!
    ```

    1.  如果你使用的 SSH 密钥来授权 Github，可以使用如下目录来克隆内部版本：

        ```sh
        git clone git@github.com:squidfunk/mkdocs-material-insiders.git
        ```

接下来，创建一个 Python 虚拟环境并激活它:

```sh
python -m venv venv
source venv/bin/activate
```


!!! note "确保 `pip` 在虚拟环境中可以正常运行"

    如果你已经设置了 `PIP_REQUIRE_VIRTUALENV` 环境变量为 `true` 的话，那么 `pip` 会拒绝虚拟环境外的任何安装。忘记激活虚拟环境可能会非常烦人，因为它会随着时间的推移在虚拟环境之外安装各种东西，可能会导致进一步的错误。因此，我们最好是将其添加到 `.bashrc` 或 `.zshrc` 文件中，并重新启动 Shell：

    ```shell
    export PIP_REQUIRE_VIRTUALENV=true
    ```

    当然，除了使用 `venv` 外，你也可以使用 `conda`、`pyenv`、`pipenv` 这些虚拟环境构建工具。

接下来，安装 Python 依赖:

