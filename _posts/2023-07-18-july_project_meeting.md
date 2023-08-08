---
layout: single
title:  "7월 회의"
toc: true
toc_sticky: true
categories: programming
tags: [python, documentation, slurm, DoD, doxygen]
---

# 첫째 주

## 배운 것

### DoD

프로젝트는 **DoD**를 바탕으로 진행한다. **DoD는 The definition of done**을 의미한다.

> DoD를 바탕으로 프로젝트를 진행하는 이유는 명확한 조건을 기준으로 프로젝트를 진행하고 관리할 수 있기 때문이다.

### 파일 경로

코드 내에 별도로 `settings` 파일을 만들어 경로 변수를 저장하고 진행했다. 이는 작업환경을 텍스트 파일 바탕으로 바꾸고 설정 것이기 때문에 에러가 발생하는 경우가 많다. (이렇게 작성하는 건 틀렸다고 말씀해주셨다.)

그렇기 때문에 경로를 일일이 수정하지 말고 **parser에 argument로 넣는 것을 추천**한다.

```python
# 수정 전
ROOTPATH = "/workspace"
DATAPATH = ROOTPATH + "data/"
MODELPATH = ROOTPATH + "models/"
```

```python
# 수정 후
parser = argparse.ArgumentParser()
parser.add_argument(
        "--root_dir",  type=str, default="/workspace")
    parser.add_argument(
        "--data_dir",  type=str, default=os.path.join("/workspace", "data"))
    parser.add_argument(
        "--model_dir", type=str, default=os.path.join("/workspace", "models"))
```

### 파일 이름

파일 이름 또한 마찬가지다. 특히 경로(directory)와 합칠 때 는 `os.path.join()`을 활용해야 한다.

```python
# 수정 전
save_dir = ROOTPATH + "/models/%s/%s_%s/" % (
        args.dataset,
        args.dataset,
        args.model_type,
    )
```

```python
# 수정 후
save_dir = os.path.join(
      args.model_dir, args.dataset, "%s_%s" % (args.dataset, args.model_type)
    )
```

### Slurm

CPU/GPU 등의 컴퓨터 리소스를 최적으로 할당한다. 

(실제 사용은 못 함)

Reference

---

