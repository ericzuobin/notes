##关于IDEA的Clojure插件Cursive的Main方法

 > 以前使用的编辑器是La Clojure插件，里面有专门的main方法选项。

1. 自从迁移到Cursive(号称IDEA中最好的Clojure编辑器，目前只有测试版)后，
发现一个问题，就是main方法找不到地方设置了。

2. Google了下，找到了以下方法可行。

- 方法如下:

1. 创建一个Java的Application测试。
2. Main Class:  clojure.main
3. Program arguments : -m 命名空间
4. 命名空间下引入 (:gen-class :main true)


  >也就是main函数下面的ns后语句

~~~Clojure
(ns net.zuobin.vclient
(:gen-class :main true))

(require '[clj-http.client :as client])

(defn -main
[& args]
(println (client/get "http://www.baidu.com"))
)

;比如这段代码,Program arguments : -m net.zuobin.vclient
~~~

5. 最重要的！在setting下面，Clojure Compiler里面把需要的namespace选上。然后Compile以下module即可。

附图说明：

![cursive1](https://github.com/ericzuobin/notes/blob/master/pic/Clojure/Cursive.jpg)
![cursive2](https://github.com/ericzuobin/notes/blob/master/pic/Clojure/Cursive2.jpg)
