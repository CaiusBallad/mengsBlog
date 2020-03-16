[toc]

参考教程：https://juejin.im/post/5c3d7c8a51882525dd591ac7

# JAVA8新特性

- 接口允许通过default关键字给抽象方法提供默认实现
- Lambda表达式
  - ()->{}
  - 内部可访问的有：
    - final修饰变量（或在隐性即不变的变量）
    - 能读写成员变量，静态变量
    - 不可访问接口的默认方法（匿名类实现可以访问）
- 函数式接口：只包含一个抽象方法的声明，默认方法不是抽象方法。
  - 只有函数式接口才能使用Lambda缩写
  - @FunctionalInterface
  - 内置的函数式接口：
    - Predicate断言：组合复杂的逻辑判断
    - Function：组合链接多个函数
    - Supplier生产者：无入参，生产指定结果（？）
    - Consumer消费者：需要提供入参，用来被消费
    - Comparator
- 便捷引用类的构造器及方法：实例对象可以使用关键字"::"来引用类的方法或构造器
  - ::

> 个人理解函数式接口是把具体方法当参数，而接口作为函数来接受这些参数的过程，Lambda用来简化方法参数，::用来快速引用对象中的构造器或方法；而函数式接口只有一个抽象函数的规定，是为了让参数能直接对应需要具体化的位置

- Optional： 防止空指针异常

- Stream流：可以使用java.util.Stream对包含一个或多个元素的集合做各种操作

  - 操作对象是集合；中间操作返回流，终端操作返回一个结果
  - 只能对实现了java.util.Collection接口的类做流的操作（Map不支持Stream流）
  - Filter过滤：中间操作
  - Sorted排序：中间操作，不改变原来的集合
  - Map转换：map函数，对集合中的每一个元素做功能处理
  - Mathc匹配：验证集合中的每一个元素，返回一个布尔类型
  - Count计数
  - Reduce：参数为Function类型，能将list归约成一个值，返回值为Optional类型
  - Parallel-Streams并行流：多线程处理，多核CPU增加效率

- Map集合：

  - 可以对其 `key`, `values`, `entry` 使用 流操作，如 `map.keySet().stream()`, `map.values().stream()` 和 `map.entrySet().stream()`

  - 新特性：

    - ```java
       map.putIfAbsent(i, "val" + i);
      ```

    - ```java
      map.forEach((key, value) -> System.out.println(value));
      ```

    - ```java
      map.computeIfPresent(9, (num, val) -> null);
      ```

    - map.remove(key, value)操作，只有完全匹配才能删除

    - map.getOrDefault(42, "not found") 取值设定默认值

    - value的合并操作

- 新的日期API

  - Clock：提供对当前日期和时间的访问
  - Timezones时区：获取不同地方时区的时间
  - LocalTime：时间对象，一个没有指定时区的时间类，可用时区创建该类
  - LocalDate：日期对象，同上为final类型对象，每次操作会返回新的时间对象
    - 日期加减，获取星期等
  - LocalDateTime：日期-时间对象

- Annotations注解

  - 【未理解作用，存疑】@Repeatable：允许对同一个类使用多重注解，可通过反射来读取注解内容

# JAVA9～13新特性

## Java 9

参考https://juejin.im/post/5a1c293c5188254dd9362140

- 模块化：module-info.java
- 集合工厂法：创建不可修改的集合
- 私有接口方法
- 响应式流
- 多分辨率图像API：操作和展示不同分辨率的图像
- 进程API改进：System.out.println("你的PID是：" + Process.getCurrentPid());
- 钻石操作符范围延伸
- HTTP2客户端：采用了全新的HTTP客户端API，这些API支持HTTP/2协议和WebSocket协议，并且替换了遗留的HTTPURLConnectionAPI
- ……

### Java 10

- 新增了var类型变量：只能用于局部变量和for循环声明中
- ……

### Java 11

参考：https://www.ibm.com/developerworks/cn/java/the-new-features-of-Java-11/index.html

- Lambda表达式中可使用var
- 新增字符串API
- 标准化HttpClient API
- java命令直接编译并运行java文件，不需要javac先编译了
- 增加对TLS 1.3的支持

### Java 12

参考：https://www.ibm.com/developerworks/cn/java/the-new-features-of-Java-12/index.html

### Java 13