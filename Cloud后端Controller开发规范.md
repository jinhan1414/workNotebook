
# TaCloud后端Controller开发规范

## 1. 基础结构

### 1.1 类定义与注解
```java
@RequestMapping("模块名")
@Controller
public class XXXController extends ArchivesBaseController {
    // 控制器内容
}
```

### 1.2 方法定义
```java
@RequestMapping("controllerName!methodName.do")
public String methodName() {
    // 方法实现
    return JSON; // 或返回JSP页面路径
}
```

## 2. 数据获取与处理

### 2.1 获取请求参数
- 使用`getDto()`获取请求参数，返回`TaParamDto`对象
- 使用`dto.getAsString()`, `dto.getAsInteger()`等方法获取具体参数值
- 使用`ValidateUtil.isEmpty()`和`ValidateUtil.isNotEmpty()`检查参数

```java
TaParamDto dto = getDto();
String paramValue = dto.getAsString("paramName");
if (ValidateUtil.isEmpty(paramValue)) {
    // 处理参数为空的情况
}
```

### 2.2 获取用户信息
- 通过`dto.getUser()`获取当前登录用户信息
- 使用`sysUser.getUserId()`, `sysUser.getOrgId()`等方法获取用户属性

```java
ISysUser sysUser = dto.getUser();
String userId = sysUser.getUserId();
String orgId = sysUser.getOrgId();
```

### 2.3 获取系统时间
- 使用`archivesBaseBpo.getSysDate()`获取系统当前时间
- 不要使用`new Date()`或自定义方法获取时间

```java
dto.put("aae036", archivesBaseBpo.getSysDate()); // 经办时间
```

## 3. 数据库操作

### 3.1 查询操作
- 使用`queryForPageWithNone`进行分页查询
- 使用`archivesBaseBpo.getResultListBySqlIdAndParamDto`进行列表查询

```java
// 分页查询
queryForPageWithNone("gridId", "sqlId", getDto(), getDto());

// 列表查询
List<Map<String, Object>> dataList = archivesBaseBpo.getResultListBySqlIdAndParamDto("sqlId", params);
```

### 3.2 新增操作
- 使用`DomainUtil.toDomainObject`将DTO转换为领域对象
- 使用`archivesBaseBpo.createDomainObject`创建领域对象

```java
Hi9bDomain hi9bDomain = DomainUtil.toDomainObject(Hi9bDomain.class, dto);
archivesBaseBpo.createDomainObject(hi9bDomain, sysUser);
```

### 3.3 更新操作
- 使用`archivesBaseBpo.updateDomainObjectNotEmpty`更新领域对象
- 只更新非空字段

```java
Hi9bDomain hi9bDomain = DomainUtil.toDomainObject(Hi9bDomain.class, dto);
archivesBaseBpo.updateDomainObjectNotEmpty(hi9bDomain, sysUser);
```

### 3.4 批量更新操作
- 使用`archivesBaseBpo.updateBatch`进行批量更新
- 为每条记录创建包含更新字段的Map

```java
List<Map> updateList = new ArrayList<Map>();
for (String id : idArray) {
    Map<String, Object> record = new HashMap<String, Object>();
    record.put("primaryKey", id);
    record.put("field1", value1);
    record.put("field2", value2);
    updateList.add(record);
}
archivesBaseBpo.updateBatch(DomainClass.class, updateList);
```

## 4. 响应处理

### 4.1 设置操作结果
- 使用`setSuccess(true/false)`设置操作是否成功
- 使用`setMessage("消息内容")`设置提示消息

```java
setSuccess(true);
setMessage("操作成功");
return JSON;
```

### 4.2 页面跳转
- 返回JSP页面路径实现页面跳转
- 返回`JSON`常量实现AJAX响应

```java
// 页面跳转
return "controllerName/pageName";

// AJAX响应
return JSON;
```

## 5. 异常处理

- 框架已统一处理异常，无需在Controller中使用try-catch
- 业务逻辑错误通过`setSuccess(false)`和`setMessage("错误信息")`返回

```java
if (ValidateUtil.isEmpty(ids)) {
    setSuccess(false);
    setMessage("请选择要操作的记录");
    return JSON;
}
```

## 6. 工具类使用

### 6.1 ValidateUtil
- 使用`ValidateUtil.isEmpty()`检查对象是否为空
- 使用`ValidateUtil.isNotEmpty()`检查对象是否非空
- 支持检查String、Collection、Map等多种类型

```java
// 检查字符串是否为空
if (ValidateUtil.isEmpty(str)) { ... }

// 检查集合是否为空
if (ValidateUtil.isEmpty(list)) { ... }
```

### 6.2 DomainUtil
- 使用`DomainUtil.toDomainObject()`将DTO转换为领域对象

```java
DomainClass domain = DomainUtil.toDomainObject(DomainClass.class, dto);
```

## 7. 代码规范

### 7.1 命名规范
- 类名使用大驼峰命名法，如`Hi9bController`
- 方法名使用小驼峰命名法，如`getHi9bList`
- 变量名使用小驼峰命名法，如`sysUser`, `updateList`

### 7.2 注释规范
- 类、方法应有清晰的注释说明功能
- 复杂逻辑应有适当的注释说明

```java
/**
 * 批量提交月度汇报
 * 
 * @return
 */
@RequestMapping("hi9bController!submitHi9b.do")
public String submitHi9b() {
    // 方法实现
}
```

### 7.3 导入语句规范
- 按照类型分组导入语句
- 避免使用通配符导入（如`import java.util.*`）
- 移除未使用的导入语句

```java
import com.yinhai.common.common.controller.ArchivesBaseController;
import com.yinhai.core.common.api.context.ISysUser;
import com.yinhai.core.common.ta3.dto.TaParamDto;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
```

### 7.4 代码组织
- 相关功能的方法应放在一起
- 公共方法应放在类的末尾
- 保持代码结构清晰，逻辑分明

## 8. 最佳实践

### 8.1 参数验证
- 在业务处理前验证必要参数
- 使用`ValidateUtil`进行参数验证
- 对于复杂验证，可以编写专门的验证方法

### 8.2 事务处理
- 复杂业务逻辑应在Service层处理
- 涉及多表操作时，确保事务一致性

### 8.3 代码复用
- 提取公共逻辑为独立方法
- 避免代码重复

### 8.4 性能优化
- 避免在循环中进行数据库操作
- 使用批量操作替代循环单条操作
- 合理使用缓存

遵循以上规范，可以确保TaCloud后端Controller代码的一致性、可读性和可维护性，提高开发效率和代码质量。
