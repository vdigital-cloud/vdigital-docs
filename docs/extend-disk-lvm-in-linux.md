---
layout: default
title: Mở rộng ổ cứng lvm trong linux
nav_order: 5
---

# Mở rộng ổ cứng lvm trong linux
{: .no_toc}

## Mục lục
{: .no_toc .text-delta}

1. TOC
{:toc}

---

## Giới thiệu

LVM (Logical Volume Management) là một công nghệ giúp quản lý các thiết bị lưu trữ dư liệu trên các hệ điều hành linux. Công nghệ này cho phép người dùng gom nhóm các ổ cứng vật lý lại và phân tách chúng thành những phân vùng nhỏ hơn, dễ dàng mở rộng các phân vùng này khi cần thiết.

Một số ưu điểm của LVM:
- Quản lý một lượng lớn ổ đĩa một cách dễ dàn..
- Điều chỉnh phân vùng ổ cứng một cách linh động.
- Backup hệ thống bằng cách snapshot các phân vùng ổ cứng (real-time).
- Migrate dữ liệu dễ dàng.

## Mô hình hoạt động

Một số khái niệm cần nắm rõ khi tiếp xúc với LVM:
- **Physical Volume – PV:** Ổ cứng vật lý từ hệ thống (đĩa cứng, partition, ISCSI LUN, SSD...) là đơn vị cơ bản để LVM dùng để khởi tạo các Volume Group. Trên mỗi một PV sẽ được khoảng 1MB header ghi dữ liệu về các phân bố của Volume Group chứa nó. Header này sẽ hỗ trợ rất nhiều trong việc phục hồi dữ liệu khi có sự cố xảy ra.
- **Volume Group – VG:** Là tập hợp các ổ cứng vật lý (PV) thành một kho lưu trữ chung với tổng dung lượng của các ổ đĩa con. Mỗi khi ta thêm một PV vào VG, LVM sẽ tự động chia dung lượng trên PV thành nhiều Physical Extent với kích cỡ bằng nhau. Và từ VG, ta có thể tạo nhiều Logical Volume và dễ dàng chỉnh sửa dung lượng của chúng.

![](https://vdigital-cloud.github.io/vdigital-docs/assets/image/volume-group-vg.png)
{: .mb-lg-4 }

- **Logical Volume – LV:** Là các phân vùng luận lý dược tạo từ VG. Logical Volume tương tự như các partition trên ổ cứng bình thường nhưng linh hoạt hơn vì kích thước của LV có thể dễ dàng thay đổi theo thời gian thực mà không lo làm gián đoạn hệ thống. Sở dĩ ta có thể dễ dàng thay đổi được kích thước của LV vì LV được chia thành nhiều Logical Extent, mỗi Logical Extent này được mapping tương ứng với 1 Physical Extent trên các ổ đĩa.

![](https://vdigital-cloud.github.io/vdigital-docs/assets/image/logical-volume-lv.png)

- **Extent:** Extent là đơn vị nhỏ nhất của VG. Mỗi một volume được tạo ra từ VG chứa nhiều extent nhỏ với kích thước cố định bằng nhau. Các extent trên LV không nhất thiết phải nằm liên tục với nhau trên ổ cứng vật lý bên dưới mà có thể nằm rải rác trên nhiều ổ cứng khác nhau. Extent chính là nền tảng cho công nghệ LVM, các LV có thể được mở rộng hay thu nhỏ lại bằng cách adđ thêm các extent hoặc lấy bớt các extent từ volume này.

Tóm lại, với công nghệ LVM ta có thể gộp nhiều ổ cứng vật lý Physical Volume lại thành Volume Group để tổng hợp toàn bộ tài nguyên lưu trữ cần thiết. Sau đó, người quản trị có thể chia nhỏ Volume Group ra thành các Logical Volume một cách tuỳ ý và linh hoạt. Mỗi một Logical Volume gồm nhiều extent, khi cần mở rộng Logical Volume thì ta thêm một số extent, khi cần thu nhỏ thì ta lấy lại một số extent.

**Ví dụ:** Theo hình ví dụ dưới dây, ta có một VG được tạo ra từ 3PV. Trên đó, ta tạo ra 3 LV có một LV trong số đó chạy trên 2 PV khác nhau:

![](https://vdigital-cloud.github.io/vdigital-docs/assets/image/lvm.png)

Một số tính năng cơ bản của LVM:
- Di chuyển LV giữa các PV khác nhau.
- Thay đổi kích thước của VG online bằng cách gắn thêm hoặc tháo bớt PV.
- Thay đổi kích thước của LV bằng cách thay đổi số lượng của extent của PV này.
- Tạo snapshot của LV (giữ nguyên toàn bộ trạng thái của LV vào thời điểm đó).

## LVM Thin provisioning

Thin Provisioning là tính năng cấp phát ổ cứng dựa trên sự linh hoạt của LVM. Giả sử ta có một **Volume Group**, ta sẽ tạo ra **1 Thin Pool** từ **VG** này với dung lượng là 20GB cho nhiều khách hàng sử dụng. Giải sử ta có 3 khách hàng, mỗi khách hàng được cấp 6GB lưu trữ. Như vậy ta có 3x 6GB là 18GB. Với kỹ thuật cấp phát truyền thống thì ta chỉ có thể cấp phát thêm 2GB cho khách hàng thứ 4.

![](https://vdigital-cloud.github.io/vdigital-docs/assets/image/what-is-thin-provisioning.png)

_So sánh giữa cấp phát truyền thống so với **Thin Provisioning**_

Nhưng với kỹ thuật Thin Provisioning ta vẫn có thể cấp thêm 6GB nữa cho khách hàng thứ 4. Tức là 4x 6GB = 24GB > 20GB lúc đầu. Sở dĩ ta có thể làm được như vậy là do mỗi user tuy được cấp 6GB nhưng thường thì họ sẽ không xài hết số dung lượng này (nếu 4 khách hàng đều xài hết thì ta sẽ gặp tình trạng Over Provisioning). Ta sẽ giả dụ là họ không xài hết dung lượng được cấp thì trên danh nghĩa mỗi người sẽ được 6GB, nhưng thực tế thì họ xài đến đâu, hệ thống sẽ cấp dung lượng đến đó.

Đối với cơ chế cấp phát bình thường thì LVM sẽ cấp phát 1 dãy block liên tục mỗi khi người dùng tạo ra 1 volume mới. Nhưng với cơ chế thin pool thì LVM chỉ sẽ cấp phát các block ổ cứng (là một tập hợp các con trỏ, trỏ tới ổ cứng) khi có dữ liệu thật sự ghi xuống đó. Cách tiếp cận này giúp tiết kiệm dung lượng cho hệ thống, tận dụng tối ưu dung lượng lưu trữ. Tuy nhiên, khuyết điểm là có thể gây phân mảnh hệ thống và gây ra tình trạng Over Provisioning như đã nói ở trên.

## Hướng dẫn mở rộng LVM

Như đã đề cập ở trên, thực chất việc mở rộng logical volume là thêm extent vào nó. Và để thêm extent, trong môi trường ảo hoá, ta có cách đó là mở rộng (resize) ổ cứng hoặc thêm một ổ cứng mới vào. Đối với server vật lý thì có cách là thêm ổ cứng mới.

Dưới đây là hướng dẫn thực hiện trường hợp thêm một ổ cứng mới.