# 内部原理

-   CIL common intermediate language
-   CLR common language runtime
	-   JIT just-in-time
	-   类型系统：负责管理类型控制和兼容性机制，包括Common Type System和用于反射机制的元数据
	-   异常处理：负责用户程序和运行时的异常处理，内建Windows SHE(structured exceptions handling) 中的原生机制和C++异常
	-   内存管理：主要是垃圾回收
	-   执行引擎：包括JIT和异常处理，在ECMA-355中称之为虚拟执行系统，描述为负责加载和运行为CLI编写的程序。提供托管代码所需要的服务，并通过元数据，提供在执行阶段将生成的单独模块连接在一起的数据
	-   垃圾回收器

## 程序集和应用域

## 进程内存区域
