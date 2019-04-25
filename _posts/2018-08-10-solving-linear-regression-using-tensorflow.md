---
layout:         post
title:          Làm quen với TensorFlow qua giải bài toán Linear regression
date:           2018-08-10
mathjax:        true
comments:       true
description:    TensorFlow là một framework về machine learning, đặc biệt chuyên về Deep learning của Google. Bài viết này nhằm mục đích làm quen với một vài khái niệm, cũng như flow khi dùng TensorFlow giải quyết các bài toán, mà cụ thể ở đây là bài toán Linear regression.
img:            make-gif/linear_regression_fitting.gif
---

TensorFlow là một framework về machine learning, đặc biệt chuyên về Deep learning của Google. Nếu chưa biết về TensorFlow, bạn có thể coi giới thiệu về TensorFlow mà mình từng viết 2 năm trước [ở đây](https://tulip4attoo.github.io/blog/dung-tensorflow-giai-quyet-fizzbuzz/).

# Flow khi sử dụng TensorFlow

Khi sử dụng TensorFlow để giải quyết các bài toán machine learning, mình sẽ thực hiện theo flow như sau:

- Xây dựng graph: bước nào bao gồm tạo ra graph theo chiều xuôi (foward), bao gồm việc tạo ra các placeholders/variables để làm các nodes, sau này mình đưa thông tin input, lưu trữ và lấy thông tin các weights/output. Ngoài ra, trong graph còn có các edges mang thông tin các operations nữa.
- Xây dựng optimizer: phần đa các thuật toán machine learning đều cần 1 thuật toán optimization nhằm điều chỉnh các parameters để tạo ra model chính xác hơn. Ở bước này, ta cần tạo ra loss node, và tạo ra 1 optimizẻ object để thực hiện bước optimization đó.
- Tạo session: những thứ chúng ta tạo ra phía trên chỉ là việc chúng ta define ra 1 graph, tuy nhiên để có thể thực hiện tính toán, chạy model, ta cần các session để thực hiện các tính toán đã được define đó. Ta thực hiện các bước training ở phần session này.

Ngoài ra, ta cần feed data vào model nữa. Việc này có thể thực hiện ở bước xây dựng graph hoặc trong bước tạo session, tuỳ theo model và lựa chọn của chúng ta.

# Giải quyết bài toán Linear regression

Trước hết chúng ta import các package cần thiết. Ở đây ta có import thêm matplotlib và imageio nhằm mục đích visualization sau này.
```python
import tensorflow as tf
import numpy as np
import time
import matplotlib.pyplot as plt
import imageio
```

Chúng ta bắt đầu với 1 tập toy dataset gồm `x` và `y` như sau:
```python
x = np.arange(0, 1, 0.01)
y = np.arange(2, 4, 0.02) + np.random.randn(x.shape[0],) * 0.2
```

Tiếp đó, chúng ta sẽ tạo ra các nodes, mang thông tin cần thiết của layers. Do chúng ta xác định model sẽ dùng là linear regression, nên ta cần thêm 1 layer nữa, mà không có activation function cho lớp này, đây đồng thời cũng là kết quả cần tính. Chú ý 1 điều, ta cho luôn data vào input và output luôn (cái này tuỳ bài toán mà sẽ có lựa chọn phù hợp)
```python
x_train = tf.constant(x, dtype=np.float32, shape=[x.shape[0], 1])
y_train = tf.constant(y, dtype=np.float32, shape=[x.shape[0], 1])
with tf.variable_scope("foo"):
    weights = tf.get_variable("weights", [1, 1],
                    initializer=tf.random_normal_initializer())
    biases = tf.get_variable("biases", [1, 1],
                    initializer=tf.constant_initializer(0.0))
```

Chiều foward có thể xác định khá đơn giản:
```python
y_hat = tf.add(tf.matmul(x_train, weights), biases)
```

Chiều backward thì ta dùng gradient descent, với loss function là RMSE:
```python
loss = tf.reduce_mean(tf.square(y_hat - y_train))
optimizer = tf.train.GradientDescentOptimizer(0.1)
train = optimizer.minimize(loss)
```

Tới đây, ta sẽ tạo session để chạy model:
```python
init_op = tf.global_variables_initializer()
with tf.Session() as sess:
    sess.run(init_op)
    for i in range(200):
        _, loss_value = sess.run((train, loss))
        if i % 5 == 0:
            print(loss_value)
```

Và đây là kết quả của chúng ta:
<p align="center">
  <img src="https://Tulip4attoo.github.io/assets/img/make-gif/linear_regression_fitting.gif"><br>
  <i>Sử dụng gradient để fit linear regression</i>
</p>

# Kết luận

Chúng ta đã hoàn thành code xong thuật toán linear regression sử dụng TensorFlow. Đây là thuật toán đơn giản, dễ code, đặc biệt là khi dùng "dao mổ trâu" như TensorFlow. Nếu bạn muốn làm quen TensorFlow, đây là bài toán ban đầu phù hợp, trước khi chuyển tới những bài toán khác, ví dụ như [dùng Neural network giải quyết bài toán fizzbuzz](https://tulip4attoo.github.io/blog/dung-tensorflow-giai-quyet-fizzbuzz/). Dự định tiếp theo của mình sẽ là dùng TensorFlow để thực hiện implement [word2vec](https://www.tensorflow.org/tutorials/representation/word2vec).