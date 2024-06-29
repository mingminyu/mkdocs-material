# 新建网站

在完成 Material for MkDocs 的安装后，可以使用 `mkdocs` 相关命令来启动文档。转到要放置项目的目录，然后在终端中输入:

```sh
mkdocs new .
```

如果你正在使用的是 Docker 中 的Material for MkDocs，则使用以下命令:

=== "Unix, Powershell"

    ```sh
    docker run --rm -it -v ${PWD}:/docs squidfunk/mkdocs-material new .
    ```

=== "Windows (cmd)"

    ```sh
    docker run --rm -it -v "%cd%":/docs squidfunk/mkdocs-material new .
    ```

以上操作会新建以下结构的文件：

```{ .sh .no-copy }
.
├─ docs/
│  └─ index.md
└─ mkdocs.yml
```


## 配置

### 最小配置

只需要简单的添加以下几行内容到 mkdocs.yml 即可启用主题。请注意，由于有几种不同的安装方法，因此配置可能会略有不同:

``` yaml hl_lines="2-5" linenums="1"
site_name: My site
site_url: https://mydomain.org/mysite
theme:
  name: material
```

`site_url` 设置之所以这么重要，原因有很多。默认情况下，MkDocs 会假设您的网站托管在您的域名下。当然，这不包括不使用自定义域名时将网站发布到 GitHub Pages 上。另一个原因则是插件需要设置 `site_url`，所以您应该始终这样做。

???+ tip "推荐: 配置验证和自动补全"

    为了最大限度地提高生产力，Material MkDocs 为配置文件 mkdocs.yml 提供了 [Schema JSON] 文件。如果您的编辑器支持 YAML 模式验证，绝对建议设置它：

    === "VS Code"

        1.  安装 `vscode-yaml` 插件来提供 YAML 语言支持
        2.  在工作区的 setting.json 文件中添加如下内容:

            ```json
            {
              "yaml.schemas": {
                "https://squidfunk.github.io/mkdocs-material/schema.json": "mkdocs.yml"
              },
              "yaml.customTags": [ // (1)!
                "!ENV scalar",
                "!ENV sequence",
                "!relative scalar",
                "tag:yaml.org,2002:python/name:material.extensions.emoji.to_svg",
                "tag:yaml.org,2002:python/name:material.extensions.emoji.twemoji",
                "tag:yaml.org,2002:python/name:pymdownx.superfences.fence_code_format"
              ]
            }
            ```

            1.  如果你打算使用 icons 和 emojis 的话，那么这一步配置是必须的，否则 VS Code 会出现报错。

    === "其他"

        3.  确保你的编辑器支持 YAML Schema 验证
        4.  在 `mkdocs.yml` 顶部添加如下内容:

            ``` yaml
            # yaml-language-server: $schema=https://squidfunk.github.io/mkdocs-material/schema.json
            ```


[Schema JSON]: https://github.com/squidfunk/mkdocs-material/blob/master/docs/schema.json

### 高级配置

Material MkDocs 还有很多其他配置项（具体如下列表所示），你可以在 **设置** 中进行查看相关文档:

<div class="grid cards" markdown>

- 更改颜色
- 更改字体
- 更改语言
- 更改 LOGO 和图标
- 确保数据隐私
- 设置导航
- 设置站内搜索
- 设置站内分析
- 设置社交卡片
- 设置博客
- 设置标签
- 设置头部
- 设置脚注
- 添加 Git 仓库
- 添加评论系统
- 构建离线使用
- 优化网站
- Markdown 扩展

</div>

## 模板

如果你想要跳过创建一个新项目的话，可以使用克隆模板项目来开始构建你的网站。


<div class="grid cards" markdown>

-   :octicons-repo-template-24: &nbsp; __[Blog][blog-template]__

    ---

    创建一个博客

-   :octicons-repo-template-24: &nbsp; __[Social cards][social-cards-template]__

    ---

    使用社交卡片来创建一个文档

</div>

[blog-template]: https://github.com/mkdocs-material/create-blog
[social-cards-template]: https://github.com/mkdocs-material/create-social-cards


## 预览

MkDocs 包含一个试试预览的服务，所有可以在撰写文档的过程中进行实时预览。当文档修改保存后，这个服务会自动重建整个网站的文档。使用以下命令启动：


```sh
mkdocs serve # (1)!
```

1.  如果你有一个庞大的文档项目，MkDocs 可能会花费比较长时间重新构建生成预览。
    很多情况下，我们仅仅是对当前页面的内容是否正确渲染感兴趣，那么就可以使用
    `--dirtyreload` 参数来加快页面重构:

    ```sh
    mkdocs serve --dirtyreload
    ```

如果你是以 Docker 的方式使用 Material MkDocs，那么请使用:

=== "Unix, Powershell"

    ```sh
    docker run --rm -it -p 8000:8000 -v ${PWD}:/docs squidfunk/mkdocs-material
    ```

=== "Windows"

    ```sh
    docker run --rm -it -p 8000:8000 -v "%cd%":/docs squidfunk/mkdocs-material
    ```


成功启动服务后，我们就可以访问 https://localhost:8000 来查看页面内容了。


## 构建网站

当文档编辑完成后，可以通过以下命令将所有的 Markdown 文件生成一个静态网站：


```sh
mkdocs build
```

如果你是以 Docker 的方式使用 Material MkDocs，那么请使用:

=== "Unix, Powershell"

    ```sh
    docker run --rm -it -v ${PWD}:/docs squidfunk/mkdocs-material build
    ```

=== "Windows"

    ```sh
    docker run --rm -it -v "%cd%":/docs squidfunk/mkdocs-material build
    ```

我们并不需要设置数据库或者服务器，因为 Material MkDocs 是自包含的，我们可以将其发布到 Github Pages 或者 Gitlab Pages 上。

如果你打算将文档放在本地而不是 WEB 服务器上，可以阅读下 Material MkDocs 的离线使用。
