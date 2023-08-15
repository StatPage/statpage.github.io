---
layout: single
title:  "k-GNN model flow in GDGNN package"
toc: true
toc_sticky: true
# categories: []
# tags: [, ]
published: true
---

# For each epoch

model.loss(batch)

# In GDGNNmodel package

loss function ($\rightarrow$ forward function)

```python
    def loss(self, batch_data):
        return self.forward(batch_data)
```

## forward method

```python
    def forward(self, batch_data):
        idx_x = batch_data.x
        x_batch = batch_data.x_batch
        idx_w = batch_data.x_w
        edge_w = batch_data.edge_w
        edge_id = batch_data.edge_id
        edge_id_batch = batch_data.edge_id_batch
        edge_index = batch_data.edge_index
        size = int(x_batch.max().item() + 1)
        print(idx_x.size() , x_batch.size(), x_batch.size(), edge_index.size(), edge_w.size())

        ## compute posterior
        #
        # KL theta
        #
        # @param idx_x: index of x
        # @param idx_w: index of weight of x
        # @param x_batch: x batch data
        # @param edge_index: index of edge
        # @param edge_w: weight of edge
        param, phi = self.encoder(
            idx_x, idx_w, x_batch, edge_index, edge_w
        )  # (B, K ) (B*len, K) B*len = B*len(idx_x)
```

self.encoder = GNNDir2encoder

```python
        if args.prior_type == "Dir2":
            self.encoder = GNNDir2encoder(args, self.word_vec)   # instance of GNNDir2encoder
```

in encoder folder,\
`GNNDir2encoder` class

