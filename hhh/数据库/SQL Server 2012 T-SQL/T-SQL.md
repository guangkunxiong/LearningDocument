## 系统数据库
- master：存储实例范围内的元数据信息、服务器配置、实例中所有数据库和初始化信息
- Resource： 隐藏、只读数据库，存储系统中所有系统对象的定义。
- model：用作创建新数据库的模板，创建的每一个新数据库都是由model的副本初始化创建的。
- tempdb：存储临时数据的地方。例如工作表、排序空间、版本控制信息等。创建的临时表在tempdb。每次重新启动sql server实例的时，tempdb会被破坏从model副本重建。
- msdb：sql server 代理服务存储数据的地方。负责自动操作，包括作业、计划和报警。

- 始终使用架构限定式的对象名称进行查找
- where只返回逻辑表达式为true的，不包括unknown
- 对于check约束而言，sql的处理定义式拒绝false，这就包括true和unknown
- exists总是返回true/false
- offset 偏移-fetch 取数 
- top 10 percent百分比
- 使用top with tims和order by会先返回order by后的数据，执行top指令，然后返回检索到最后一行order by的字段值相同的其他的所有行。这就有可能超过top的行数
- 开窗函数：对基于查询中的每一行，按行的窗口组进行运算，并计算一个标量结果值 over字句
- 常规字符和unicode，常规为char和varchar ，unicode为nchar和nvarchar要求没字符为两字节
- max的大小默认为8000字节，超过阈值就会作为大型对象存储在外部
- 视图式无序的，需要在外部查询使用order by
