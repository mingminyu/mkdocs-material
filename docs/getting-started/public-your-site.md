# 发布网站

将网站托管在 Git 库中的最大好处是能够在推送更改时能自动部署它，MkDocs使得这一操作更加简单。

## 1. Github Pages :material-github:

如果已经在 GitHub 上托管代码，那么使用 GitHub Pages 来发布网站那是再方便不过了。


### 1.1 使用 Github Actions

使用 GitHub Actions 可以自动部署网站，我们需要在库的根目录下新建一个 GitHub Actions Workflow，例如 `.github/workflows/ci.yml`，并粘贴入以下内容：
 
=== "Material for MkDocs"

    ```yaml linenums="1"
    name: ci # (1)!
    on:
      push:
        branches:
          - master # (2)!
          - main
    permissions:
      contents: write
    jobs:
      deploy:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v4
          - name: Configure Git Credentials
            run: | # (3)!
              git config user.name github-actions[bot]
              git config user.email 41898282+github-actions[bot]@users.noreply.github.com
          - uses: actions/setup-python@v5
            with:
              python-version: 3.x
          - run: echo "cache_id=$(date --utc '+%V')" >> $GITHUB_ENV # (4)!
          - uses: actions/cache@v4
            with:
              key: mkdocs-material-${{ env.cache_id }}
              path: .cache
              restore-keys: |
                mkdocs-material-
          - run: pip install mkdocs-material # (5)!
          - run: mkdocs gh-deploy --force
    ```

    1.  你可以更改为自己喜欢的 Workflow 名称。
    2.  Github 新的提交刚推送到 `master` 或者 `main` 分支。如果你的项目默认是 `master` 分支的话，那么可以将 `main` 移除掉。
    3.  !!! warning "注意"
        这里需要将 `user.name` 和 `user.email` 替换成你自己的 Github 信息。
    4.  保存 `cache_id` 环境变量的目的在于后续通过 `key` 参数来访问变量值。需要注意的是，这里的名称是区分大小写的，所以确保内容为 `${{ env.cache_id }}`。
        - `--utc` 选项是为了确保每一个 Workflow Runner 使用想用的时区。
        - `%V` 为格式化参数，用于设置每周更新一次缓存。
        - 当然，你也可以使用 `%F` 参数来设置每天更新缓存。

        如果你对 `date` 命令的其他参数感兴趣，可以自行 Bing 搜索 Linux Shell `date` 命令。
    5.  使用 `pip` 命令来安装 MkDocs 的插件:

        ``` sh
        pip install mkdocs-material \
            mkdocs-awesome-pages-plugin
        ```

=== "内部版本"

    ```yaml linenums="1"
    name: ci
    on:
      push:
        branches:
          - master
          - main
    permissions:
      contents: write
    jobs:
      deploy:
        runs-on: ubuntu-latest
        if: github.event.repository.fork == false
        steps:
          - uses: actions/checkout@v4
          - name: Configure Git Credentials
            run: |
              git config user.name github-actions[bot]
              git config user.email 41898282+github-actions[bot]@users.noreply.github.com
          - uses: actions/setup-python@v5
            with:
              python-version: 3.x
          - run: echo "cache_id=$(date --utc '+%V')" >> $GITHUB_ENV
          - uses: actions/cache@v4
            with:
              key: mkdocs-material-${{ env.cache_id }}
              path: .cache
              restore-keys: |
                mkdocs-material-
          - run: apt-get install pngquant # (1)!
          - run: pip install git+https://${GH_TOKEN}@github.com/squidfunk/mkdocs-material-insiders.git
          - run: mkdocs gh-deploy --force
    env:
      GH_TOKEN: ${{ secrets.GH_TOKEN }} # (2)!
    ```

    1.  如果你想要使用 `optimize` 插件来自动压缩图片的话，那么这一步是必须的。
    2.  在部署内部版本 Material MkDocs 时，记得在 【Github --> Security --> Secrets and variables】中设置 `GH_TOKEN` 环境变量。

现在，当新的更改被提交到 `master` 或者 `main` 分支后，静态网站将自动构建和部署，我们可以在仓库中的 Actions 查看具体 Workflow 运行情况和日志。

### 1.2 使用 MkDocs

