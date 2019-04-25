---
layout: post
title:  Truncation Error trong Finite Difference (Hay là lỗi sai số trong tính toán xấp xỉ đạo hàm)
date:   2017-03-17
mathjax: true
comments: true
description: lỗi sai số trong tính toán xấp xỉ đạo hàm
img: truncation-error/different-figure.png
---


# Truncation Error là gì

Truncation Error (có thể gọi là lỗi sai số, lỗi làm tròn, tuy nhiên có vẻ không có từ tương đương trong tiếng Việt) là 1 lỗi thường gặp trong quá trình tính toán. Đây là tên chung cho các lỗi liên quan tới việc làm tròn số (đặc biệt là việc lưu trữ số thập phân trong máy tính), việc thay thế 1 chuỗi vô hạn bằng chuỗi hữu hạn, nhìn chung, đây là điều không tránh khỏi trong quá trình thực hành. Chú ý theo dõi và giảm thiểu Truncation Error luôn là 1 điều quan trọng trong tính toán và làm việc.

# Đạo hàm của hàm số

Đạo hàm của 1 hàm số là sự biến thiên giá trị của hàm số tại 1 điểm. Về biểu diễn hình học, đạo hàm của 1 hàm số tại 1 điểm là tiếp tuyến của hàm số tại điểm đó.

<p align="center">
  <img src="https://tulip4attoo.github.io/assets/img/truncation-error/different-figure.png"><br>
  <i>Đường màu đỏ chính là đạo hàm của hàm số (nguồn: wikipedia)</i>
</p>

Trong trường hợp chúng ta có hàm số: $y = f(x)$, khi đó đạo hàm của hàm số, ký hiệu là $f'(x)$ được tính bằng:

$$f'(x_{0}) = \lim_{\Delta x\to0} \frac{f(x_{0} + \Delta x) - f(x_{0})}{\Delta x}$$

Tuy nhiên, trong thực tế, chúng ta không thể lấy được giá trị $\Delta x$ nào để có thể thoả mãn được biểu thức bên trên cả. Chúng ta chỉ có thể sử dụng các hằng số nhỏ khác để thay thế. Tuy nhiên, khi tính toán trên các đơn vị quá bé, máy tính sẽ không đưa ra được kết quả thật sự chính xác, vì vậy đây cũng là 1 vấn đề đáng cân nhắc. Chúng ta sẽ chọn $\Delta x$ đủ bé để giá trị xấp xỉ gần với giá trị đạo hàm thực tế, đồng thời phải chọn $\Delta x$ không quá bé để tránh lỗi tính toán nói trên. Một giải pháp cho vấn đề này là lựa chọn hàm xấp xỉ có Truncation Error thấp hơn, khi đó chúng ta không cần $\Delta x$ phải quá bé nữa.

Ở trên chúng ta vừa đề cập tới công thức của đạo hàm hàm số tại 1 điểm. Tuy nhiên, đó chỉ là 1 trong nhiều cách biểu diễn đạo hàm mà thôi. Cụ thể sẽ có 3 cách thường dùng như sau:

* Forward: 

$$f'(x_{0}) = \lim_{\Delta x\to0} \frac{f(x_{0} + \Delta x) - f(x_{0})}{\Delta x}$$

* Backward: 

$$f'(x_{0}) = \lim_{\Delta x\to0} \frac{f(x_{0}) - f(x_{0} - \Delta x)}{\Delta x}$$

* Central: 

$$f'(x_{0}) = \lim_{\Delta x\to0} \frac{f(x_{0} + \frac{1}{2}\Delta x) - f(x_{0} - \frac{1}{2}\Delta x)}{\Delta x}$$


Trong phần tiếp theo, chúng ta sẽ tính lần lượt Truncation error của 3 công thức, từ đó chọn ra công thức có Truncation error nhỏ nhất.

## Khai triển Taylor

