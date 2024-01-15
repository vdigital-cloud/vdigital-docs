---
layout: default
title: Setup page in local
nav_order: 2
---

# Setup page in local
{: .no_toc}

## table of content
{: .no_toc .text-delta}

1. TOC
{:toc}

---

In these page, we have setup running this page in local.

## Install ruby

In the page, we have downloaded Ruby from the in the [website](https://rubyinstaller.org/downloads/). And install in the [link](https://www.geekbits.io/how-to-install-ruby-on-windows-11/)

You should download Ruby with Devkit, because if you download Ruby without Devkit it will have issue use command `bundler install.
{: .note }

## Using gem install library

You should use powershell run as administrator. And run command in folder source after pull to git.
{: .warning }

After downloading and install Ruby, we have used gem install bundler (Bundler is a gem that manages gem dependencies).

```bash
gem install bundler
```

And using bundle install library or dependency in the gem file.

```bash
bundle install
```

After running the above command, the result will be as shown in the image below.

![](../../assets/image/bundle-install.png)

## Running website in local

In this module, we using command:

```bash
bundle exec jekyll serve
```

After running the above command, the result will be as shown in the image below.

![](../../assets/image/web-running-local.png)