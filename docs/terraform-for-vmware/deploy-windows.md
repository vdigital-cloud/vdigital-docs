---
layout: default
title: Tạo máy ảo Windows trên vCenter
parent: Terraform và vmware
nav_order: 6
---

# Tạo template
{: .no_toc }

## Mục lục
{: .no_toc .text-delta }

1. TOC
{:toc}

Nếu bạn không có source của bộ Terraform dưới, nhấn vào link này và pull về máy [GitHub link](https://github.com/aquynh1682/terraform-create-vmware).

## Khai báo cấu hình cho terraform bằng cách khai biến trong file json

Trong file Json cấu hình cho Windows. Nó có 11 cặp khoá key và values cho máy ảo Windows. Nó bao gồm key và values như ở dưới đây.

{: .note}
> Bạn có thể tạo nhiều máy ảo một lúc trong vCenter hãy thêm các object trong JSON File cấu hình.
> <div markdown="block">
> {: .warning }
>> Nếu thêm các máy ảo khác trong JSON File, bạn phải thêm dấu `,` sau một object cũ và trước một object mới.
> </div>

|        Key    |    Value        |
|:--------------|:----------------|
|`name_vm`      |Tên của máy ảo ở trong vcenter |
|`memory`       |Số lượng memory được cấp cho VM ví dụ: 4Gb -> 4096 |
|`cpu`          |Số lượng CPU được cấp cho VM |
|`ip_address`   |Địa chỉ IP được gắn cho VM |
|`subnet`       |Subnet mark gắn cho VM ví dụ: 255.255.255.0 -> 24|
|`gateway`      |Gateway gắn cho VM|
|`host_name`    |Hostname cho OS|
|`domain`       |Domain của OS, nó có giá trị mặc định là `default`|
|`admin_pass`       |Domain của OS, nó có giá trị mặc định là `default`|
|`dns1`          |DNS của VM ví dụ: 8.8.8.8|
|`dns2`          |DNS của VM ví dụ: 8.8.4.4 hoặc là nhập thêm một dns phụ khác|

Sau khi điền các thông tin cấu hình vào file Json, và ảnh sẽ như bên dưới.

![](https://vdigital-cloud.github.io/vdigital-docs/assets/image/config-json-windows.png)

## Cấu hình biến trong file variables.tf

Tại phần này sẽ cấu hình biến Terrafrom, bạn có thể xem nội dung của file cấu hình đó như dưới này.

```tf
locals {
    ########    for create windows    ####################
    vm_configs = jsondecode(file("./host_windows.json"))
}

variable "vsphere_server" { # host/domain of vmware vsphere client
    type = string
    default = ""
}

variable "esxi_host" { # vmware esxi name
    type = string
    default = ""
}

variable "vsphere_dataCenter" { # name datacenter in vsphere
    type = string
    default = ""
}

variable "vsphere_dataStore" { # name datastore in vsphere
    type = string
    default = "Local Data"
}

variable "vsphere_network" { # name network in vsphere
    type = string
    default = "MNG"
}

variable "vsphere_template" { # name template in vsphere
    type = string
    default = "template-windows-terraform"
}

variable "vsphere_user" { # user login vmware vsphere client
    type = string
    default = "administrator@vsphere.local"
}

variable "vsphere_password" { # password login vmware vsphere client
    type = string
    default = ""
}
```

Trong cấu hình các biến ở trên, chúng ta đã xem nội dung của nó rồi. Tiếp đến là phần giải thích từng biến của file bên trên.

|        Biến    |    Giải thích        |
|:--------------|:----------------|
|`vsphere_server`      |Domain hoặc ip để truy cập vào vsphere|
|`esxi_host`          |Domain hoặc ip để truy cập vào esxi|
|`vsphere_dataCenter`       |Tên của Datacenter trong vsphere|
|`vsphere_dataStore`   |Tên của ổ cứng trong esxi|
|`vsphere_network`       |Chọn card mạng cho VM|
|`vsphere_template`      |Sẽ tạo VM theo mẫu của template OS windows|
|`vsphere_user`         |User đăng nhập vsphere|
|`vsphere_password`       |Password đăng nhập vsphere|

{: .note }
Bạn có thể điền các giá trị vào trong mục default.

## Triển khai máy ảo trên vCenter bằng terraform

{: .note }
trước khi chạy terraform, bạn trên truy cập vào file main.tf kiểm tra lại lần nữa xem là cấu hình chạy cho windows có được gỡ comment chưa và đã comment cấu hình chạy cho linux chưa. Nếu đã xác nhận xong thì sang bước tiếp theo.

Sau khi cấu hình biến, bạn có thể chạy lệnh `terraform plan` để xác nhận cấu hình có đúng không. Sau khi kiểm tra xong, bạn có thể chạy lệnh `terraform apply` và nhập `yes`. Sau khi đợi vài phút máy ảo sẽ được triển khai.

{: .warning}
Sau khi chạy lệnh `terraform apply`, terraform sẽ tạo ra hai file là terraform.tfstate và terraform.tfstate.backup. Cả 2 file này có lưu lại cấu hình chạy trước đó. Nếu bạn không xoá hai file này, khi chạy lại `terraform apply` nó sẽ xoá hết cấu hình tài nguyên cũ đã tạo, và tạo lại tài nguyên cũ.