```python
class GNNDir2encoder(nn.Module):
    def __init__(self, args, word_vec):
        super(GNNDir2encoder, self).__init__()
        self.args = args
        if word_vec is not None and args.word:
            self.word_vec = word_vec
            if args.fixing:
                self.word_vec.requires_grad = False
        else:
            self.word_vec = torch.eye(
                args.vocab_size, dtype=torch.float, device=args.device
            )
        input_size = self.word_vec.size(1)
        self.enc1_gnn1 = GraphConv(input_size, args.nw, bias=True)
        self.bn_gnn1 = nn.BatchNorm1d(args.nw)

        self.enc2_fc1 = nn.Linear(input_size + args.nw, args.enc_nh)
        self.enc2_fc2 = nn.Linear(input_size + args.nw, args.enc_nh)
        self.enc2_drop = nn.Dropout(0.2)

        self.mean_fc = nn.Linear(args.enc_nh, args.num_topic)  # 100  -> 50
        self.mean_bn = nn.BatchNorm1d(args.num_topic)  # bn for mean
        self.logvar_fc = nn.Linear(args.enc_nh, args.num_topic)  # 100  -> 50
        self.logvar_bn = nn.BatchNorm1d(args.num_topic)  # bn for logvar

        self.phi_fc = nn.Linear(args.nw + input_size + args.enc_nh, args.num_topic)
        self.phi_bn = nn.BatchNorm1d(args.num_topic)

        self.logvar_bn.weight.requires_grad = False
        nn.init.constant_(self.logvar_bn.weight, 1.0)
        self.mean_bn.weight.requires_grad = False
        nn.init.constant_(self.mean_bn.weight, 1.0)

        prior_mean = torch.Tensor(1, args.num_topic).fill_(0)
        prior_var = torch.Tensor(1, args.num_topic).fill_(args.variance)
        prior_logvar = prior_var.log()
        self.register_buffer("prior_mean", prior_mean)
        self.register_buffer("prior_var", prior_var)
        self.register_buffer("prior_logvar", prior_logvar)

        self.reset_parameters()

    def reset_parameters(self):
        nn.init.zeros_(self.logvar_fc.weight)
        nn.init.zeros_(self.logvar_fc.bias)
        pass

    def forward(self, idx_x, idx_w, x_batch, edge_index, edge_weight):
        print(idx_x, idx_x.size(), self.word_vec, self.word_vec.size())
        x = self.word_vec[idx_x]
        diag = (
            torch.ones(2, idx_x.size(0), dtype=torch.long, device=idx_x.device).cumsum(
                dim=-1
            )
            - 1
        )
        edge_index_exp = torch.cat([edge_index, diag], dim=-1)  # 添加对角线 대각선 추가요~
        diag_w = (
            torch.ones(idx_x.size(0), dtype=torch.float, device=idx_x.device) * idx_w
        )
        edge_weight_exp = torch.cat([edge_weight, diag_w], dim=0)

        enc1 = torch.tanh(
            self.bn_gnn1(self.enc1_gnn1(x, edge_index_exp, edge_weight=edge_weight_exp))
        )

        if torch.isnan(enc1).sum() > 0:
            import ipdb

            ipdb.set_trace()
        enc1 = torch.cat([enc1, x], dim=-1)
        enc2 = torch.sigmoid(self.enc2_fc1(enc1)) * torch.tanh(self.enc2_fc2(enc1))
        size = int(x_batch.max().item() + 1)
        enc2 = scatter(enc2, x_batch, dim=0, dim_size=size, reduce="sum")  # B*enc_h
        enc2d = self.enc2_drop(enc2)

        mean = self.mean_bn(self.mean_fc(enc2d))  # posterior mean
        logvar = self.logvar_fc(enc2d)  # posterior log variance

        word_embed = torch.cat([enc1, enc2[x_batch]], dim=-1)
        phi = torch.softmax(self.phi_fc(word_embed), dim=-1)  # (B*max_len)*num_topic

        param = (mean, logvar)
        if torch.isnan(mean).sum() > 0:
            import ipdb

            ipdb.set_trace()
        return param, phi

    def reparameterize(self, param):
        posterior_mean = param[0]
        posterior_var = param[1].exp()
        # take sample
        if self.training:
            eps = torch.zeros_like(posterior_var).normal_()
            z = posterior_mean + posterior_var.sqrt() * eps  # reparameterization
        else:
            z = posterior_mean
        theta = torch.softmax(z, dim=-1)
        return theta

    def KL_loss(self, param):
        posterior_mean = param[0]
        posterior_logvar = param[1]

        prior_mean = Variable(self.prior_mean).expand_as(posterior_mean)
        prior_var = Variable(self.prior_var).expand_as(posterior_mean)
        prior_logvar = Variable(self.prior_logvar).expand_as(posterior_mean)
        var_division = posterior_logvar.exp() / prior_var
        diff = posterior_mean - prior_mean
        diff_term = diff * diff / prior_var
        logvar_division = prior_logvar - posterior_logvar
        KL = 0.5 * (
            (var_division + diff_term + logvar_division).sum(1) - self.args.num_topic
        )

        if torch.isinf(KL).sum() > 0 or torch.isnan(KL).sum() > 0:
            import ipdb

            ipdb.set_trace()

        return KL
```

`GNGNNmodel.py`에서 `self.encoder`는 `GNNDir2encoder` class의 instance이다.

class instance를 생성할 때, dot notation을 사용해서 그 instance의 method나 attributes에 access할 수 있다.

`GNNDir2encoder` class에서 `forward()` method가 input에 대해 작동하는 main method로 되어있기 때문이다.

`GNNDir2encoder` class의 `__call__()` method는 forward() method를 암시적으로 호출한다.

그래서 `self.encoder(idx_x, idx_w, x_batch, edge_index, edge_w)`는 `self.encoder.forward(idx_x, idx_w, x_batch, edge_index, edge_w)`랑 동일하다.

`__call__()` method는 instance가 호출될 때 function처럼 실행하는 method다.

`GNNDir2encoder` class는 `nn.Module` class를 상속한다. `nn.Module` class에는 `__call__()` method가 정의되어 있다. 

