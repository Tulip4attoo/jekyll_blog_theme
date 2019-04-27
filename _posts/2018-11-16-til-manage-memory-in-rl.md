---
layout:         post
title:          TIL - Làm việc với memory trong RL
date:           2018-11-16
mathjax:        true
comments:       true
description:    Vấn đề nên xử lý như nào với Memory khi train RL
author:         Tulip
---

# Sơ qua về câu chuyện

Mình có làm project tạo bot chơi game [Pong-v0](https://github.com/Tulip4attoo/rl/tree/master/f-class/pong-v0). Vấn đề đặt ra là mình có làm replay buffer, để agent có thể train với random memory từ trước nữa. Nhưng mà, trong paper [Human-level control through deep reinforcement learning](https://deepmind.com/research/publications/human-level-control-through-deep-reinforcement-learning/) của Deepmind (và cả cái tutorial mà mình học theo nữa), họ dùng memory size là 1 triệu. Hmm, theo tính toán, hiện tại với size 600, mình tốn 200MB RAM, tức là để đạt memory size là 1 triệu, mình cần khoảng 300GB RAM. Hmmm.

Note lại là trong bài [RL hard](https://tulip4attoo.github.io/blog/tir-rl-hard/), người ta toàn train mấy trăm triệu frames.

# Cách giải quyết

Có 2 cách giải quyết mình nghĩ tới:
+ điều chỉnh input khéo hơn:
    + giảm kích cỡ input về 40*40, cũng như điều chỉnh input cho memory khi load, cũng như có thể giảm stack xuống còn 3 --> sẽ x8 được memory size.
    + điều chỉnh type của image, cụ thể float64 --> np.float16, x4 memory size mà không cần điều chỉnh gì nhiều
+ lưu input ra máy và load. Thực ra nên nhớ là các model bt cũng đều load data từ máy lên đó thôi, vài trăm GB ảnh thì đâu có lưu trong RAM.


## Tổng kết lại

Đây là 1 vấn đề khó. Cách 1 là giải quyết tạm thời. Cách 2 là xử lý tốt cho sau này, tuy nhiên mình mới đang làm thôi. Note là cách 2 có thể view tốt được data và đảm bảo reproduce kết quả.

## Update

Mình mới nhận ra là 8k * 8 * 4 ~ 240k.

240k là 1 con số ổn, có thể so sánh với 1mil. Như vậy, việc áp dụng cả 2 phương pháp ở cách 1 cũng có thể train được ở mức độ vừa phải. 

Ưu điểm ở đây là dễ code và máy yếu (4GB RAM) vẫn có thể phà phà chạy tầm 50k, 100k memory size vô tư. Tất nhiên cách 2 vẫn là tối ưu hơn cho máy yếu, nwhng chưa biết tốc độ khi implement sẽ như thế nào, cái này cần thực chiến mới biết được.

Update 2: 19 Nov 18: phat hiện ra kiểu save file và load chạy cực kỳ cực kỳ chậm. Suy nghĩ tới việc chuyển load cho 10/100 batch cho từng lần chạy, chú ý 100 batch thực ra chỉ là random trong 100/1mil = 0.0001 nên ảnh hưởng hầu như không đáng kể, có thể thực hiện được. Tuy nhiên, mình đọc được bài này: https://github.com/fg91/Deep-Q-Learning/blob/master/DQN.ipynb Ở đây có 1 cách khá hay là chuyển về dạng uint8 (so với np.float16 của mình), cũng như chuyển dạng lưu là 1mil frame, và khi load chỉ cần index sẽ kéo ra 5 frame, tạo thành state và next_state. Như vậy, memory_size có thể tăng lên thêm 2 * 8 = 16 lần, như giờ mình có thể train với 35k memory_size --> 35k*16 = 560k, có thể coi là đã ổn rồi. Cheer! Vậy là mình nên đọc qua thêm vài github để coi họ có các hướng nào hay ho để học tập nữa, hehee.