# 移动端处理

## 问题

### 一、页面添加

1. 维修设备时，针对交换机设备提供修改跳线的界面 √
2. 维修设备时，针对电箱设备提供修改电闸控制线路的界面 √

### 二、页面跳转逻辑

1. 设备维修的界面跳转逻辑 √ （主菜单设备信息 -> 本次维修）
2. 设备问题上报的界面跳转逻辑 √ （主菜单设备信息 -> 右上角菜单-问题上报 或者 扫码 -> 右上角菜单 -> 问题上报）
3. 设备备件申请的界面跳转逻辑 √ （主菜单设备信息 -> 右上角菜单-配件申请 或者 扫码 -> 右上角菜单 -> 配件申请）
4. 设备报修的页面跳转逻辑 没有该页面

### 三、接口调试
各页面与后台的接口调试

## 代码修改

### 一、页面添加

### 二、页面跳转逻辑

1. 首页设备信息跳转到设备信息页面（原来是搜索页面）

```java
4 -> {context?.startActivity(Intent(context, SearchActivity::class.java))}
=>
4 -> {context?.startActivity(Intent(context, SearchActivity::class.java))}
```

### 三、接口调试






























# 项目代码习惯

<br>

## 前端部分

<br>

### HTML部分
1. 与codeDict相关的内容都通过映射完成，不要向table中添加新元素

### JavaScript部分
1. 外部函数引用顺序:
    1. Vue官方库
    2. utils、router中的库
    3. api/codeDict中的库
    4. 其他api中的库，按首字母排序
2. getData需要包含所有数据的获取（数据量大的在前）
3. api调用，必须包含code判断，最外层调用需要进行catch捕获
4. 添加操作初始化即可
5. 修改操作需要先初始化再赋值

<br>

## 后端


<br>

# 代码块

### 一、table中数据映射

```html


```

### 二、form中数据

```html
<!-- html -->
<el-form-item label="巡检周期单位">
    <el-input 
        :model-value="codeDictReplace(patrolInspectionMissionCycleUnit, 
            patrolInspectionInfo.patrolInspectionCycleUnit)" 
        readonly>
    </el-input>
</el-form-item>
```

### 三、table中颜色标签映射
```html
<el-table-column
    prop="spareApplyStatusID"
    label="备件申请状态标识"
    align="center">
    <template #default="scope">
       <el-tag :type="statusTagColor(scope.row.spareApplyStatusID)">
            {{codeDictReplace(spareApplicationOrderStatus, scope.row.spareApplyStatusID)}}
        </el-tag>
    </template>
</el-table-column>
```

```javascript
// 数据替换
const codeDictReplace = (codeDict, id) => {
    if(typeof(codeDict) != undefined){
        const object = codeDict.find((item) => {
            return item.id == id;
        })
        if(object == null) return "";
        else return object.value;
    }
};

// 状态颜色
const statusTagColor = (value) => {
    if(value == 0){
        return "";
    }else if(value == 1){
        return "success";
    }else if(value == 2){
        return "danger"; 
    }
};
```



