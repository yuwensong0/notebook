#### ``AnnotationUtils``

用于注解的工具类，每个注解都实现了``java.lang.annotation.Annotation``的接口，但是在自定义注解的过程中只是用到了@符号，背后的工作由编译器完成。该工具类的使用地方主要是用在反射时候，因为反射时才可以简单获取到实现了Annotation接口的实例，该实例是个代理。在大部分情况下， Java的annotation没有行为，只能有数据，实际上就是一组键值对而已。 对于自定义的注解，很多情况下都是类似标记的功能，拦截方法通过反射查看是否有特定的自定义注解，有的话执行相应的逻辑，该作用建立在拦截器或者过滤器的基础上。

#### ArrayUtils

- 添加一个元素到数组，包括添加到最后，第一和指定位置 add
- 一次性添加多个元素到最后 addAll
- 克隆一个数组 clone
- 判断数组是否包含某个指定元素，或者指定包含某个元素的指定范围 contains
- 获取数组长度 getLength
- 查找数组中包含某个元素的所有位置的位图 indexesOf
- 查找数组中某个元素第一次出现的位置，可以指定起始范围 indexOf，最后一次出现位置lastIndexOf
- 将一组元素插入到指定位置的数组中 insert
- 空判断 isEmpty
- 将null变为一个长度为0的同类型数组nullToEmpty
- 移除指定位置的元素remove，可以一次性移除多个removeAll
- 移除所有指定元素removeAllOccurrences
- 移除第一个出现的指定元素removeElement，可以指定移除多个元素，每个指定的元素在数组中只会被移除一次removeElements
- 将数组反转Reverses
- 将数组平移指定位数，正数向右，负数向左shift
- 打乱数组shuffle
- 截断数组，全新数组，不共享原数组subarray
- 交换指定位置的数组元素，可以指定长度，长度超过最短可交换取最短值swap
- 给定的一组元素返回该类型的数组toArray
- 将特定的数组转换为map toMap
- 将基本类型的数组转换为对应的包装类型数组toObject，反之toPrimitive，可以对null设定默认值
- 数组转为字符串toString，转为字符串数组toStringArray

#### BooleanUtils

感觉不是很实用，提供从基本类型到包装类型，字符串到boolean，反之。数字到boolean，反之。多个boolean运算等。

#### CharSequenceUtils

方法比较少，大部分情况下适用于String，目前感觉没什么有用

#### CharUtils

提供对字符的各种判断：是否ascii，小写，大写，数字，控制字符还有对字符转换为unicode编码

#### ClassPathUtils

可以得到资源的完整包名或者路径字符串，也许可以用来一个空的java文件作为标记，可以得到同级文件的路径

#### ClassUtils

主要封装了jdk的反射操作，很多都是涉及基本类型问题，字符串转换为class类型，以及字符串的表示方式

- 获取简短类名，对数组跟内部类进行过处理优化显示 getShortClassName
- 获取简单类名，对内部类不会显示外部类的信息getSimpleName
- getName获取类名，同jdk的getName
- 获取包名，对元素类型及对应数组返回空字符串，通过截取cls.getName()来实现 getPackageName
- 获取指定长度的缩写getAbbreviatedName
- 获取所继承所有的类 getAllSuperclasses
- 获取所有实现的接口getAllInterfaces
- 字符串与类的互相转换convertClassNamesToClasses，convertClassesToClassNames
- 检查是否类型赋值相容，可进行自动装箱拆箱，原始类型进度不减向上兼容 isAssignable
- 是否原始类型或其包装类isPrimitiveOrWrapper
- 原始类型类与包装类型类互相转换primitiveToWrapper，wrapperToPrimitive
- 是否内部类isInnerClass
- 由字符串获取class，支持更多的字符串形式，参数更多 getClass
- 获取反射方法getPublicMethod
- 获取规范化的简短类名getShortCanonicalName
- 获取规范化简短包名getPackageCanonicalName
- 可迭代的继承父类hierarchy