- [하우론](https://haawron.tistory.com/)

### 코드 주석

주석의 용도는 다음 두 가지와 같다.

1. 프로그램의 의도와 설치 등의 내용을 담은 일반 문서: [sphinx](https://www.sphinx-doc.org/en/master/)를 사용 권장.
2. 프로그램의 각 부분의 동작을 설명해 놓은 문서: [doxygen](https://www.doxygen.nl/manual/docblocks.html#pythonblocks)을 사용 권장.

#### Doxygen

파이썬에서는 documentation strings(`'''`)를 이용한 코드 작성이 표준 방법이다. `'''`에 작성된 내용은 __doc__에 기록된다. **Doxygen**은 __doc__에 기록된 내용을 추출하는 것이다.

- 예시 1:

```python
"""@package docstring
Documentation for this module.
 
More details.
"""
 
def func():
    """Documentation for a function.
 
    More details.
    """
    pass
 
class PyClass:
    """Documentation for a class.
 
    More details.
    """
   
    def __init__(self):
        """The constructor."""
        self._memVar = 0;
   
    def PyMethod(self):
        """Documentation for a method."""
        pass
```

그 외의 방법으로 `##`를 이용한 방법이 있는데 이 방법의 장점으로는 **doxygen**이 지원하는 다른 언어에도 똑같은 문서 작성 방법을 적용할 수 있기 때문에 통일성이 높아진다.

- 예시 2:

```python
## @package pyexample
#  Documentation for this module.
#
#  More details.
 
## Documentation for a function.
#
#  More details.
def func():
    pass
 
## Documentation for a class.
#
#  More details.
class PyClass:
   
    ## The constructor.
    def __init__(self):
        self._memVar = 0;
   
    ## Documentation for a method.
    #  @param self The object pointer.
    def PyMethod(self):
        pass
     
    ## A class variable.
    classVar = 0;
 
    ## @var _memVar
    #  a member variable
```

## TODO

### DoD

1. sphinx로 일반 문서의 초안을 만든다.
2. doxygen-python 형식으로 모든 함수에 주석을 붙인다.

프로젝트에서는 파이썬의 코드 내용을 설명할 필요가 있기 때문에 **doxygen의 가이드를 바탕으로 주석을 작성**하기로 했다. 내가 맡은 역할은 같이 할 부분을 제외하고 doxygen으로 문서를 작성하는 것이다.

### 직접 수정한 내용

```python
## Calculate the loss of the model to evaluate it.
#
# Arguments
#
# model (_type_): GNTM model.
# test loader (_type_): test data.
# mode (str, optional): the mode of model.
# verbose (bool, optional): Defaults to True.
def test(model, test_loader, mode="VAL", verbose=True):
    model.eval()  # switch to testing mode
    num_sent = 0
    val_output = {}

    # calculate the loss with test data.
    for batch in test_loader:
        batch = batch.to(device)
        batch_size = batch.y.size(0)
        outputs = model.loss(batch)    # outputs = { loss: loss.mean(), recon_word: recon_word.mean(), recon_edge: recon_edge.mean(), recon_structure: recon_structure.mean(), p_edge: p_edge.mean(), KL1: KL1.mean(), KL2: KL2.mena()}

        # with outputs of batch data
        for key in outputs:
            # check the initial value of val_output dictionary
            if key not in val_output:
                val_output[key] = 0
            # add the values of output
            val_output[key] += outputs[key].item() * batch_size
        num_sent += batch_size

    # report the value of loss
    if verbose:
        report_str = " ,".join(
            ["{} {:.4f}".format(key, val_output[key] / num_sent) for key in val_output]
        )
        print("--{} {} ".format(mode, report_str))
    return val_output["loss"] / num_sent

## Return thetas and labels of data with model
#
# Arguments
#
# model: GNTM model
# loader: data loader
#
# return the thetas and labels of data
def learn_feature(model, loader):
    model.eval()  # switch to testing mode
    thetas = []
    labels = []

    # batch of data
    for batch in loader:
        batch = batch.to(device)

        # return the theta of (text) data
        theta = model.get_doctopic(batch)
        thetas.append(theta)
        labels.append(batch.y)
    thetas = torch.cat(thetas, dim=0).detach()
    labels = torch.cat(labels, dim=0).detach()
    return thetas, labels

## Evaluate the model
#
# Arguments
#
# model: GNTM model
# test_loader: test data 
#
# evalute the performance of model with thetas(prediction) and labels
def eval_doctopic(model, test_loader):
    thetas, labels = learn_feature(model, test_loader)
    thetas = thetas.cpu().numpy()
    labels = labels.cpu().numpy()

    # evaluate the model
    eval_top_doctopic(thetas, labels)
    # eval_km_doctopic(thetas, labels)
```

# 둘째 주

## 배운 것

### Function Comments

함수를 설명할 때는 Arguments가 아니라 @param으로 표시를 해야 한다.

Arguments는 함수 내에서 parser의 arguments를 설명하기 위한 것.

```python
# 수정 전
## Calculate the loss of the model to evaluate it.
#
# Arguments
#
# model (_type_): GNTM model.
# test loader (_type_): test data.
# mode (str, optional): the mode of model.
# verbose (bool, optional): Defaults to True.
def test(model, test_loader, mode="VAL", verbose=True):
```

```python
# 수정 후
## Calculate the loss of the model to evaluate it.
#
# @param model (_type_): GNTM model.
# @param test loader (_type_): test data.
# @param mode (str, optional): the mode of model. There is only VAL mode, which means valuation. TODO: clarify this.
# @param verbose (bool, optional): Defaults to True.
# @retval loss dictionary in average. Regrading loss, refer to the explanation inside test method.
def test(model, test_loader, mode="VAL", verbose=True):
```

참고로 이건 경험의 영역이지만 `output`으로 나온 파일에 대해서 주석으로 표시할 때는 구분해서 보기 좋도록 따로 형식을 정해서 보여주자.

```python
# 수정 전
for batch in test_loader:
        batch = batch.to(device)
        batch_size = batch.y.size(0)
        outputs = model.loss(batch)    # outputs = { loss: loss.mean(), recon_word: recon_word.mean(), recon_edge: recon_edge.mean(), recon_structure: recon_structure.mean(), p_edge: p_edge.mean(), KL1: KL1.mean(), KL2: KL2.mena()}
```

```python
# 수정 후
for batch in test_loader:
        batch = batch.to(device)
        batch_size = batch.y.size(0)

        # loss calculation from model
        #
        # type of loss data structure (dictionary with the following key:value)
        #   loss: loss.mean(),
        #   recon_word: recon_word.mean(), TODO: clarify this
        #   recon_edge: recon_edge.mean(), TODO: clarify this
        #   recon_structure: recon_structure.mean(), TODO: clarify this
        #   p_edge: p_edge.mean(), TODO: clarify this
        #   KL1: KL1.mean(), part of loss
        #   KL2: KL2.mena()
        outputs = model.loss(batch)
```

### Dictionary iteration

dictionary의 items을 이용한 loop 문을 할 때는 key만 이용하지 말고 key와 value 모두 쓰면 좋다.

```python
# 수정 전
# ouputs dictionary에서 key만 loop문으로 활용한 경우. value를 또 빼서 구해야 한다.
for key in outputs:
    if key not in val_output:
           val_output[key] = 0
    val_output[key] += outputs[key].item() * batch_size
```

```python
# 수정 후
# value까지 한 번에 꺼내니까 outputs[key].item() -> value.item()으로 간단해졌다.
for key, value in outputs:
            # check the initial value of val_output dictionary
            if key not in val_output:
                val_output[key] = 0
            # add the values of output
            val_output[key] += value.item() * batch_size
```

### 디버깅

딥러닝을 사용하다보니 코드를 전체적으로 돌리는 것보다 코드를 한 줄씩 살펴보면서 결과물과 오류 유무를 살펴봐야 하는 경우가 많이 발생한다. 

pdb를 배우면서 원하는 부분을 살펴보니 프로젝트를 살펴볼 때 많은 도움이 됐다. 필요할 때 다시 볼 수 있도록 기능을 정리해 둔다.

실행 방법과 기능

```bash
python -m pdb example.py
```

- `l`: 주변 소스코드들을 출력(화살표는 현재 라인)
- `n`: 다음 문장으로 이동
- `s`: 함수 내부로 이동. `-Call--`이라고 나옴. 다 진행된 후는 `-Return--`이 나온다.
- `c`: 중단점을 만날때까지 코드를 실행. 중단점이 없다면 끝까지 실행.
- `r`: 현재 함수의 return이 나올때까지 실행.
- `a`: 현재 함수의 매개변수들을 출력.

#### Reference

- [TWpower’s Tech Blog](https://twpower.github.io/203-debug-python-code-using-pdb)

## TODO

1. 주석을 달면서 불명확한 부분 보충하기
2. 코드 리뷰하기

