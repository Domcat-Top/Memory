## Java

### 1. @FunctionalInterface注解

- 该注解表示，当前接口为一个函数式接口。
- 该接口只有一个方法
- 这种类型的接口也被称为SAM接口，即 Single Abstract Method interfaces
- **用途：**主要用于Lambda表达式和方法引用，即，当前接口的实现类，可以直接使用Lambda实现当前接口的方法（因为只有一个方法）。
- **注意：**该注解不是必须的。如果 一个接口符合“函数式接口” 的定义，那么加不加该注解，都没有影响，加上该注解能更好的让编译器进行检查。反之，如果编写的不是函数式接口，但是添加了@FunctionInterface，那么编译器会报错。

### 2. @SuppressWarnings注解

- JavaSE提供的注解，作用是屏蔽一些无关紧要的警告。

- 使用方式：标注到 类、字段、方法、参数、构造方法 及 局部变量 上。告诉编译器忽略指定的警告，不用在编译完成后出现警告信息。

- ```tex
  all，抑制所有警告
  boxing，抑制与封装/拆装作业相关的警告
  cast，抑制与强制转型作业相关的警告
  dep-ann，抑制与淘汰注释相关的警告
  deprecation，抑制与淘汰的相关警告
  fallthrough，抑制与switch陈述式中遗漏break相关的警告
  finally，抑制与未传回finally区块相关的警告
  hiding，抑制与隐藏变数的区域变数相关的警告
  incomplete-switch，抑制与switch陈述式(enum case)中遗漏项目相关的警告
  javadoc，抑制与javadoc相关的警告
  nls，抑制与非nls字串文字相关的警告
  null，抑制与空值分析相关的警告
  rawtypes，抑制与使用raw类型相关的警告
  resource，抑制与使用Closeable类型的资源相关的警告
  restriction，抑制与使用不建议或禁止参照相关的警告
  serial，抑制与可序列化的类别遗漏serialVersionUID栏位相关的警告
  static-access，抑制与静态存取不正确相关的警告
  static-method，抑制与可能宣告为static的方法相关的警告
  super，抑制与置换方法相关但不含super呼叫的警告
  synthetic-access，抑制与内部类别的存取未最佳化相关的警告
  sync-override，抑制因为置换同步方法而遗漏同步化的警告
  unchecked，抑制与未检查的作业相关的警告
  unqualified-field-access，抑制与栏位存取不合格相关的警告
  unused，抑制与未用的程式码及停用的程式码相关的警告
  ```

### 3. Calendar

- 原生 Java 的 Utils 包下的工具

  ```java
  // 简单获取当前年份、月份、小时、周几、第几周、一年的第几天等 参数，直接这样使用即可
  Calendar.YEAR; 
  // 如果想操作时间的话，即获取一个calendar对象，内置了很多可以操作时间的函数和方法
  Calendar cal = Calendar.getInstance();
  // 获取当前时间：格式是国际标准，看起来别扭
  cal.getTime();
  // 获取时间戳
  cal.getTime().getTime();
  ```

  

