# 6. 关于schema的说明

## 先说结论

vk框架不依赖 schema，schema 对vk框架不生效。（如果你用JQL去操作数据库，schema 是生效的）

如果你想知道原因，那么继续往下看。

## 原因说明

云开发的数据库为 mogondb，它与我们之前用的 mysql 不同，mogondb 其实没有所谓的字段这个概念。

在 mogondb 中，表的数据=list，行记录=json，字段=json的属性。

因此字段可以任意拓展，你通过update一个不存在的字段，就等于添加这个字段并设置这个字段的值。（这也是mogondb的优势）

同时 mogondb 允许1个表内所有的行记录内的字段可以不统一，即第一条记录有a字段，第二条记录可以没有a字段。

即在 mogondb 中，没有所谓的 schema（表结构）

schema 是 uniCloud 官方为了配合 clientDB（前端操作数据库） 而生（后面又出了JQL，JQL也对 schema 生效，同时JQL可以写在云函数端）

## schema 功能如下：

1. 因为前端操作数据库不安全，因此需要schema来控制读写权限。（这也是schema诞生的原因，前端操作数据库必须要有这个schema来控制权限）
2. 描述现有的数据含义。可以一目了然的阅读每个表、每个字段的用途。
3. 对每个字段进行一些规则约束，如必填，若默认值等等。
4. 直接映射表的连表关系，通过JQL可以很方便的进行连表。
5. 根据schema自动生成前端界面（schema2code），包括列表、详情、新建和编辑页面，自动处理校验规则。

更多功能可查看官方[schema文档](opens new window)

而 vk框架 的数据库操作全部在后端（云端）操作，因此可不依赖 schema（同时 vk框架 封装了一套 [vk.baseDao](opens new window)数据库API）

当然如果你想在云函数中使用JQL语法，则需依赖 schema