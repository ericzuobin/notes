##集合与数据结构

####Clojure数据结果特色

- 数据结果是依据抽象来用的，而不是依据具体的实现细节来用。
- 数据结构是不可改变而且是持久的。

####集合字面量
- list列表      '(a b c)
- vector      ['a 'b 12.5]
- map  {:key1 "value1" :key2 "value2"}   

  注：空格和逗号没有区别，可以使用空格来区分{}是Clojure中空map的字面量。

- set   #{1 2 3}

####集合常用函数
- conj添加一个元素到集合

  >注：关于conj函数，conj不保证把元素添加到所有集合的最前面或者最后面，它保证
  > 对于所有的集合都能高效的插入。所以针对list列表，插入会插入到最前面，针对vector
  > 会插入到最后面等等。

- empty 获取一个跟所提供集合类型一样的空集合。

  >我们可以不必知道一个集合的具体类型就能创建出一个同类型的集合。

- connt 函数返回集合中的元素个数，保证对于所有的集合操作耗时都是高效的。(序列除外)

- seq 序列（序列不是列表List）

  >计算一个序列的长度是比较耗时的，而且序列可能是惰性的，所以可能是无限惰性序列。
  >列表会保存它的长度，所以要获取列表的长度是高效的。序列则不能。获取序列的长度只能遍历序列。

  >序列操作函数rest和next的区别：如果操作的结果为空的话，rest返回的是空序列，next返回
  > 的是nil.

  >创建序列的方法：

  1. cons 始终把第一个参数值加入到第二个集合参数的头上去，而不管第二个参数的具体的集合类型。
  2. list* 用于简化创建指定多个值的seq请求。

  >注：list*不返回一个列表。

  3. 访问一个惰性序列的过程叫做实例化。

  >关于惰性序列：定义惰性序列应该尽量不要有副作用，不应该依赖惰性序列元素的实例化来控制程序的
  > 执行流程。
  > 注意：一旦惰性序列的一个元素被实例化以后，只要你保持了对这个序列的引用，那么这个元素就会一直
  > 被保持着了。

####关系型数据结构

>key value关联起来的数据结构。


- assoc方法，添加一个key和value的映射
- disassoc方法，移除一个key和value的映射
- get 找出指定key的value
  >对于特殊值，nil，如果集合中恰好包含nil的值，那么get就会出现问题。
  >可以使用contain？然后再get
  > 或者使用新的函数find，该函数会返回键值对。

- constains? 是否包含指定集合key
  >对于检测vector的话，是vector的下标


~~~Clojure
(constains? [1 2 3] 0)

;=true
~~~


####索引集合Indexed

- nth函数

  >对于nth和get的区别，nth越界的话，会抛出异常，get会返回nil.

####栈Stack

  >Clojure并没有独立的“栈”的集合。

  1. conj方法添加到栈。
  2. pop方法获取并移除栈顶元素。
  3. peek方法获取栈顶的值，但是不移除栈顶元素。

####访问集合元素的简洁方式

  1. 集合是函数
  2. 集合的key(通常)也是函数

  >推荐使用关键字或者符号作为查找函数来使用，这样做可以避免空指针异常。

####持久性和结构共享

  1. 数据结构是持久的，这种技术使得你做过修改的集合跟原来的集合共享内部存储数据，同时还能
  保证高效率。
  2. 为了提供持久性语义，Clojure中几乎所有的数据结构都是通过树来实现的(hashmap,hashset,有序map,有序set,vector等)

####易变集合

  1. 对一个易变集合做修改之后，旧版本易变集合的任何引用都不能再使用了！！！
  2. 易变集合是针对大量更新某个集合，以减少内存分配带来的开销而进行优化的，所有老版本的任何引用都不要再使用了。


####read-string
- 一个对于尝试Clojure方便的函数read-string，这个函数做的是和read函数一样的事情，接受一个字符串作为参数
    (read-string "(+ 1 2)")
    ;=(+ 1 2)

####正则表达式
- \#开头的字符串为正则表达式


####var不是变量
- var引用类型是一种不可修改的内存地址，从而可以保存任何值，通常用来定义常量或者函数。

####阻止求值 quote
- 阻止表达式求值
- '单引号也是阻止求值的关键字

####代码块do
- do会依次求职你传进来的所有表达式，并且把最后一个表达式的结果作为返回值。

####定义var：def
- 作用是在**当前**命名空间定义或者重定义一个var

####let绑定
- \_下划线指定的let绑定是不关心它的绑定值的。
- 所有的本地绑定都是不可变的。避免大量的编程错误。
- let绑定可以在编译器进行解构，简化绑定数组抽取的操作。

####解构   let []
- 顺序解构    []里面对应的是[]
    (def v [12 45 66 [22 87]])
    最基本的解构：(let [[x y z] v])
    嵌套解构：(let [[x _ _ [y z]] v])    注：下划线表示忽略某个绑定
    保持剩下的元素：(let [ [x & rest] v ] rest)    ;=(45 66 [22 87]])
    保持被解构的值： :as关键字  (let \[[ x _ z :as original-vector] v]  (conj original-vector (+ x z)) ) ;=[12 45 66 [22 87] 78]

- map解构  []里面对应的是{}
    (def m {:a 5 :b 6 :c [1 88 9] :d 88})
    最基本的map解构： (let \[{ a :a b :b} m] (+ a b))  ;= 11
    map解构的是vector、字符串或者数组的话，key一个个是数组下标。