---
layout: post
title:  "[PyTorch] torch.nn.functional - softmax / log_softmax 비교"
date:   2021-03-22T14:25:52-05:00
author: Heejin Do
categories: pytorch
tags:	pytorch deep_learning torch.nn functional softmax log_softmax
---

softmax와 log_softmax를 비교하고, torch.nn.functional에서의 사용법을 정리해보았다. 

### 1. softmax
소프트맥스(softmax)는 입력값(k차원의 벡터)을 0에서 1 사이의 값으로 정규화해 k개의 각 클래스에 대한 확률을 추정하는 함수로, 이 때 출력값들의 총합은 1이 된다.
k-dimension 벡터에서 i 번째 원소를 x_i라고 했을 때, i번째 클래스가 정답일 확률을 아래의 softmax 함수식으로 계산할 수 있다.

<img src="/assets/images/softmax-1.png" title="">

softmax 함수를 torch에서 사용하고자 할 때 **torch.nn.functional**을 불러오며,
사용 형식은 아래와 같다.

- torch.nn.functional.softmax(input, dim=None, _stacklevel=3, dtype=None)

**사용 예시)**
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

------
### 2. log_softmax
로그 소프트맥스는 소프트맥스에 log함수를 취한 것으로(`log(softmax)`), softmax 함수를 보완하는 역할을 한다.
딥러닝 학습시, softmax 함수를 이용하면 **Vanishing Gradients(기울기 손실) 문제**가 발생하기 쉬운데, 이 문제를 해결하기 위해 사용하는 것이 LogSoftmax 이다. 
또한, 로그 소프트맥스를 사용하면 (*,/)를 (+,-) 형식으로 변형할 수 있어(소위 `log-sum-exp trick`이라고 불림) 수식 계산에서 더 안정적인 장점이 있다(**numerically more stable**).
<img src="/assets/images/softmax-2.png" title="">

로그 소프트맥스 함수도 마찬가지로 **torch.nn.functional**을 불러와 사용하며,
사용 형식은 아래와 같다.

- torch.nn.LogSoftmax(dim=None)

**사용 예시)**
{% highlight python %}
import torch.nn.functional as F

dataset = torch.randn(4)
print(dataset)
print(F.softmax(dataset, dim=0)) # 소프트맥스
print(F.log_softmax(dataset, dim=0)) # 로그 소프트맥스
print(torch.exp(F.log_softmax(dataset, dim=0))) # 로그 소프트맥스에 exp()를 하면, 소프트맥스와 같은 결과!

## OUTPUT
# tensor([ 1.1663, -0.8920, -0.8952, -0.9068])
# tensor([0.7242, 0.0925, 0.0922, 0.0911])
# tensor([-0.3226, -2.3808, -2.3841, -2.3956])
# tensor([0.7242, 0.0925, 0.0922, 0.0911])

{% endhighlight%}

> 참고 사이트 
> : https://eli.thegreenplace.net/2016/the-softmax-function-and-its-derivative/
> https://discuss.pytorch.org/t/logsoftmax-vs-softmax/21386
