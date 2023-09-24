---
layout: single
title:  "RuntimeError: The size of tensor a (20) must match the size of tensor b (10) at non-singleton dimension 1"
toc: true
toc_sticky: true
# categories: []
# tags: [, ]
published: true
---

# 에러가 발생할 때

파이토치를 사용하다가 아래와 같은 에러가 발생할 때가 있다.

![image]![image](https://github.com/StatPage/blog-images/assets/61931924/a0d1fc17-68ba-4c35-8271-be9b11dc809d){: .align-center}

원인은 텐서의 차원이 맞지 않아서다.

# 해결 방안

텐서의 차원을 맞춰주면 된다. 

차원을 맞춰주는 방법은 여러가지가 있지만, 가장 간단한 방법은 `unsqueeze`를 사용하는 것이다.

`unsqueeze`는 텐서의 차원을 늘려줘 (예를 들어, 1차원을 2차원으로) 텐서의 차원을 맞춰준다.

```python
import torch

# 차원이 맞지 않는 경우
a = torch.randn(2, 4)  
# tensor([[ 0.3198,  0.6493, -0.7194,  0.8467],
#         [ 2.1424,  0.2000, -1.7774,  0.2972]])
b = torch.randn(1, 2)  
# tensor([[ 0.2674, -0.2215]])
c = a + b
```

```console
RuntimeError: The size of tensor a (20) must match the size of tensor b (10) at non-singleton dimension 1
```

```python
# 차원을 맞춰주는 방법
c = b.unsqueeze(2)
# tensor([[[ 0.2674],
#          [-0.2215]]])
a + c
```

```console
tensor([[[ 0.5872,  0.9167, -0.4521,  1.1140],
         [ 1.9209, -0.0215, -1.9989,  0.0757]]])
```

`unsqueeze`를 이용해 `b`의 차원을 늘리니 2개의 원소가 열 데이터가 아니라 행 데이터가 되었고 ([1, 2]: 1 row $\times$ 1column $\rightarrow$ [1, 2, 1]: 1 batch $\times$ 2 row $\times$ 1 column), `a`와 row(행) 차원이 2로 맞아서 a의 각 행에 b의 각 원소를 더할 수 있게 되었다.
