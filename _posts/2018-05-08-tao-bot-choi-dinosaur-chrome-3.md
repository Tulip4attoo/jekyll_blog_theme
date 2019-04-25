---
layout:         post
title:          Tạo bot chơi T-Rex trong Chrome (phần 3) - Setup môi trường training
date:           2018-05-08
mathjax:        true
comments:       true
description:    Bài viết thứ 3 trong series tạo bot chơi game T-Rex của Chrome. Trong bài viết này, mình sẽ nói về việc setup môi trường để training thuật toán. Môi trường bao gồm 1 model Neural network với 1 hidden layer, xác định state hiện tại của game (tốc độ, vị trí xương rồng, etc.), việc chuyển Neural network thành bot.
img:            chrome-trex/NN_model.png
---

Bài viết thứ 3 trong series tạo bot chơi game T-Rex của Chrome. Trong bài viết này, mình sẽ nói về việc setup môi trường để training thuật toán. Môi trường bao gồm 1 model Neural network với 1 hidden layer, xác định state hiện tại của game (tốc độ, vị trí xương rồng, etc.), việc chuyển Neural network thành bot.

# Ý tưởng thực hiện

Chúng ta sẽ tạo ra 1 model để từ trạng thái của game đưa ra quyết định (nhảy/không nhảy). Trạng thái của game đưa vào input bao gồm: `speed` (speed hiện tại của màn chơi), `distance` (khoảng cách từ khủng long tới cây xương rồng gần nhất), `size` (kích thước của cây xương rồng, vì mỗi cây có 1 kích thước). Tại sao lại lựa chọn những input này? Bởi vì mình thấy chỉ cần có 3 input này là đủ để chơi rồi.

Bàn thêm 1 chút, nếu các bạn có tìm hiểu cách mà Deepmind tạo bot chơi game Atari, input đầu vào của họ chỉ là các ảnh chụp màn hình (cho tất cả các game luôn) [1]. Lý do của Deepmind là muốn xây dựng nên 1 general AI có khả năng chơi đa dạng các game, và đặc biệt có nhiều game con người còn chưa thể đưa ra lời giải tốt nhất, vì vậy các AI tạo ra không cần tri thức của con người, sẽ có nhiều khả năng đạt được tầm cao hơn là bị phụ thuộc vào hiểu biết của con người. Điều này thể hiện rõ trong triết lý và cách xây dựng Alphago Zero, phiên bản mới xịn xò hơn rất nhiều của Alphago [2].

Tuy nhiên đây là project nhỏ, nên thay vì giết gà bằng da mổ trâu, ta sẽ áp dụng luôn. Có thể trong project tiếp theo mình sẽ học cách của Deepmind (nếu mình đủ chăm để viết blog lại =)) ).

## Lựa chọn model

Như đã thực hiện ở phần 1, chúng ta có thể tạo ra 1 phiên bản hardcode chỉ từ việc check xem 1 vùng trước mặt của khủng long có xương rồng hay không. Điều này cho thấy ta có thể xây dựng model không mấy phức tạp và vẫn có thể giải quyết được bài toán.

Cá nhân tôi cho rằng có thể giải quyết bài toán với nhiều model, ví dụ logistic, decision tree,... Tuy nhiên tôi lựa chọn Neural network để giải quyết bài toán này. Lý do lựa chọn Neural network là bởi:

+ tôi biết Neural network đủ khả năng giải quyết.

+ sử dụng Neural network thì có thể giải quyết cho các bài toán khác sau này (nếu tôi có làm tiếp cho 1 game nào đó) 

+ tôi thích vì Neural network nghe cool hơn :))

Ok. Vậy chúng ta sẽ lựa chọn các hyper-param như nào cho Neural network của mình? Tôi lựa chọn size 3-3-1 (1 Hidden layer với 3 nodes), lý do bởi vì tôi nghĩ 1 cấu trúc đơn giản như vậy cũng có thể giải quyết bài toán. Output của Neural network sẽ là 1 giá trị `action_value`, nếu `action_value` lớn hơn 1 `threshold` nào đó thì sẽ quyết định sẽ nhảy, còn lại thì không.

