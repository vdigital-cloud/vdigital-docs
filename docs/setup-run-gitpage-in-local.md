---
layout: default
title: Chạy trang này dưới local
nav_order: 2
---

# Chạy trang này dưới local
{: .no_toc}

## Mục Lục
{: .no_toc .text-delta}

1. TOC
{:toc}

---

Trong trang này, chúng tôi sẽ hướng dẫn chạy website này dưới local. Mục đích của việc này là giúp bạn có thể xem nhanh các nội dung bạn đã thêm hoặc sửa trước khi đẩy lên git.

## Cài Đặt ruby

Đầu tiên chúng ta sẽ tải bộ cài Ruby trong đường dẫn này [website](https://rubyinstaller.org/downloads/). và cách cài đặt tại link này [link](https://www.geekbits.io/how-to-install-ruby-on-windows-11/)

Bạn nên tải Ruby với Devkit, tại vì nếu bạn tải Ruby mà không kèm với Devkit nó sẽ bị lỗi khi sử dụng lệnh `bundler install`.
{: .note }

## Sử dụng Gem để cài đặt thư viện 

Bạn cần mở powershell với quyền Administrator. Và chạy lệnh trong thư mục vừa pull từ git về.
{: .warning }

Sau khi tải và cài đặt Ruby, sử dụng gem để install Bundler (Bundler là một công cụ quản lý các dependencies của dự án Ruby).

```bash
gem install bundler
```

Và sử dụng bundle để cài đặt thư viện và dependency trong file gem.

```bash
bundle install
```

Sau khi chạy lệnh ở trên, kết quả sẽ như ảnh bên dưới.

![](../../assets/image/bundle-install.png)

## Khởi chạy website ở dưới local

Trong phần này sẽ chạy câu lệnh như bên dưới để phát web ở dưới local bằng port 4000

```bash
bundle exec jekyll serve
```

Sau khi chạy câu lệnh ở trên, kết quả sẽ được như hình bên dưới.

![](../../assets/image/web-running-local.png)