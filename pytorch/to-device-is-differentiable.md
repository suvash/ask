# Pytorch tensor `.to(device)` is differentiable

The method `to(device)` is used to "move" a tensor to the selected device.

What this means in practice is that, the tensor is copied to the device and the tensor on that new device is returned. This a not a real* move in the sense that no new computation graph is created. Instead, the "move" actually introduces another tensor in the same computation graph.

```python
x = torch.rand(4, requires_grad=True)
# x = tensor([0.7432, 0.3016, 0.8133, 0.7630], requires_grad=True)

y = x.to('cuda:0')
# x = tensor([0.7432, 0.3016, 0.8133, 0.7630], requires_grad=True)
# y = tensor([0.7432, 0.3016, 0.8133, 0.7630], device='cuda:0', grad_fn=<ToCopyBackward0>)
```

This makes sense, as one can then start out with the data on one device(say CPU), then "move" it to another device for faster operations(say cuda). The data can further be moved to another device and so on. Because all of these moves are differentiable, we can eventually call `.backward()` and get the gradient to be calculated all the way back to the origin.

This is actually pretty neat as it allows for using multiple devices without detaching the computation graph.

```python
# setup
x = torch.randn(1, 1, requires_grad=True)
# tensor([[0.4967]], requires_grad=True)
lin_on_cpu = torch.nn.Linear(1, 1)
lin_on_gpu = torch.nn.Linear(1, 1).cuda()

# forward
out_on_cpu = lin_on_cpu(x)
# tensor([[-0.7594]], grad_fn=<AddmmBackward0>)
out_on_gpu = out_on_cpu.to('cuda')
# tensor([[-0.7594]], device='cuda:0', grad_fn=<ToCopyBackward0>)
out_on_gpu = lin_on_gpu(out_on_gpu)
# tensor([[1.2004]], device='cuda:0', grad_fn=<AddmmBackward0>)

# backward
out_on_gpu.mean().backward()

# valid gradient at source tensor
print(x.grad)
# tensor([[0.4209]])
```

The important thing to remember is that since no new computation graph is created at any of these moves, only the original tensor can collect the gradients. Every other operation (including the moves) is adding to the computation graph itself.

## Source
- https://discuss.pytorch.org/t/no-gradient-on-cuda/144807
- https://discuss.pytorch.org/t/tensor-grad-none-for-tensors-on-gpu/24838
- https://pytorch.org/docs/stable/generated/torch.Tensor.to.html#torch.Tensor.to