`nn.Module` 클래스는 `__call__()` 메서드를 정의하고, 이 메서드는 forward() 메서드를 호출합니다. nn.Module 클래스를 상속하는 클래스에서 forward() 메서드를 정의하면, `__call__()` 메서드를 호출하여 해당 클래스의 forward() 메서드를 실행할 수 있습니다.

### Pytorch에서 __call__() method의 용도

`__call__()`을 사용하는 이유는 class의 instance를 함수로 취급해서 다른 함수의 파라미터로 사용하기 위해서라고 한다. 

```python
class IDIOT:
    def subtract(self, a, b):
        return a-b

    __call__ = plus
```

```python
idiot = IDIOT()
idiot(6, 2)   # output: 3
```

하나의 예시를 살펴보자. 

```python
class BinaryClassifier(nn.Module):
    def __init__(self):
        super().__init__()
        self.linear = nn.Linear(1, 5)
        self.sigmoid = nn.Sigmoid

    def forward(self, x):
        return self.sigmoid(self.linear(x))

model = BinaryClassifier()
model(5)
```
여기서 이제 instance인 모델을 데이터와 호출하면 자동으로 `forward()` 함수가 실행된다.

> 근데 왜 `forward()`일까?

이건 torch.nn.Module class 파일을 보면 알 수 있다. (파일: torch/nn/modules/module.py)

```python
    def _call_impl(self, *args, **kwargs):
        forward_call = (self._slow_forward if torch._C._get_tracing_state() else self.forward)
        # If we don't have any hooks, we want to skip the rest of the logic in
        # this function, and just call forward.
        if not (self._backward_hooks or self._backward_pre_hooks or self._forward_hooks or self._forward_pre_hooks
                or _global_backward_pre_hooks or _global_backward_hooks
                or _global_forward_hooks or _global_forward_pre_hooks):
            return forward_call(*args, **kwargs)
        # Do not call functions when jit is used
        full_backward_hooks, non_full_backward_hooks = [], []
        backward_pre_hooks = []
        if self._backward_pre_hooks or _global_backward_pre_hooks:
            backward_pre_hooks = self._get_backward_pre_hooks()

        if self._backward_hooks or _global_backward_hooks:
            full_backward_hooks, non_full_backward_hooks = self._get_backward_hooks()

        if _global_forward_pre_hooks or self._forward_pre_hooks:
            for hook_id, hook in (
                *_global_forward_pre_hooks.items(),
                *self._forward_pre_hooks.items(),
            ):
                if hook_id in self._forward_pre_hooks_with_kwargs:
                    result = hook(self, args, kwargs)  # type: ignore[misc]
                    if result is not None:
                        if isinstance(result, tuple) and len(result) == 2:
                            args, kwargs = result
                        else:
                            raise RuntimeError(
                                "forward pre-hook must return None or a tuple "
                                f"of (new_args, new_kwargs), but got {result}."
                            )
                else:
                    result = hook(self, args)
                    if result is not None:
                        if not isinstance(result, tuple):
                            result = (result,)
                        args = result

        bw_hook = None
        if full_backward_hooks or backward_pre_hooks:
            bw_hook = hooks.BackwardHook(self, full_backward_hooks, backward_pre_hooks)
            args = bw_hook.setup_input_hook(args)

        result = forward_call(*args, **kwargs)
        if _global_forward_hooks or self._forward_hooks:
            for hook_id, hook in (
                *_global_forward_hooks.items(),
                *self._forward_hooks.items(),
            ):
                if hook_id in self._forward_hooks_with_kwargs:
                    hook_result = hook(self, args, kwargs, result)
                else:
                    hook_result = hook(self, args, result)

                if hook_result is not None:
                    result = hook_result

        if bw_hook:
            if not isinstance(result, (torch.Tensor, tuple)):
                warnings.warn("For backward hooks to be called,"
                              " module output should be a Tensor or a tuple of Tensors"
                              f" but received {type(result)}")
            result = bw_hook.setup_output_hook(result)

        # Handle the non-full backward hooks
        if non_full_backward_hooks:
            var = result
            while not isinstance(var, torch.Tensor):
                if isinstance(var, dict):
                    var = next((v for v in var.values() if isinstance(v, torch.Tensor)))
                else:
                    var = var[0]
            grad_fn = var.grad_fn
            if grad_fn is not None:
                for hook in non_full_backward_hooks:
                    grad_fn.register_hook(_WrappedHook(hook, self))
                self._maybe_warn_non_full_backward_hook(args, result, grad_fn)

        return result

    __call__ : Callable[..., Any] = _call_impl
```