<p align="center">
  <img src="https://Tulip4attoo.github.io/assets/img/chrome-trex/NN_model.png"><br>
  <i>Model để chơi</i>
</p>

# Code phần Neural network

Mục đích chính của phần này là tạo được 1 model NN, có cấu trúc [3,3,1] với input đầu vào là `input_set = [distance, speed, size]`, và cho ra output là hành động. Phần này được tôi tạo riêng ở file `trex_nn.py`.

Trước hết chúng ta import các package cần thiết. Ở đây ta import `numpy` để thực hiện tính toán, và `chrome_trex_api` để tiến hành chuyển từ output của model ra hành động.

```python
import numpy as np
import chrome_trex_api
```

Chúng ta define 2 hàm activation function trong NN là `relu` và `sigmoid`

```python
def relu(array):
    return np.maximum(array, 0.)

def sigmoid(z):
    s = 1 / (1 + np.exp(-z))
    return s
```

Chúng ta cần 1 hàm `initialize_parameters` để tạo ra các giá trị random ban đầu cho NN. Input đầu vào sẽ là kích thước của NN. Chú ý, chúng ta sẽ nhân các weight và bias với các hệ số riêng, lý do là để scale các giá trị lại với nhau để model hội tụ nhanh hơn.

```python
def initialize_parameters(n_x, n_h, n_y):
    """
    Argument:
    n_x -- size of the input layer
    n_h -- size of the hidden layer
    n_y -- size of the output layer
    
    Returns:
    params -- python dictionary containing your parameters:
                    W1 -- weight matrix of shape (n_h, n_x)
                    b1 -- bias vector of shape (n_h, 1)
                    W2 -- weight matrix of shape (n_y, n_h)
                    b2 -- bias vector of shape (n_y, 1)
    """
    
    W1 = np.random.randn(n_h, n_x) * 0.01
    b1 = np.random.randn(n_h, 1) * 0.5
    W2 = np.random.randn(n_y, n_h) * 1.5
    b2 = np.random.randn(n_y, 1) * 0.15
    
    parameters = {"W1": W1,
                  "b1": b1,
                  "W2": W2,
                  "b2": b2}
    
    return parameters
```

Có 1 hàm nhỏ ta cũng phải xài là `re_shape_X` nhằm reshape lại input. Lý do là input chúng ta có dạng ma trận ngang `input_set = [distance, speed, size]`, cần chỉnh sửa đôi chút về dạng ma trận dọc.

```python
def re_shape_X(X, n_x):
    return np.reshape(X, (n_x, 1))
```

Chúng ta dùng hàm `tRex_model` để tính toán output thông qua input là bộ input `X, parameters`. Ở đây, `parameters` là 1 dictionary chứa các parameters. Activation function được sử dụng là `sigmoid`.

```python
def tRex_model(X, parameters):
    """
    Argument:
    X -- input data of size (n_x, m)
    parameters -- python dictionary containing your parameters (output of initialization function)
    
    Returns:
    A2 -- The sigmoid output of the second activation
    """
    W1 = parameters['W1']
    b1 = parameters['b1']
    W2 = parameters['W2']
    b2 = parameters['b2']

    Z1 = np.dot(W1, X) + b1
    A1 = sigmoid(Z1)
    Z2 = np.dot(W2, A1) + b2
    A2 = sigmoid(Z2)
    return A2
```

Và cuối cùng là hàm `wrap_model` để áp dụng model vào để chơi

```python
def from_model_to_action(value, threshold = 0.6):
    if value > threshold:
        chrome_trex_api.press_up()

def wrap_model(X, parameters, n_x):
    X_adj = re_shape_X(X, n_x)
    action_value = tRex_model(X_adj, parameters).item()
    from_model_to_action(action_value, threshold = 0.6)
```

