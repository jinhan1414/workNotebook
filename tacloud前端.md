# TaCloud前端框架规范说明

## 基本组件

TaCloud前端框架是一套基于jQuery的UI框架，提供了丰富的组件和工具函数，主要包括以下几个方面：

### 1. 页面布局组件

- **ta:box**: 容器组件，可设置`fit="true"`使其自适应父容器大小
- **ta:panel**: 面板组件，可用于分组展示内容
- **ta:fieldset**: 表单分组组件，可通过`cols`属性设置每行显示的字段数
- **ta:buttonLayout**: 按钮布局容器

### 2. 表单组件

- **ta:text**: 文本输入框
- **ta:date**: 日期选择器，可通过`showSelectPanel`、`issue`、`dateYear`等属性配置
- **ta:button**: 按钮组件，可设置`key`、`onClick`等属性
- **ta:select**: 下拉选择框

### 3. 数据表格

- **ta:datagrid**: 数据表格组件
  - `forceFitColumns`: 列宽自适应
  - `fit`: 表格大小自适应
  - `columnFilter`: 启用列筛选
  - `haveSn`: 显示序号列
- **ta:datagridItem**: 表格列定义
  - `hiddenColumn`: 隐藏列
- **ta:dataGridToolPaging**: 表格分页工具

## 核心API

### Base对象

Base对象是TaCloud框架的核心，提供了丰富的工具方法：

#### 1. 表单提交

```javascript
Base.submit(submitIds, url, parameter, onSubmit, autoValidate, succCallback, failCallback)
```

**参数说明**:
- `submitIds`: 需要传递到后台的对象id或容器id，多个id可用逗号隔开
- `url`: 提交的地址
- `parameter`: 入参JSON格式对象，如`{"dto['aac001']":"1000001"}`
- `onSubmit`: 提交前手动检查函数，返回false将不再提交
- `autoValidate`: 是否自动校验表单
- `succCallback`: 业务成功回调函数
- `failCallback`: 业务失败回调函数

#### 2. 窗口操作

```javascript
Base.openWindow(options)
```

**options参数**:
- `url`: 窗口内容URL
- `title`: 窗口标题
- `width`: 窗口宽度
- `height`: 窗口高度
- `buttons`: 窗口按钮配置
- `onLoad`: 窗口加载完成回调
- `onClose`: 窗口关闭回调

#### 3. 消息提示

```javascript
Base.alert(message, callback)
Base.confirm(message, callback)
```

#### 4. 表格操作

```javascript
Base.getGridSelectedRows(gridId)  // 获取表格选中行
```

## 开发规范

### 1. 命名规范

- 函数名使用驼峰命名法，如`fnGetList`、`fnAddMonthlyReport`
- 组件ID应具有语义性，如`addMonthlyReport`、`batchExport`

### 2. 事件处理

- 按钮点击事件通过`onClick`属性绑定
- 事件处理函数应进行必要的参数验证

### 3. 数据交互

- 使用`Base.submit`方法进行数据提交
- 后端接口通常使用`Controller!method.do`格式
- 批量操作时，应先获取选中记录，进行必要的状态检查

### 4. 用户体验

- 操作前应进行必要的提示和确认
- 操作成功后应给予反馈并刷新相关数据
- 对于批量操作，应检查选中记录的状态是否满足操作条件

## 最佳实践

1. 使用`Base.submit`进行表单提交和数据获取
2. 使用`Base.openWindow`打开弹窗进行数据编辑
3. 操作前进行数据验证和用户确认
4. 操作成功后刷新相关数据
5. 合理使用回调函数处理异步操作
### Base.submit

`Base.submit(submitIds, url, parameter, onSubmit, autoValidate, succCallback, failCallback)`

异步提交表单，action必须返回JSON或者null，此方法不能用于页面跳转，通常用于返回表格数据。能够自动校验表单，能够对后台返回的json进行自动处理。处理如下：

1. 有消息自动提示（根据不同类型提示不同类型的提示框）
2. 如果有返回自由格式的内容自动给页面对应的输入对象赋值
3. 如果有列表的值，自动给所有列表更新列表内容
4. 如果有对表单输入对象或按钮的控制内容，自动根据数据进行控制
5. 如果有校验不通过的自动设置不通过的输入对象为校验失败的样式，同时第一个元素获取焦点。
6. 如果有设置焦点的数据，自动给数据对应的对象获取焦点。

#### 参数 :

| 名称           | 类型       | 描述                                                                                                                                                                                                     | 值   | 默认值  |
| ------------ | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --- | ---- |
| submitIds    | string   | 需要传递到后台的对象id或容器id, 多个id可以用","隔开                                                                                                                                                                        | -   | -    |
| url          | string   | 提交的地址                                                                                                                                                                                                  | -   | -    |
| parameter    | Object   | 入参 json格式对象，例如: `{"dto['aac001']":"1000001", "dto['aac002']":"01"}`                                                                                                                                    | -   | -    |
| onSubmit     | Function | 提交前手动检查，如果返回false将不再提交, 必须返回true或false                                                                                                                                                                 | -   | -    |
| autoValidate | Boolean  | 默认true 是否自动调用`Base.validateForm`对ids对象进行校验，如果校验失败将不再提交                                                                                                                                                 | -   | true |
| succCallback | Function | callback 返回业务成功后的回调，入参返回的为json对象和XMLHttpRequest对象。<br>例如：`function(data){alert(data.lists.grid2.list[0].aac003)}`，<br>其中data为返回的json数据，grid2是在action中绑定的id(setList('grid2', list))，通常是jsp中datagrid的id. | -   | -    |
| failCallback | Function | failCallback 业务失败回调，入参返回的为json对象和XMLHttpRequest对象                                                                                                                                                      | -   | -    |

#### 示例代码

```js
Base.submit("form", "hi93Controller!saveMonthlyReportData.do");
```