Chúng ta sẽ tính Truncation Error của 3 công thức này dựa vào [khai triển Taylor](https://en.wikipedia.org/wiki/Taylor_series). Nhắc lại công thức của khai triển Taylor:

Khai triển Taylor của 1 hàm số $f(x)$ thoả mãn điều kiện có thể đạo hàm vô hạn lần tại điểm a là:

$$f(x) = f(a) + \frac{f'(a)}{1!}(x-a) + \frac{f''(a)}{2!}(x-a)^2 + \frac{f^{(3)}(a)}{3!}(x-a)^3 + ... $$

Chúng ta có thể rút gọn vế phải thành:

$$f(x) = \sum\limits_{n=0}^{\infty} \frac{f^{(n)}(a)}{n!}(x-a)^n$$

## Truncation error của Forward difference

Áp dụng khai triển Taylor cho $f(x + h)$, ta được:

$$f(x + h) = f(x) + \frac{hf'(x)}{1!} + \frac{h^2f''(x)}{2!} + \frac{h^3f^{(3)}(x)}{3!} + ... $$

Chuyển vế $f(x)$ và chia 2 vế cho $h$, ta được:

$$ \frac{f(x+h) - f(x)}{h} = f'(x) + \frac{hf''(x)}{2!} + \frac{h^2f^{(3)}(x)}{3!} + ... $$

$$ \Rightarrow \frac{f(x+h) - f(x)}{h} - f'(x) = O(h) \rightarrow 0 \quad (khi \ \,	h\rightarrow 0)$$

Như vậy, Truncation error của Forward difference có độ lớn $O(h)$. Dễ thấy với Backward difference, Truncation error cũng có độ lớn $O(h)$

## Truncation error của Central difference

Áp dụng khai triển Taylor cho $f(x + \frac{h}{2})$ và $f(x - \frac{h}{2})$, ta được:

$$f(x + \frac{h}{2}) = f(x) + \frac{hf'(x)}{2 *1!} + \frac{h^2f''(x)}{2^2 * 2!} + \frac{h^3f^{(3)}(x)}{2 ^3 *3!} + ... $$

$$f(x - \frac{h}{2}) = f(x) - \frac{hf'(x)}{2 *1!} + \frac{h^2f''(x)}{2^2 * 2!} - \frac{h^3f^{(3)}(x)}{2 ^3 *3!} + ... $$

Trừ vế 2 biểu thức trên, ta được:

$$f(x + \frac{h}{2}) - f(x - \frac{h}{2}) = hf'(x) + \frac{h^3f^{(3)}(x)}{2 ^2 *3!} + ... $$

Chia 2 vế cho $h$, khi đó:

$$ \frac{f(x + \frac{h}{2}) - f(x - \frac{h}{2})}{h} = f'(x) + \frac{h^2f^{(3)}(x)}{2 ^2 *3!} + ... $$

$$ \Rightarrow \frac{f(x + \frac{h}{2}) - f(x - \frac{h}{2})}{h} - f'(x) = O(h^2) \rightarrow 0 \quad (khi \ \,	h\rightarrow 0)$$


Như vậy, Truncation error của Central difference có độ lớn $O(h^2)$. Điều này cho thấy chúng ta nên sử dụng Central difference thay cho Forward difference trong tính toán xấp xỉ đạo hàm.

# Tổng kết

Như vậy, chúng ta đã điểm qua được khái niệm Truncation error, cũng như tính toán được Truncation error cho Forward, Backward, Central difference, từ đó đi tới kết luận sử dụng Central difference sẽ tốt hơn Forward difference trong tính toán xấp xỉ đạo hàm. Tuy nhiên, đây chưa phải là công thức tối ưu. 3 công thức nêu trên chỉ là 1st order của finite diffrence, chúng ta có thể sử dụng 2nd order, 3rd order,... với Truncation error $O(h^4), O(h^6)$ và thấp hơn nữa. Ngoài ra, tôi cũng chưa tìm hiểu trong thực tế, liệu có còn thuật toán xấp xỉ đạo hàm nào khác nữa không. Tuy nhiên, với mức độ tính toán đơn giản (chỉ tính toán tới $0(h)$ nhưng lại chỉ có Truncation error $O(h^2)$), và gần gũi với công thức đạo hàm chúng ta quen thuộc, Central difference vẫn là 1 lựa chọn tốt cho công thức xấp xỉ đạo hàm.