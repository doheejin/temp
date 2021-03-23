---
layout: post
title:  "[PyTorch] torch.nn.functional - softmax / log_softmax 비교"
date:   2021-03-22T14:25:52-05:00
author: Heejin Do
categories: pytorch
tags:	pytorch deep_learning torch.nn functional softmax log_softmax
---

torch.nn.functional을 사용할 때 softmax와 log_softmax에 대해 비교해보았다. 

### 1. softmax
소프트맥스(softmax)는 입력값(k차원의 벡터)을 0에서 1 사이의 값으로 정규화해 k개의 각 클래스에 대한 확률을 추정하는 함수로, 이 때 출력값들의 총합은 1이 된다.
k-dimension 벡터에서 i 번째 원소를 x_i라고 했을 때, i번째 클래스가 정답일 확률을 아래의 softmax 함수식으로 계산할 수 있다.

<img src="/assets/images/softmax-1.png" title="">

이런 softmax 함수를 torch에서 사용하고자 할 때 torch.nn.functional을 불러오며,
사용 형식은 아래와 같다.

- torch.nn.functional.softmax(input, dim=None, _stacklevel=3, dtype=None)


{% highlight python %}
import torch.nn.functional as F

dataset = torch.randn(4)
print(dataset)
print(F.softmax(dataset, dim=0))
print(F.softmax(dataset, dim=0).sum())  # 확률 분포라서 총합이 1이 된다.

## OUTPUT
# tensor([-1.7806,  0.5530,  1.5214,  0.3727])
# tensor([0.0212, 0.2190, 0.5769, 0.1829])
# tensor(1.)
{% endhighlight%}

### 2. log_softmax
