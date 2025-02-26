# TaCloud前端框架规范说明

## 基础组件

TaCloud前端框架是一套基于jQuery的企业级UI框架，提供了丰富的组件和API，用于快速构建企业级应用。

### 1. 页面布局组件

- **ta:box**: 基础容器组件，可设置`fit="true"`使其自适应父容器
- **ta:panel**: 面板组件，用于内容分组展示
- **ta:fieldset**: 表单字段分组，可通过`cols`属性设置每行显示的字段数
- **ta:tabs**: 选项卡组件，包含多个`ta:tab`子组件
- **ta:buttonLayout**: 按钮布局容器

### 2. 表单组件

- **ta:text**: 文本输入框
- **ta:date**: 日期选择器，支持`showSelectPanel`、`dateYear`等配置
- **ta:selectInput**: 下拉选择框，可通过`collection`属性绑定数据字典
- **ta:button**: 按钮组件，支持`key`、`onClick`、`isok`等属性
- **ta:hquery**: 高级查询组件

### 3. 数据表格

- **ta:datagrid**: 数据表格组件
  - `forceFitColumns`: 列宽自适应
  - `fit`: 表格大小自适应
  - `columnFilter`: 启用列筛选
  - `haveSn`: 显示序号列
- **ta:datagridItem**: 表格列定义
  - `hiddenColumn`: 隐藏列
  - `formatter`: 自定义格式化函数
  - `collection`: 绑定数据字典
  - `dataAlign`: 数据对齐方式
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
- `parameter`: 入参JSON格式对象
- `onSubmit`: 提交前手动检查函数
- `autoValidate`: 是否自动校验表单
- `succCallback`: 业务成功回调函数
- `failCallback`: 业务失败回调函数

#### 2. 窗口操作

TaCloud提供了两种不同的窗口打开方式，用于不同场景：

```javascript
// 打开模态窗口，用于数据编辑、详情查看等需要用户交互的场景
Base.openWindow(id, title, url, parameter, width, height, buttons, callback, modal, closeCallback)

// 打开标签页，用于导航到系统的其他功能模块
Base.openTabMenu(id, title, url, parameter)
```

#### 3. 消息提示

```javascript
Base.alert(message, callback)
Base.confirm(message, callback)
```

#### 4. 表格操作

```javascript
Base.getGridSelectedRows(gridId)  // 获取表格选中行
Base.setTabTitle(tabId, title)    // 设置选项卡标题
```

#### 5. 值操作

```javascript
Base.getValue(id)    // 获取元素值
Base.setValue(id, value)  // 设置元素值
```

## 开发规范

### 1. 命名规范

- 函数名使用驼峰命名法，建议以`fn`开头，如`fnQuery`、`fnAddMonthlyReport`
- 组件ID应具有语义性，如`queryPnl`、`hi9bGrid`
- 变量名使用驼峰命名法，如`selectedRows`、`isDelete`

### 2. 事件处理

- 按钮点击事件通过`onClick`属性绑定
- 事件处理函数应进行必要的参数验证
- 表格行操作通常通过自定义formatter函数实现

### 3. 数据交互

- 使用`Base.submit`方法进行数据提交和获取
- 后端接口通常使用`Controller!method.do`格式
- 批量操作时，应先获取选中记录，进行必要的状态检查

### 4. 页面导航

- 使用`Base.openWindow`打开模态窗口进行数据编辑、详情查看等操作
- 使用`Base.openTabMenu`打开新的功能模块页面
- 窗口关闭后通常需要刷新数据列表

### 5. 用户体验

- 操作前应进行必要的提示和确认
- 操作成功后应给予反馈并刷新相关数据
- 对于批量操作，应检查选中记录的状态是否满足操作条件

## 最佳实践

1. 页面加载完成后初始化布局和数据
   ```javascript
   $(document).ready(function () {
       $("body").taLayout();
       fnQuery();
   });
   ```

2. 查询数据并更新UI
   ```javascript
   function fnQuery() {
       Base.submit("queryPnl", "controller!queryData.do", null, null, false, function(data) {
           // 处理返回数据
       });
   }
   ```

3. 打开模态窗口进行数据编辑
   ```javascript
   function fnEdit(row) {
       Base.openWindow("edit", "编辑", "controller!openEditPage.do", 
           {"dto.id": row.id}, "90%", "80%", null, null, true, function() {
           fnQuery(); // 关闭窗口后刷新数据
       });
   }
   ```

4. 打开新的功能模块页面
   ```javascript
   function fnAddMonthlyReport() {
       Base.openTabMenu("monthlyReportAdd", "月度汇报", "path/controller.do");
   }
   ```

5. 批量操作前进行数据验证
   ```javascript
   function fnBatchSubmit() {
       var selectedRows = Base.getGridSelectedRows("gridId");
       if (selectedRows.length === 0) {
           Base.alert("请至少选择一条记录");
           return;
       }
       // 继续处理
   }
   ```

以上规范和最佳实践可以帮助开发人员更好地使用TaCloud前端框架，提高开发效率和代码质量。
