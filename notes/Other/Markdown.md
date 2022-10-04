## 表格

### 固定列宽

> 1. 指定列宽
```
|a|
|---|
|<div style="width: 150pt">text|

```

> 2. 全局style（谷歌不识别）
```
<style>
table th:first-of-type {
    width: 4cm;
}
table th:nth-of-type(2) {
    width: 150pt;
}
</style>

| a | b |
|---|---|
| 列宽 = 4 cm| 列宽 = 150 pt|
```