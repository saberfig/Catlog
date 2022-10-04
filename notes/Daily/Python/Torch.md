## 模型的读取与使用

### 常见问题

```python
# 问题
RuntimeError: Error(s) in loading state_dict for DataParallel:
        Missing key(s) in state_dict: "module.acpnet.emb_nn.conv1.weight"

# 解决方法
net.load_state_dict(state_dict)
net.load_state_dict(state_dict, strict=False)
```
