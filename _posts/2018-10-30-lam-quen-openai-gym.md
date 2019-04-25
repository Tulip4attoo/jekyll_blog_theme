---
layout:         post
title:          Làm quen với OpenAI gym
date:           2018-10-30
mathjax:        true
comments:       true
description:    Làm quen với OpenAI gym thông qua game Cartpole.
img:            rl/openai.png
---

Reinforcement learning hiện đang là một chủ đề được quan tâm nhiều trong giới AI. Hiện mình đang học RL theo course CS294 của Bekerley song song với tham gia 1 nhóm những người cùng công ty mình. Đây là bài viết đầu tiên trong nhiều bài viết của mình trong quá trình mình học RL. Bạn có thể coi qua 1 [repo](https://github.com/Tulip4attoo/rl) tổng hợp của mình nếu quan tâm tới quá trình học của mình.

## Why OpenAI gym?

> Theo [1 survey năm 2016 của Nature](https://www.nature.com/news/1-500-scientists-lift-the-lid-on-reproducibility-1.19970
), có tới hơn 70% những nhà nghiên cứu đã từng thử và thất bại trong việc reproduce lại những kết quả nghiên cứu khác, và tới hơn một nửa thất bại trong việc reproduce lại kết quả của chính bản thân mình.

OpenAI là 1 công cụ được tạo ra nhằm giúp những nhà nghiên cứu dễ dàng hơn trong việc có 1 benchmark tốt bằng cách tạo một môi trường ổn định, có cách cài đặt đơn giản. Mục đích của công cụ này là giúp tăng khả năng reproduce lại các kết quả trong lĩnh vực AI, cũng như cung cấp 1 công cụ giúp chúng ta dễ dàng thao tác với các môi trường AI hơn. ([nhưng thực ra cũng không ăn thua lắm](https://tulip4attoo.github.io/blog/tir-rl-hard/))

Còn lý do sử dụng OpenAI của mình là bởi nó thiết lập sẵn các môi trường để làm việc và thao tác. Mình [từng xử lý game Dinosaur](https://github.com/Tulip4attoo/chrome_trex) của Chrome, và mình hiểu việc engineering những thứ này mệt mỏi như thế nào.

## Cài đặt

Việc cài đặt khá đơn giản. Có 2 cách: cài qua pip và cài từ source. Mình lựa chọn cách cài từ source, để sau này có thể custom nếu cần.

Trước hết, ta tạo lập môi trường và kích hoạt môi trường.

```
conda create -n rl python=3.6
source activate rl
```

Sau đó, ta cài đặt theo hướng dẫn trên trang chủ.

```
git clone https://github.com/openai/gym
cd gym
pip install -e .
```

## Chạy thử môi trường

Chúng ta có thể bật python lên và thử chạy môi trường

```py
import gym
env = gym.make('CartPole-v0')
env.reset()
for _ in range(1000):
    env.render()
    env.step(env.action_space.sample()) # take a random action
```

Nếu hiện ra 1 cửa sổ như thế này, thì bạn đã cài đặt thành công OpenAI gym rồi.

<p align="center">
  <img src="https://Tulip4attoo.github.io/assets/img/rl/cartpole.gif"><br>
  <i>Môi trường game Cartpole
</i>
</p>

Những bài viết tiếp theo, mình sẽ nói kỹ hơn về các môi trường cũng như cách mình sử dụng RL để giải quyết các môi trường đó. Cheer!

## Tham khảo

[1] https://towardsdatascience.com/reinforcement-learning-with-openai-d445c2c687d2
