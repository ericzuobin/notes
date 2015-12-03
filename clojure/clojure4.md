##Clojure与Java互操作

>学习一门语言肯定要学习两个语言中不同的地方，以下介绍Clojure和Java不同的地方。
> 关键是要培养函数式的思维！

####在刚开始使用Clojure的时候，会有一些不适应。

1. 主要是Clojure的关键字少。
2. 大部分操作都是基于函数，在Java里面很常见的操作需使用函数才能实现。
3. 关键还是API不熟，函数式思维还没建立。

####以下部分例子，转换函数式思维

- import操作

~~~Clojure
;用于导入Java的类
(ns foo.bar
  (:import (java.util Date
                      Calendar)
           (java.util.logging Logger
                              Level)))

~~~

- Java操作(很多都是通过反射实现的,后面有介绍如何降低反射带来的消耗)

1. 创建一个对象

  (ClassName.) 不带参数
  (ClassName. args1 args2) 带参数

2. 调用实例方法

  (.methodName object) 不带参数
  (.methodName object args1 args2) 带参数

3. 调用类静态方法

  (ClassName/staticMehod)
  (ClassName/staticMehod args1 args2)

4. 访问类静态字段

  (ClassName/Field)

5. 引用Class   

  ClassName  ;相当于ClassName.Class

6. 访问对象实例字段

  (.field object)

7. 给对象实例字段赋值

  (set! (.field object) newvalue)

~~~Clojure
user=> (import java.util.Date)
java.util.Date

user=> (def *now* (Date.))
#'user/*now*

user=> (str *now*)
"Tue Jul 13 17:53:54 IST 2015"
~~~

- 便利的一些互操作宏

1. ..宏(方法调用宏)

  可以依次执行的一个宏,减少代码量

~~~Clojure
(. (. calendar getTimeZone) getDisplayName) ;
(.. calendar getTimeZone getDisplayName) ;

user> (.. "fooBAR" (toLowerCase) (contains "ooba"))
true
~~~

2. doto宏

  调用一系列的嵌套形式，把doto的第一个参数作为第一个参数传给后面的所有的形式。

~~~Clojure
user> (doto (java.util.HashMap.)
            (.put "a" 1)
            (.put "b" 2)
            (println))
#<HashMap {b=2, a=1}>
{"b" 2, "a" 1}
~~~

3. 异常处理(和Java类似)

~~~Clojure
=> (try
     (/ 1 0)
     (catch Exception e (str "caught exception: " (.getMessage e))))

"caught exception: Divide by zero"
~~~

4. finally的挽歌,withopen

  帮助你自动关闭资源。

~~~Clojure
user=> (with-open [r (clojure.java.io/input-stream "myfile.txt")]
         (loop [c (.read r)]
           (if (not= c -1)
             (do
               (print (char c))
               (recur (.read r))))))
~~~

- 强制类型转换

1. 基本Java的基本数据类型都包含相应的强制类型转换函数,unchecked-byte,unchecked-int,unchecked-long等。
2. 还有unchecked-add，unchecked-inc，unchecked-dec,unchecked-negate等函数。

~~~Clojure
;unchecked-byte  强制转换成byte
user=> (unchecked-byte 127)
127
user=> (unchecked-byte 128)
-128
user=> (unchecked-byte 255)
-1

;直接使用会报错，超过值范围
user=> (byte 300)
IllegalArgumentException Value out of range for byte: 300  clojure.lang.RT.byteCast (RT.java:1008)

user=> (unchecked-add Integer/MAX_VALUE 0)
2147483647

user=> (unchecked-add Integer/MAX_VALUE 1)
-2147483648
~~~

- 位操作

1. 包含bit-and与,bit-or或,bit-not非,bit-xor异或等等操作。
2. bit-shift-left相当于<<操作,bit-shift-right 相当于>>操作。
3. bit-set 将某位设置1

~~~Clojure
(bit-and 2r1100 2r1001)
;;=> 8
;; 8 = 2r1000

user=> (bit-xor 2r1100 2r1001)
5
;; 5 = 2r0101

(bit-shift-left 1 10)
1024

(bit-set 2r1011 2) ; 序号从0开始
15
~~~

- 数组

1. 基本数据类型对应的数组byte-array,int-array等。
2. make-array创建数组操作。
3. aget访问数组操作。
4. aset设置数组的操作，还有aset-int,aset-long等基本类型的设置值操作。
5. into操作。

~~~Clojure
user=> (int-array [1 2 3])
int[] [i@263c8db9];

(make-array Integer/TYPE 3)
; Types 定义在 clojure/genclass.clj:
;    Boolean/TYPE
;    Character/TYPE
;    Byte/TYPE
;    Short/TYPE
;    Integer/TYPE
;    Long/TYPE
;    Float/TYPE
;    Double/TYPE
;    Void/TYPE
user=> (pprint (make-array Double/TYPE 3))
[0.0, 0.0, 0.0]

;创建空数组
(make-array Integer 10 100)   ;new Integer [10] [100]

;aget操作
user=> (def a1 (double-array '(1.0 2.0 3.0 4.0)))
#'user/a1
user=> (aget a1 2)
3.0

user=> (def a3 (make-array Integer/TYPE 100 100))
#'user/a3
user=> (aget a3 23 42)
0

;aset操作
user=> (def my-array (into-array Integer/TYPE [1 2 3]))
#'user/my-array
user=> (aset my-array 1 10)
10

;从集合创建数组
(into [] {1 2, 3 4})
[[1 2] [3 4]]
~~~

- 避免使用反射，可以给参数或返回值加上类型提示

~~~Clojure
;在调用Java的方法的时候可以使用，避免Clojure通过反射消耗
(defn length-of
  [^String text]
  (.length text))
~~~

- for关键字

1. Clojure中不再是遍历关键字，是一个宏，返回一个序列。

~~~Clojure
user=> (for [x (range) :while (< x 10)
             y (range) :while (<= y x)]
         [x y])
([0 0]
 [1 0] [1 1]
 [2 0] [2 1] [2 2]
 [3 0] [3 1] [3 2] [3 3]
 [4 0] [4 1] [4 2] [4 3] [4 4]
 [5 0] [5 1] [5 2] [5 3] [5 4] [5 5]
 [6 0] [6 1] [6 2] [6 3] [6 4] [6 5] [6 6]
 [7 0] [7 1] [7 2] [7 3] [7 4] [7 5] [7 6] [7 7]
 [8 0] [8 1] [8 2] [8 3] [8 4] [8 5] [8 6] [8 7] [8 8]
 [9 0] [9 1] [9 2] [9 3] [9 4] [9 5] [9 6] [9 7] [9 8] [9 9])
~~~

- loop,recur强大，谨慎使用，尽量使用函数

~~~Clojure
(loop [x 10]
  (when (> x 1)
    (println x)
    (recur (- x 2))))

;;=> 10 8 6 4 2

(loop [result [] x 0]
        (if (= x 10)
          result
          (recur (conj result x) (inc x))))
~~~

- -> ->> 宏

  -> 将前一个函数的值当做下一个函数的第一个参数
  ->> 将前一个函数的值当做下一个函数的最后一个参数

~~~Clojure
;->宏
user=> (-> "a b c d"
           .toUpperCase
           (.replace "A" "X")
           (.split " ")
           first)
"X"

;->>宏
user=> (def c 5)
user=> (->> c (+ 3) (/ 2) (- 1))                          
3/4
;相当于(- 1 (/ 2 (+ 3 c)))

~~~
