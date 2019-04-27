---
layout:                 post
title:                  Tạo bot chơi T-Rex trong Chrome
author:                 Tulip
date:                   2019-04-27
mathjax:                true
featured:               true
description:            Giới thiệu về 1 series 4 bài viết về việc tạo bot chơi game T-Rex của Chrome. Các bài viết sẽ giới thiệu và diễn giải các khái niệm về Genetic Algorithm, cũng như hướng dẫn cụ thể các bước engineering, như thiết kế hệ thống, lấy input là image và xử lý, cách train hệ thống. Có 2 version bot là hardcode version và neural network version train bởi Genetic Algorithm.
img:                    assets/img/chrome-trex/chrome_trex_intro.gif
img_feature:            assets/img/chrome-trex/chrome_trex_intro_square.gif
categories:             [ bot, TRex Chrome, GA ]
---

Có thể bạn đã biết, trình duyệt Google Chrome được tích hợp sẵn một game nhỏ để bạn có thể giết thời gian mỗi khi mất mạng. Nếu bạn truy cập một trang web bằng Chrome mà mất mạng, bạn sẽ nhìn thấy hình ảnh một chú khủng long khá dễ thương hiện ra. Cách chơi game rất đơn giản: bạn bấm phím "Space"/"Up" để nhảy tránh chướng ngại vật, là cây xương rồng hoặc những con khủng long bay.

<p align="center">
  <img src="https://Tulip4attoo.github.io/assets/img/chrome-trex/chrome_trex_intro.gif"><br>
  <i>Infamous Chrome game</i>
</p>

# Source code

Source code (và cả weights) được lưu ở [github của mình](https://github.com/Tulip4attoo/chrome_trex/)

# Bài viết

Mình có viết 1 series 4 bài hướng dẫn khá chi tiết về project này, từ thuật toán cho tới cách code. Các bạn có thể đọc ở đây:

[Part 1](https://tulip4attoo.github.io/blog/tao-bot-choi-dinosaur-chrome-1/) |
[Part 2](https://tulip4attoo.github.io/blog/tao-bot-choi-dinosaur-chrome-2/) |
[Part 3](https://tulip4attoo.github.io/blog/tao-bot-choi-dinosaur-chrome-3/) |
[Part 4](https://tulip4attoo.github.io/blog/tao-bot-choi-dinosaur-chrome-4/)

# Tham khảo

+ https://github.com/pauloalves86/go_dino 
+ deeplearning.ai courses
+ http://www.obitko.com/tutorials/genetic-algorithms/index.php 
