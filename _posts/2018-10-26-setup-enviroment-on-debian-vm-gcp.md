---
layout: post
title:  TIL - Cài đặt environment trên máy ảo Debian của Google cloud
date:   2018-10-26
mathjax: true
comments: true
description: Cài đặt environment trên máy ảo Debian của Google cloud
img: short-post-images/nvidia-pain.png
---

# Sơ qua về câu chuyện

Mình được giao nhiệm vụ training model trên con VM instance thuộc Google Cloud Platform (GCP) của khách hàng. Đây là lần đầu tiên mình có dịp sử dụng GCP, nên mình háo hức vô cùng. Mọi chuyện dần trở nên vcd khi mình cài mãi mà tensorflow vẫn báo lỗi khi import...

# Cách cài cắm

Sau khi tham khảo khá nhiều hướng dẫn cài trên mạng, mình đã phải tự mò ra rằng, lý do lớn nhất khiến việc cài đặt trở nên khó khăn là vì Debian OS thiếu mất 1 vài packages sẵn có.

Cứ cài đặt các gói qua `sudo apt-get install`, rồi truy ngược tới những packages còn thiếu là ok.

Có 1 điều cần chú ý là cần cải đủ: NVIDIA drivers (hãy download bằng cách chọn chính xác GPU, đừng lấy link down trên mạng), cuda và cudnn là mọi chuyện sẽ xong.

## Tổng kết lại

Thực ra không nhiều người gặp vấn đề như mình, hình như còn có thể chọn cả gói cài sẵn khi lập máy ảo nữa (?!). Dù sao, hãy nhớ chọn OS VM là Ubuntu thay vì cái của nợ Debian này.

Do không chụp ảnh nên viết ngắn gọn kiểu TIL thoai, heheeeee.