`__call__` method는 instance가 호출됐을 때 실행되니까 함수인 `_call_impl`의 return 값이 반환되는 것이다. 그리고 `_call_impl` method의 첫 번째 줄을 보면 `self.forward`를 찾을 수 있다.

```python
forward_call = (self._slow_forward if torch._C._get_tracing_state() else self.forward)
```

추적(tracing) 상태인지 확인하는데 사용됩니다. 추적 상태에서는 `_slow_forward()` 메서드를 호출하고, 추적 상태가 아니면 forward() 메서드를 호출합니다.

`_slow_forward()` 메서드는 추적 상태에서 사용되며, 추적 상태에서는 모델의 순전파를 추적하기 위해 사용됩니다. 추적 상태가 아닌 경우에는 `_slow_forward()` 메서드 대신 forward() 메서드를 호출하여 모델의 순전파를 실행합니다.

따라서, forward_call은 추적 상태에서는 `_slow_forward()` 메서드를 호출하고, 추적 상태가 아니면 forward() 메서드를 호출하는데 사용됩니다.

결국 `forward_call`은 호출 가능한(callable) instance다. tracing 상태 여부에 따라 `_slow_forward()`를 반환할 수 있지만 지금은 `forward()` method를 반환한다고 생각하면 된다. 그렇게 해서 아래와 같이 작동한다.

```python
result = forward_call(*args, **kwargs)
```

다시 코드를 들여다보면 왜 `GNNDir2encoder` class의 `forward` method가 실행됐는지 알 수 있다.

```python
class GNNDir2encoder(nn.Module):
    def __init__(self, args, word_vec):
        super(GNNDir2encoder, self).__init__()
        ...
    ...

    def forward(self, idx_x, idx_w, x_batch, edge_index, edge_weight):
        print(idx_x, idx_x.size(), self.word_vec, self.word_vec.size())
        x = self.word_vec[idx_x]
        diag = (
            torch.ones(2, idx_x.size(0), dtype=torch.long, device=idx_x.device).cumsum(
                dim=-1
            )
            - 1
        )
        edge_index_exp = torch.cat([edge_index, diag], dim=-1)
        diag_w = (
            torch.ones(idx_x.size(0), dtype=torch.float, device=idx_x.device) * idx_w
        )
        edge_weight_exp = torch.cat([edge_weight, diag_w], dim=0)

        enc1 = torch.tanh(
            self.bn_gnn1(self.enc1_gnn1(x, edge_index_exp, edge_weight=edge_weight_exp))
        )

        if torch.isnan(enc1).sum() > 0:
            import ipdb

            ipdb.set_trace()
        enc1 = torch.cat([enc1, x], dim=-1)
        enc2 = torch.sigmoid(self.enc2_fc1(enc1)) * torch.tanh(self.enc2_fc2(enc1))
        size = int(x_batch.max().item() + 1)
        enc2 = scatter(enc2, x_batch, dim=0, dim_size=size, reduce="sum")  # B*enc_h
        enc2d = self.enc2_drop(enc2)

        mean = self.mean_bn(self.mean_fc(enc2d))  # posterior mean
        logvar = self.logvar_fc(enc2d)  # posterior log variance

        word_embed = torch.cat([enc1, enc2[x_batch]], dim=-1)
        phi = torch.softmax(self.phi_fc(word_embed), dim=-1)  # (B*max_len)*num_topic

        param = (mean, logvar)
        if torch.isnan(mean).sum() > 0:
            import ipdb

            ipdb.set_trace()
        return param, phi
```
`GDGNNModel` class 모델은 `nn.Module`를 상속받고, 생성될 때 `__init__()` 함수가 자동으로 호출되어 객체가 갖는 속성 값을 초기화한다.