如果你更喜欢手动部署项目，你可以在根目录下执行以下命令:

```sh
mkdocs gh-deploy --force
```

执行上述操作后 Github 会编译文档并将它部署到 `gh-pages` 分支上。查看 [MkDocs 总览](https://www.mkdocs.org/user-guide/deploying-your-docs/#project-pages) 来获取更多信息，如果想要查看命令行参数，可以查阅[命令行](https://www.mkdocs.org/user-guide/cli/#mkdocs-gh-deploy)文档。

!!! warning "注意"
    
    这里我们还差一步，我们需要项目仓库的 Settings --> Pages 进行如下设置：
    
      1. Build and deployment --> Deploy from a branch
      2. Brach => gh-pages
      3. Custm domain --> 你的域名


### 1.3 使用自定义域名

如果你想使用自己的域名，那么需要在 `docs` 文件下添加一个 CNAME 文件，并填充域名信息，示例如下:

```python title="CNAME"
www.zerooai.cn
```


## 2. Github Pages :material-gitlab:

如果你是将代码托管在 GitLab 上，那么也可以使用 GitLab CI 来部署 GitLab Pages。在仓库的根目录中，创建一个名为 `.gitlab-ci.yml` 文件，然后添加以下内容：

=== "Material for MkDocs"

    ```yaml linenums="1"
    pages:
      stage: deploy
      image: python:latest
      script:
        - pip install mkdocs-material
        - mkdocs build --site-dir public
      artifacts:
        paths:
          - public
      rules:
        - if: '$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH'
    ```

=== "内部版本"

    ```yaml linenums="1"
    pages:
      stage: deploy
      image: python:latest
      script: # (1)!
        - pip install git+https://${GH_TOKEN}@github.com/squidfunk/mkdocs-material-insiders.git
        - mkdocs build --site-dir public
      artifacts:
        paths:
          - public
      rules:
        - if: '$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH'
    ```

    1.  在部署内部记得设置好 `GH_TOKEN` 环境变量。

现在，当新的更改被提交到 `master` 或者 `main` 分支后，静态网站将自动构建和部署，我们可以在仓库中的 Actions 查看具体 Workflow 运行情况和日志。

我们的网站访问入口为 `<username>.gitlab.io/<repository>`。

## 3. 其他


由于我们无法覆盖所有可能的平台，我们依赖社区贡献的指南来介绍如何将 Material for MkDocs 构建的网站部署到
其他平台上：

<div class="grid cards" markdown>

- [:simple-azuredevops: Azure][Azure]
- [:simple-cloudflarepages: Cloudflare Pages][Cloudflare Pages]
- [:simple-digitalocean: DigitalOcean][DigitalOcean]
- [:material-airballoon-outline: Fly.io][Flyio]
- [:simple-netlify: Netlify][Netlify]
- [:simple-vercel: Vercel][Vercel]
- [:simple-codeberg: Codeberg Pages][Codeberg Pages]

</div>

  [GitLab Pages]: https://gitlab.com/pages
  [GitLab CI]: https://docs.gitlab.com/ee/ci/
  [masked custom variables]: https://docs.gitlab.com/ee/ci/variables/#create-a-custom-variable-in-the-ui
  [default branch]: https://docs.gitlab.com/ee/user/project/repository/branches/default.html
  [Azure]: https://bawmedical.co.uk/t/publishing-a-material-for-mkdocs-site-to-azure-with-automatic-branch-pr-preview-deployments/763
  [Cloudflare Pages]: https://www.starfallprojects.co.uk/projects/deploy-host-docs/deploy-mkdocs-material-cloudflare/
  [DigitalOcean]: https://www.starfallprojects.co.uk/projects/deploy-host-docs/deploy-mkdocs-material-digitalocean-app-platform/
  [Flyio]: https://documentation.breadnet.co.uk/cloud/fly/mkdocs-on-fly/
  [Netlify]: https://www.starfallprojects.co.uk/projects/deploy-host-docs/deploy-mkdocs-material-netlify/
  [Vercel]: https://www.starfallprojects.co.uk/projects/deploy-host-docs/deploy-mkdocs-material-vercel/
  [Codeberg Pages]: https://andre601.ch/blog/2023/11-05-using-codeberg-pages/
