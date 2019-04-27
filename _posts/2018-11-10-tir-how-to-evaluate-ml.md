---
layout:         post
title:          TIR - Today I read - How Should We Evaluate Machine Learning for AI?
date:           2018-11-15
mathjax:        true
comments:       true
description:    Tóm tắt lại bài talk How Should We Evaluate Machine Learning for AI? (https://www.youtube.com/watch?v=7CcSm0PAr-Y)
img:            assets/img/tir/adversarial_attack.jpeg
author:         Tulip
---

Hiện tại, xu thế chủ đạo của việc evaluate 1 model ML thường vẫn dựa trên các chỉ số accuracy, F1, hoặc đa dạng hơn xíu thì là ROC, MAP,... nhìn chung thì ta sẽ không thực sự chú trọng (và chưa chú trọng được thì đúng hơn) về việc model có thể gặp các sai lầm ba chấm hay không. Like this:

<p align="center">
  <img src="https://Tulip4attoo.github.io/assets/img/tir/adversarial_attack.jpeg"><br>
  <i>Hmmm, con người sẽ không ntn
</i>
</p>

Chúng ta cần có 1 thay đổi trong việc chỉ focusing on accuracy alone.

https://www.youtube.com/watch?v=7CcSm0PAr-Y

## Intro

Tác giả là Percy Liang, 1 rising super star của ngành AI và NLP với bảng thành tích khá là vcd.

## Vấn đề

Những năm gần đây, ML đang có những bước tiến cực kỳ mạnh mẽ. Nhưng chưa đủ. Vẫn có quá nhiều thứ wrong, ví dụ như adversarial attack có thể khiến 1 model nhận dạng sai hoàn toàn 1 bức ảnh, dù cho con người sẽ không bao giờ làm sai như vậy. Ví dụ thì như ở ảnh trên.

Về cơ bản, kể cả SOTA của CV hay NLP đều rất dễ bị trick như vậy. Trong NLP thì khó giải quyết hơn, còn trong CV thì đang là trò mèo vờn chuột (cat and mouse game), cứ bên này cao thì bên kia lại cao hơn. Vây chúng ta có thể giải quyết vấn đề ntn?

+ capsule?
+ consciousness prior?
+ probabilistics programming?

Chúng ta cần 1 objective evaluation, but how to create it?

## Harder test

Ta cần các bài test khó hơn, mà không đơn giản chỉ cần sử dụng cheap trick là được. Trong NLP, có rất nhiều task chỉ cần cheap trick là ok, và ta còn cách goal rất xa.

## Phát triển được model có thể ngoại suy (extrapolate)

Nội suy thì ok, nhưng ngoại suy thì quá khó. Làm sao để predict ra khi ta chưa từng nhìn thấy nó bao giờ? Con người từng làm được cho nhiều vd ở Vật lý:

+ train: small object, past
+ test: large object, future

Vì vậy, ta vẫn có thể trông đợi

## Phần còn lại

Đoạn trên chỉ là phần mở đầu, đoạn tới nói về harder data và model có khả năng ngoại suy thì thực sự mình nghe không hiểu, quá khó quá mới.

Đoạn này có nói về 1 số kỹ thuật attack + defense, có nhắc tới trong CV thì build attack dễ hơn, trong NLP thì nhiều khi phải build attack bằng tay, khá khó.


## Lời cuối

Thực ra mình bị hiểu nhầm nội dung bài talk nên mới xem, ban đầu mình tưởng nên chọn phương pháp nào để đánh giá cho các bài toán cụ thể. Awww, cuối cùng thì nó khác hẳn, nhưng nghe cũng hay, biết thêm được nhiều thứ, cũng như biết là có quá nhiều thứ mình chưa biết và giờ nghe chưa hiểu hihe.

Việc thay đổi evaluation như nào, hãy dành cho những superstar, mình thì tạm cứ dùng thôi. Nhưng mà cố gắng từ cái đơn giản suy ra cái toàn thể sẽ là goal tiếp theo của ngành AI thôi.