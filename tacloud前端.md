
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

| 名称 | 类型 | 描述 | 值 | 默认值 |
| --- | --- | --- | --- | --- |
| submitIds | string | 需要传递到后台的对象id或容器id, 多个id可以用","隔开 | - | - |
| url | string | 提交的地址 | - | - |
| parameter | Object | 入参 json格式对象，例如: `{"dto['aac001']":"1000001", "dto['aac002']":"01"}` | - | - |
| onSubmit | Function | 提交前手动检查，如果返回false将不再提交, 必须返回true或false | - | - |
| autoValidate | Boolean | 默认false 是否自动调用`Base.validateForm`对ids对象进行校验，如果校验失败将不再提交 | - | false |
| succCallback | Function | callback 返回业务成功后的回调，入参返回的为json对象和XMLHttpRequest对象。<br>例如：`function(data){alert(data.lists.grid2.list[0].aac003)}`，<br>其中data为返回的json数据，grid2是在action中绑定的id(setList('grid2', list))，通常是jsp中datagrid的id. | - | - |
| failCallback | Function | failCallback 业务失败回调，入参返回的为json对象和XMLHttpRequest对象 | - | - |