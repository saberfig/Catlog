## HDF5

### 文件介绍

HDF5可以看作一个键值对列表，不过这里的值是一个矩阵

https://blog.csdn.net/Hanx09/article/details/107511115

### 使用Python操作HDF5文件

```python
import h5py
# 读取HDF5文件
file_read = h5py.File('file_path', 'r')
# 查看所有keys
keys = file_read.keys()
# 查看对应值（矩阵）
data = file_read['key_name'][:]
file_read.close()

# 写入HDF5文件
file_write = h5py.File('file_path', 'w')
file_write['key_name'] = data
file_write.close()
```