`super()` 함수는 `nn.Module` class의 속성들을 가지고 초기화된다.
(속성: 클래스 내부에 포함된 함수나 변수를 의미)

`forward()` 함수는 forward 연산을 진행시키는 함수다. 

진행 순서를 정리하면,
> self.encoder() $\rightarrow$ nn.Module $\rightarrow$ self.forward()

순서인 것이다.

Reference: 
- [꾸준한 성장일기](https://seungseop.tistory.com/7)
- [PyTorch](https://pytorch.org/docs/stable/generated/torch.nn.Module.html)



## forward method in `GNNDir2encoder` class

이제 `GNNDir2encoder` class의 `forward` method를 보자.  
(주석은 내용을 이해할 수 있도록 내가 추가해둔 것이다.)

```python
    def forward(self, idx_x, idx_w, x_batch, edge_index, edge_weight):

        # x: Each word embedding matrix for a specific subset of words in the vocabulary.
        x = self.word_vec[idx_x]  # Matrix dimension: N * nw

        # Index Matrix that has dimensions 2 by n.
        # dtype(data type) argument specifices 64-bit integer (signed) = torch.long
        # device argument specifies that the tensor should be created on the same device as the idx_x tensor.
        # cumsum method computes the cumulative sum wof the elements along the specified dimension. Since the tensor is a tensor of ones, the result of the cumulative sum is a tensor where each element is equal to its index along the specified dimension. Each element is equal to its index along the last dimension. This is because the cumulative sum of a sequence of ones is subtracted by -1
        # [1, 2, 3]
        # [1, 2, 3]
        # Self-loops are edges that connect a node to itself, and they are represented by the diagonal elements of the adjacency matrix.
        diag = (
            torch.ones(2, idx_x.size(0), dtype=torch.long, device=idx_x.device).cumsum(dim=-1) - 1
        )

        # Concatenating edge_index and diag is to add self-loops to the graph.
        # Diag represents the diagonal indices of the adjacency matrix of the graph
        # So edge_index_exp is the adjacency matrix, which is reponsible for encoding the graph structure into a feature representation.
        edge_index_exp = torch.cat([edge_index, diag], dim=-1)  # Add diagonal line

        # This variables are performing similar processes to diag and edge_index_exp in the code above.
        diag_w = (
            torch.ones(idx_x.size(0), dtype=torch.float, device=idx_x.device) * idx_w
        )
        edge_weight_exp = torch.cat([edge_weight, diag_w], dim=0)

        # GNN layer consists of two parts: enc1_gnn1 layer and bn_gnn1 layer
        # The enc1_gnn1 layer is a graph convolutional layer. The enc1_gnn1 layer operates on undirected graphs, where each edge is represented by two directed edges in opposite directions. self.enc1_gnn1() returns the output of the first Graph Convolutional Network (GCN) layer
        # The output of the enc1_gnn1 layer is passed through the bn_gnn1 layer, which applies batch normalization to the output. Batch normalization is a technique that helps to improve the stability and performance of neural networks by normalizing the input to each layer.
        # self.bn_gnn1 = nn.BatchNorm1d(args.nw): Batch Normalization
        # self.enc1_gnn1 = GraphConv(input_size, args.nw, bias=True): Graph Convolutional Network
        enc1 = torch.tanh(
            self.bn_gnn1(self.enc1_gnn1(x, edge_index_exp, edge_weight=edge_weight_exp))
        )   

        # Check if there are any NaN values
        # Then, the code enters a debugging mode
        if torch.isnan(enc1).sum() > 0:
            import ipdb

            ipdb.set_trace()

        # In next layer 
        # enc1: (h_1, h_2, ..., h_N).
        # enc1 dim: (N, nw+input_size), this is the because the size of allwords is same in each layer
        # enc2: h_G
        # enc2: equation (12), in thesis and next layer
        # enc2 dim: (N, enc_nh)
        enc1 = torch.cat([enc1, x], dim=-1)
        enc2 = torch.sigmoid(self.enc2_fc1(enc1)) * torch.tanh(self.enc2_fc2(enc1))
        size = int(x_batch.max().item() + 1)   # size = B

        # Dropout: self.enc2_drop = nn.Dropout(0.2) for regularization
        enc2 = scatter(enc2, x_batch, dim=0, dim_size=size, reduce="sum")  # B*enc_nh
        enc2d = self.enc2_drop(enc2)

        # equation (13) in thesis
        # mean and logvar are the parameters of the variational distribution
        # self.mean_fc = nn.Linear(args.enc_nh, args.num_topic)
        # self.logvar_fc = nn.Linear(args.enc_nh, args.num_topic)
        # logvar = log(sigma^2) and the resason why to use enc2d is that we dropout some nodes
        # Be catious that the parameters are calculated by nn.Linear randomly. It means that the parameters were initially set to random values and then they were updated during the training process.
        mean = self.mean_fc(enc2)  # posterior mean
        logvar = self.logvar_fc(enc2d)  # posterior log variance

        # The reason why to calculate the word_embed is that we need to calculate the phi
        # word_embed dim: (N, nw+input_size+enc_nh)
        # phi dim: (B, max_len, num_topic)
        word_embed = torch.cat([enc1, enc2[x_batch]], dim=-1)
        phi = torch.softmax(self.phi_fc(word_embed), dim=-1)
        param = (mean, logvar)

        # Check if there are any NaN values
        if torch.isnan(mean).sum() > 0:
            import ipdb

            ipdb.set_trace()
        return param, phi
```

# forward method in `GDGNNmodel` class

이제 parameter가 반환됐으니까 그것을 바탕으로 분포를 구하고 다시 decoding을 해야 한다.

```python
        # KL loss of GNN encoder
        KL1 = self.encoder.KL_loss(param)  # (B)
```

`self.encoder`는 `GNNDir2encoder` class의 instance니까 KL_loss를 이해하기 위해서는 `GNNDir2encoder` class를 봐야한다 

```python
    ## KL loss of GNN encoder
    #
    # @param param: parameters of the variational distribution
    #
    # calculate the KL divergence between the variational distribution and the prior distribution 
    def KL_loss(self, param):
        posterior_mean = param[0]
        posterior_logvar = param[1]

        # prior_mean, prior_var, prior_logvar are the parameters of the prior distribution
        # The reason to use expand_as() method is to expand the size of the tensor to the same size as the tensor of the posterior distribution
        prior_mean = Variable(self.prior_mean).expand_as(posterior_mean)
        prior_var = Variable(self.prior_var).expand_as(posterior_mean)
        prior_logvar = Variable(self.prior_logvar).expand_as(posterior_mean)

        # the reason to use .exp() method is to calculate the variance of the posterior distribution. That's because the posterior_logvar is the log of the variance of the posterior distribution
        var_division = posterior_logvar.exp() / prior_var   # sigma^2/sigma_0^2

        # diff_term = (mu - mu_0)^2 / sigma_0^2
        diff = posterior_mean - prior_mean
        diff_term = diff * diff / prior_var
        logvar_division = prior_logvar - posterior_logvar

        # KL = 0.5 * (sigma_0^2/sigma^2 + (mu - mu_0)^2/sigma_0^2 + log(sigma^2/sigma_0^2) - K)
        KL = 0.5 * (
            (var_division + diff_term + logvar_division).sum(1) - self.args.num_topic
        )

        if torch.isinf(KL).sum() > 0 or torch.isnan(KL).sum() > 0:
            import ipdb

            ipdb.set_trace()

        return KL
```
## other KL division

Variational Inference를 진행하기 위해서 KL distance를 구하고 있지만 위에서 구한 KL term 말고도 하나 더 구해야 하는 KL term이 존재한다. 나머지를 이제 구한다.

```python
        # KL(z) term
        # param: (mean, logvar)
        # theta: the proportions of the topics for each document
        # calculate the KL divergence to sum over the all words' KL divergence
        # \sum_{n=1}^{N_d} \phi_{d, n} \dot (\log \phi_{d, n}): first term in equation (S14) in appendix
        if self.args.prior_type in ["Dir2", "Gaussian"]:
            theta = self.encoder.reparameterize(param)
            KL2 = torch.sum(
                phi * ((phi / (theta[x_batch] + 1e-10) + 1e-10).log()), dim=-1
            )  # (B*len)

        # \sum_{n=1}^{N_d} \phi_{d, n} \dot (\log \phi_{d, n} - E_q [\log \theta_d]): first and second term in equation (S14) in appendix
        if self.args.prior_type == "Dir":
            gamma = param[0]  # mean
            Elogtheta = Expectation_log_Dirichlet(gamma)  # (B, K)
            KL2 = torch.sum(phi * ((phi + 1e-10).log() - Elogtheta[x_batch]), dim=-1)
        KL2 = myscattersum(idx_w * KL2, x_batch, size=size)  # B

        if (
            not self.args.eval
            and self.args.prior_type in ["Dir2", "Gaussian"]
            and self.args.iter_ < self.args.iter_threahold
        ):
            phi = theta[x_batch]
```

이제는 모든 결과를 `self.encoder`를 통해서 구하는 것을 알 수 있다. 다시 `GNNDir2encoder` class로 돌아가 reparameterize method를 보면

```python
    def reparameterize(self, param):
        posterior_mean = param[0]
        posterior_var = param[1].exp()   # sigma^2: variance of the posterior distribution
        
        # take sample
        if self.training:
            eps = torch.zeros_like(posterior_var).normal_()  # eps: random noise from the standard normal distribution
            z = posterior_mean + posterior_var.sqrt() * eps  # reparameterization
        else:
            z = posterior_mean
        theta = torch.softmax(z, dim=-1)  # the proportions of the topics for each document
        return theta
```

`Expectation_log_Dirichlet` 함수의 경우 `GDGNNmodel` class에 정의되어 있다.

```python
def Expectation_log_Dirichlet(gamma):
    # E_{Dir(\theta| \gamma)}[\log \theta] = \Psi(\gamma) - \Psi(|\gamma|)
    return torch.digamma(gamma) - torch.digamma(gamma.sum(dim=-1, keepdim=True))
```

```python
        ##### generate structure
        W = self.get_W()  # (K, K)  after sigmod
        log_PW = (W).log()  # K,K
        log_NW = (1 - W).log()  # K,K
        p_phi = phi[edge_index, :]  # 2,B*len_edge, K
        p_edge = torch.sum(
            torch.matmul(p_phi[0], log_PW) * p_phi[1], dim=-1
        )  # B*len_edge
        p_edge = myscattersum(p_edge, edge_id_batch, size=size)  # B

        neg_mask, neg_mask2 = adj_mask(x_batch, device=idx_w.device)

        neg_mask[edge_index[0], edge_index[1]] = 0

        n_edge = torch.matmul(torch.matmul(phi, log_NW), phi.T)  # B*len, B*len
        n_edge1 = torch.sum(n_edge * neg_mask, dim=-1)  # B*len
        n_edge1 = myscattersum(n_edge1, x_batch, size=size)  # B

        tmp = torch.ones_like(
            edge_id_batch, dtype=torch.float, device=edge_id_batch.device
        )
        NP = myscattersum(tmp, edge_id_batch, size=size)  # B

        NN = myscattersum(torch.sum(neg_mask, dim=-1), x_batch, size=size)  # B

        recon_structure = -(p_edge + n_edge1 / (NN + 1e-6) * NP)

        # #### recon_word
        beta = self.get_beta()  # (K, V)

        q_z = torch.distributions.RelaxedOneHotCategorical(
            temperature=self.args.temp, probs=phi
        )
        z = q_z.rsample([self.args.num_samp])  # (ns, B*len, K)

        z = hard_z(z, dim=-1)

        beta_s = beta[:, idx_x]  # K*(B*len) !TODO idx_x or idx_x
        beta_s = beta_s.permute(1, 0)  # (B*len)*K
        recon_word = (phi * (beta_s + 1e-6).log()).sum(-1)  # (B*len)
        recon_word = -myscattersum(idx_w * recon_word, x_batch)  # B

        ######recon_edge
        beta_edge = self.get_beta_edge(weight=False)

        edge_w = torch.unsqueeze(edge_w, 0)  # (1, B*len_edge)
        beta_edge_s = (beta_edge[:, edge_id]).permute(1, 0)  # (B*len_edge,K)

        z_edge_w = z[:, edge_index, :]  # (ns,2,B*len_edge, K)
        mask = z_edge_w > self.maskrate
        z_edge_w = z_edge_w * mask

        edge_w = edge_w.unsqueeze(-1)  # (1, B*len_edge, 1)
        z_edge_w = edge_w * z_edge_w.prod(dim=1)  # (ns,B*len_edge, K)
        beta_edge_s = beta_edge_s.unsqueeze(0) * z_edge_w  # (ns,B*len_edge,K)

        beta_edge_s = beta_edge_s.permute(1, 0, 2)  # (B*len_edge, ns,K)
        # beta_edge_s = myscattersum(beta_edge_s, edge_id_batch,size=size)  # (B, ns,K)
        beta_edge_s = scatter(
            beta_edge_s, edge_id_batch, dim=0, dim_size=size, reduce="sum"
        )
        beta_edge_s = beta_edge_s.permute(1, 0, 2)  # (ns,B,K)

        z_edge_w = z_edge_w.permute(1, 0, 2)  # (B*len,ns,K)
        # z_edge_w = myscattersum(z_edge_w, edge_id_batch,size=size)  # (B,ns,K)
        z_edge_w = scatter(z_edge_w, edge_id_batch, dim=0, dim_size=size, reduce="sum")
        z_edge_w = z_edge_w.permute(1, 0, 2)  # (ns,B,K)
        recon_edge = (
            -(torch.clamp_min(beta_edge_s, 1e-10) / torch.clamp_min(z_edge_w, 1e-10))
            .log()
            .sum(-1)
            .mean(0)
        )

        if (
            not self.args.eval
            and self.args.prior_type in ["Dir2", "Gaussian"]
            and self.args.iter_ < self.args.iter_threahold
        ):

            loss = recon_word + KL1
        else:
            loss = recon_edge + recon_word + KL1 + KL2 + recon_structure

        if (
            torch.isnan(loss).sum() > 0
            or loss.mean() > 1e20
            or torch.isnan(recon_structure).sum() > 0
        ):
            ipdb.set_trace()

        outputs = {
            "loss": loss.mean(),
            "recon_word": recon_word.mean(),
            "recon_edgew": recon_edge.mean(),
            "recon_structure": recon_structure.mean(),
            "p_edge": p_edge.mean(),
            "KL1": KL1.mean(),
            "KL2": KL2.mean(),
        }

        return outputs

```