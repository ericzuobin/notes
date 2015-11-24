##并发编程

####Clojure的并发实体

- delay函数

  >delay是一种让一些代码延迟执行的机制，代码会在显式调用deref的时候执行。
  > 语法糖是@。
  > 同时delay包含的代码只执行一次，返回值会被缓存起来。

- future函数

  >对于future的调用会立即返回，不会阻塞，同时线程结果保存在future里面。

  1. future使用跟agent共享的一个线程池，所以比原生线程高效。
  2. 创建的线程比原生线程更加简洁。
  3. 其实就是Java JUC包里面的Future，能和Java更好的交互。

- promise函数

  >和delay和future相似，一样可以解引用。

  1. promise在创建的时候不会指定任何的代码或者函数来最终产生出它们的值。
  2. promise跟一个一次性的，单值的管道类似：数据再一端通过deliver函数传入，然后在管道的另外一端通过deref函数来获取值。
  3. 称为“数据流变量”，是函数式并发编程的基础构件。

~~~Clojure
user=> (def a (promise))
#'user/a
user=> (def b (promise))
#'user/b
user=> (def c (promise))
#'user/c
user=> (future (deliver c (+ @a @b))
  #_=>   (println "Complete!"))
#<core$future_call$reify__6110@a8f8f7: :pending>

user=> (deliver a 5)
#<core$promise$reify__6153@17def5b: 5>
user=> (deliver b 15)
Complete!
#<core$promise$reify__6153@b77860: 15>

user=> @c
20
~~~

  >promise最直接的应用就是基于API的回调得以同步执行。

####简单的并行化

- agent组件

  >可以用来高效的完成并行化计算任务。
