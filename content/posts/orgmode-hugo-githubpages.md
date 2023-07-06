+++
title = "Setup Blog Using Org Mode and Publish on github pages"
author = ["hsun"]
tags = ["hugo", "emacs", "githubpages"]
draft = false
+++

## Install **hugo** {#install-hugo}

<https://gohugo.io/installation/>


## Configurate **ox-hugo** in emacs config's file {#configurate-ox-hugo-in-emacs-config-s-file}

<https://ox-hugo.scripter.co/doc/installation/>

```emacs-lisp
(use-package ox-hugo
  :ensure t   ;Auto-install the package from Melpa
  :pin melpa  ;`package-archives' should already have ("melpa" . "https://melpa.org/packages/")
  :after ox)

(with-eval-after-load 'ox
  (require 'ox-hugo))
```


## Use hugo to generate the scaffold {#use-hugo-to-generate-the-scaffold}


### 1. Go to home directory and get the blog scaffold {#1-dot-go-to-home-directory-and-get-the-blog-scaffold}

```shell
cd && hugo new site blog && cd blog
```


### 2. Use git submodule to get the theme **PaperMod** {#2-dot-use-git-submodule-to-get-the-theme-papermod}

```shell
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod themes/PaperMod
echo 'theme = "PaperMod"' >> config.toml
```


### 3. Append these lines to the site config {#3-dot-append-these-lines-to-the-site-config}

```shell
[markup.goldmark.renderer]
  unsafe = true
```


## Write the org file {#write-the-org-file}


### 1. Setup the Hot-Reload {#1-dot-setup-the-hot-reload}

Live listen the content

```shell
mkdir content-org
hugo server -D --navigateToChanged
```

Then Open the url
<http://localhost:1313/>


### 2. Add \`#+hugo_base_dir: ../\` to tell ox-hugo the base dir {#2-dot-add-plus-hugo-base-dir-dot-dot-to-tell-ox-hugo-the-base-dir}

```org
#+hugo_base_dir: ../

\* My first post                                             :tag1:@category1:
:PROPERTIES:
:EXPORT_FILE_NAME: my-first-post
:END:
This is my post body
```


### 3. Take the heading to be **DONE,** Shift + left {#3-dot-take-the-heading-to-be-done-shift-plus-left}


### 4. Tangle the file {#4-dot-tangle-the-file}

\`C-c C-e H H\`


## Host on github {#host-on-github}

<https://gohugo.io/hosting-and-deployment/hosting-on-github/>


### create github repo {#create-github-repo}


### add the workflow actions {#add-the-workflow-actions}

<https://github.com/peaceiris/actions-hugo>

```yaml
name: GitHub Pages

on:
  push:
    branches:
      - main  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-22.04
    concurrency:
      group: ${{ github.workflow }}-${{ github.ref }}
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.110.0'
          # extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```


### Set `gh-pages` as publish branch and `root` as publish directory in github  \`-&gt;\` settings \`-&gt;\` pages {#set-gh-pages-as-publish-branch-and-root-as-publish-directory-in-github-settings-pages}


### <span class="org-todo todo TODO">TODO</span> Configuring a custom domain(Optional), Doesn't work {#configuring-a-custom-domain--optional--doesn-t-work}

<https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site>


#### 1. supported custom domains {#1-dot-supported-custom-domains}

-   Custom domain : www.example.com  blog.example.com
-   Apex domain: example.com


## reference {#reference}

<https://ox-hugo.scripter.co/>