Cụ thể hơn bạn có thể xem file ở [github mình](https://github.com/Tulip4attoo/chrome_trex/blob/master/trex_nn.py)

# Nhận diện vật thể

Đây thực sự là phần tốn nhiều công sức của mình nhất. Phần tạo model và implement GA thì không quá mệt, nhưng nhận diện vật thể gặp phải khá nhiều vấn đề. Mục đích chính của phần này là lấy được state của game, sau đó gộp lại thành `input_set = [distance, speed, size]`, và còn có phần xử lý liên tục trong lúc chơi (đảm bảo được behavior cho bot luôn) . Phần này được tôi tạo riêng ở file `capturing_objects.py`. Tuy nhiên, tôi sẽ không tập trung vào phần này bởi nó sẽ khiến bạn phân tâm khỏi việc thực hiện model. Ngoài ra, hiện tại các vấn đề bắt đầu được hỗ trợ nhiều hơn về phần engineering, ví dụ như openAI gym, môi trường StarCraft của Deepmind, nên phần này bạn có thể bỏ qua.

Cụ thể hơn bạn có thể xem file ở [github mình](https://github.com/Tulip4attoo/chrome_trex/blob/master/capturing_objects.py)

Đầu tiên vẫn là màn import các package cần thiết. Chúng ta sẽ dùng  `mss` để chụp màn hình, `pyautogui` để output ra ngoài, `webbrowser` cùng `_thread` (ở python 2 nó là `thread`) phục vụ việc mở browser và vào game (cảm ơn anh [Huy Trần](https://kipalog.com/users/huytd/mypage)) đã nhắc) và vài package quen thuộc khác. Ta cũng import `trex_nn` để lấy model cho bot.

```python
import pyautogui
import time
import numpy as np
import cv2
import os
import webbrowser
import _thread

import trex_nn

from mss import mss
```

Để thuận tiện cho các bạn sử dụng, mình có viết cả phần setup môi trường chạy, bao gồm việc mở chrome, truy nhập website, co màn hình lại. Do đặc thù của package `webbrowser`, ta cần mở thêm 1 thread để chạy lệnh `webbrowser.open`. Chi tiết hơn có thể xem dưới đây.

```python
def open_url_on_chrome(url):
    chrome_path = '/usr/bin/google-chrome %s'
    webbrowser.get(chrome_path).open(url)
    print("opening...")

def chrome_setup(url):
    _thread.start_new_thread(open_url_on_chrome, (url,))
    time.sleep(2)
    pyautogui.hotkey('ctrl', 'winleft', 'left')
```

Chúng ta sẽ khai báo các hằng số, bao gồm: `GAMEOVER_BOX` để chứa thông tin vị trí xuất hiện chữ `GAME OVER` (chú ý là giá trị này ứng với giá trị của màn hình game, nên không cần điều chỉnh), `MAX_SPEED_STEP` là bước chuyển tối đa cho speed (vd speed hiện tại là 300 thì speed trong frame kế tiếp không vượt quá 315), `INIT_SPEED` là speed ban đầu của màn chơi. Ngoài ra còn có thêm một vài hằng số được dùng từ bài trước.

```python
GAMEOVER_BOX = {'Y_GAMEOVER': 30, 'X_GAMEOVER': 115, 
                'W_GAMEOVER': 200, 'H_GAMEOVER': 15}
GAMEOVER_RANGE = [630000, 670000]
TIME_BETWEEN_FRAMES = 0.01
TIME_BETWEEN_GAMES = 0.5

MAX_SPEED_STEP = 15
INIT_SPEED = 270
N_X = 3 # kich thuoc input layer
N_H = 3 # kich thuoc Hidden layer
N_Y = 1 # kich thuoc output layer
LANDSCAPE = False
```

Để lấy được state hiện tại của game như: `speed, size, distance`, chúng ta cần phân tích hình ảnh màn hình game. Để có thể phù hợp với nhiều máy, thay vì hardcode như bài trước, ta sẽ tạo ra 1 hàm `find_game_position` để xác định vị trí của game. Việc thực hiện là matching màn hình với 1 template có sẵn và xác định phần matched.

<p align="center">
  <img src="https://Tulip4attoo.github.io/assets/img/chrome-trex/dino_landscape.png"><br>
  <i>Template để xác định diện tích màn chơi</i>
</p>

```python
def find_game_position(sct, threshold):
    # ham de tim game position 
    dino_template = cv2.imread(os.path.join('templates', 'dino.png'), 0)
    w, h = dino_template.shape[::-1]
    landscape_template = cv2.imread(os.path.join('templates', 'dino_landscape.png'), 0)
    lw, lh = landscape_template.shape[::-1]

    landscape = {}
    # mac dinh la se hien thi o screenshot 1, du chung ta co 1 hay nhieu man hinh
    monitor = sct.monitors[1]
    image = np.array(sct.grab(monitor))[:,:,:3]
    gray_image = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    res = cv2.matchTemplate(gray_image, dino_template, cv2.TM_CCOEFF_NORMED)
    loc = np.where(res >= threshold)
    if len(loc[0]):
        pt = next(iter(zip(*loc[::-1])))
        landscape = dict(monitor, height=lh, left=pt[0], top=pt[1] - lh + h, width=lw)
    return landscape

def get_game_landscape_and_set_focus_or_die(sct, threshold=0.7):
    # ham ho tro
    landscape = find_game_position(sct, threshold)
    if not landscape:
        print("Can't find the game!")
        exit(1)
    pyautogui.click(landscape["left"], landscape['top'] + landscape['height'])
    return landscape
```

Để thực hiện việc tính toán các chỉ số, ta có thể tính toán cho 1 khu vực nhỏ thay vì là toàn bộ màn chơi, phần này ta gọi là `roi - region_of_interest`.

```python
def compute_region_of_interest(landscape):
    # tu thong tin ve landscape, ta lay ra duoc 1 vung chua day du cac vat the can
    # xem xet, nhung lai nho hon landscape (do do giam duoc khoi luong tinh toan)
    ground_height = 12
    y1 = landscape['height'] - 44
    y2 = landscape['height'] - ground_height
    x1 = 44 + 24
    x2 = landscape['width'] - 1
    return x1, x2, y1, y2
```

Ta thực hiện tính toán các chỉ số như 2 hàm dưới đây. Để lấy được `distance` (khoảng cách từ khủng long tới cây xương rồng gần nhất), ta sẽ quét từ khủng long cho tới vùng đổi màu gần nhất, trong trường hợp không có xương rồng, ta sẽ gán `distance = max_distance` với max_distance được ta tự quy định. `size` thì lấy tương tự. Riêng về `speed`, ta tính bằng cách lấy quãng đường thay đổi chia cho thời gian. Tuy nhiên, vì cắt cảnh thường không mượt nên ta cần thêm vài mốc để có `speed` mượt hơn.

```python
def compute_distance_and_size(roi, max_distance):
    """
    minh chi check vung region_of_interest thoi vung nay chi co xuong rong 
    --> co mau la thi` minh se biet do la co xuong rong. Lay vung do thoi.
    Doan nay hard code de co size.
    Khong hay lam, nhung cung chap nhan duoc
    """
    obstacle_found = False
    distance = max_distance
    roi_mean_color = np.floor(roi.mean())
    last_column = distance
    for column in np.unique(np.where(roi < roi_mean_color)[1]):
        if not obstacle_found:
            distance = column
            obstacle_found = True
        elif column > last_column + 4:
            break
        last_column = column
    return distance, last_column - distance

def compute_speed(distance_array, last_distance, last_speed, loop_time, max_speed_step = 15):
    """
    tinh dua theo ca quang duong va thoi gian no di tren ca quang duong day
    boi vi co vai lag nen doi khi speed se bi noi ra rat rong, nen ta can co
    1 max_speed_step de gioi han
    """
    speed = (distance_array[0] - last_distance) / loop_time
    return max(min(speed, last_speed + max_speed_step), last_speed - max_speed_step)
```

Ta define thêm vài hàm phụ trợ. Các hàm phần lớn có trong file `chrome_trex_api.py` nhưng mình có sửa thêm để tránh hard code, có thể chạy với các máy khác nhau.

```python
def reset_game():
    pyautogui.hotkey('ctrl', 'r')
    time.sleep(2)

def reset_game_2(landscape):
    y = 65 + landscape['top']
    x = 235 + landscape["left"]
    pyautogui.click(y, x)
    time.sleep(2)

def start_game():
    pyautogui.press('space')
    time.sleep(1.5)

def check_gameover(img_landscape, gameover_range = GAMEOVER_RANGE, gameover_box = GAMEOVER_BOX):
    result = False
    y1 = GAMEOVER_BOX["Y_GAMEOVER"]
    y2 = GAMEOVER_BOX["Y_GAMEOVER"] + GAMEOVER_BOX["H_GAMEOVER"]
    x1 = GAMEOVER_BOX["X_GAMEOVER"]
    x2 = GAMEOVER_BOX["X_GAMEOVER"] + GAMEOVER_BOX["W_GAMEOVER"]
    gray_image = cv2.cvtColor(img_landscape, cv2.COLOR_BGR2GRAY)
    gray = gray_image[y1:y2, x1:x2]
    curr_state = gray.sum()
    print(x1,x2,y1,y2, curr_state)
    if curr_state < GAMEOVER_RANGE[1] and curr_state > GAMEOVER_RANGE[0]:
        result = True
    return result
```

Cuối cùng, ta define hàm `play_game`, để với 1 bộ `parameters_set` làm input, ta sẽ chạy game từ đầu cho tới lúc game over. Hàm `play_game` ban đầu sẽ quét vị trí màn chơi bằng hàm `get_game_landscape_and_set_focus_or_die`, sau đó liên tục lấy thông tin states của game `distance, speed, size`, thông qua hàm `trex_nn.wrap_model` để từ đó đưa ra hành động.

```python
def play_game(parameters_set):
    global LANDSCAPE
    with mss() as sct:
        if LANDSCAPE:
            reset_game_2(LANDSCAPE)
        else:
            reset_game()
        landscape = get_game_landscape_and_set_focus_or_die(sct, .8)
        start_game()

        last_distance = landscape['width']
        x1, x2, y1, y2 = compute_region_of_interest(landscape)
        speed = INIT_SPEED
        start_time = None
        distance_array = []
        count_cactus = 0

        while True:
            image = np.array(sct.grab(landscape))[:,:,:3]
            gray_image = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
            gray_image += np.abs(247 - gray_image[0, x2])
            roi = gray_image[y1:y2, x1:x2] # roi la tam anh region_of_interest thoi
            distance, size = compute_distance_and_size(roi, x2)
            input_set = [distance, speed, size]
            trex_nn.wrap_model(input_set, parameters_set, N_X)
            if distance == x2:
                continue
            elif distance < 40 or distance > last_distance: # co the thay bang so khac, tuy nhien ko qua can thiet hehe
                if len(distance_array):
                    end_time = time.time()
                    if start_time:
                        loop_time = end_time - start_time
                        speed = compute_speed(distance_array, distance, 
                            speed, loop_time, max_speed_step = MAX_SPEED_STEP)
                    start_time = time.time()
                    count_cactus += 1
                    print(count_cactus)
                distance_array = []
            else:
                distance_array.append(distance)
            gameover_state = check_gameover(image)
            print(gameover_state)
            if gameover_state:
                print("Game over. Restart game")
                return count_cactus
            last_distance = distance
            time.sleep(TIME_BETWEEN_FRAMES)
```

# Kết luận

Tới đây, ta đã hoàn thành việc define sẵn mạng Neural network để chạy bot, cũng như phần engineering để xác định các thông tin cho bot của chúng ta. Công việc còn lai thì chúng ta cần implement GA để train cho model. Phần implement GA và train model mình sẽ viết trong phần 4, cũng là phần cuối của series này.

Các bạn có thể chạy file [`clever_bot.py`](https://github.com/Tulip4attoo/chrome_trex/blob/master/clever_bot.py) để xem ví dụ về 1 con bot chạy trên cơ sở những thiết lập trong bài viết này.

# Tham khảo

1. https://arxiv.org/abs/1312.5602 (paper này public trước khi Google mua lại Deepmind, hẳn là cũng có (nhiều) tác động trong việc mua lại này).

2. https://deepmind.com/blog/alphago-zero-learning-scratch/ (Deepmind có cách viết blog rất tuyệt vời, rõ ràng, tường minh, minh hoạ tốt bằng nhiều hình ảnh/video, giải thích dễ hiểu, rất đáng học tập).

3. https://www.deeplearning.ai/ (phần code Neural network có tham khảo ở đây).