####安装La Clojure

>安装IDEA插件La Clojure。进行IDEA后，点左上角的IntelliJ IDEA，
>选preferences, 然后左边选Plugins, 点Browse Repositories，
>搜索Clojure， 下载La Clojure之后重启即可。

- 新建项目

~~~
;创建工作目录
SahinndeMac:workspace_idea sahinn$ mkdir clojure
SahinndeMac:workspace_idea sahinn$ cd clojure/

;利用lein 创建项目，会自动帮助建立相应的目录
;其中net.zuobin/study和maven的概念一致
SahinndeMac:clojure sahinn$ lein new net.zuobin/study
Generating a project called net.zuobin/study based on the 'default' template.
The default template is intended for library projects, not applications.
To see other templates (app, plugin, etc), try `lein help new`.

;创建完成之后的文件如下
SahinndeMac:clojure sahinn$ cd study/
SahinndeMac:study sahinn$ ls
LICENSE     README.md   doc         project.clj resources   src         test

;由于创建完成之后的项目不是maven标准工程，如果需要利用lein创建pom文件
SahinndeMac:study sahinn$ lein pom
Wrote /Users/sahinn/Documents/workspace_idea/clojure/study/pom.xml

SahinndeMac:study sahinn$ ls
LICENSE     README.md   doc         pom.xml     project.clj resources   src         test
~~~

- 导入IDEA

在IDEA中import project,选择Import project from external model, 点Maven即可。

- 配置主函数

1. 在打开的IDEA工程中，打开src目录，在net.zuobin包中有study.clj文件。
这个clojure文件中，并没有main函数，因此在IDEA中执行run, 什么也不会输出。
把这个文件的内容改一下

~~~Clojure
(ns net.zuobin.study)

(defn -main
  [& args]
  (println "Hello, World!"))
~~~

2. 然后在IDEA中最上边的菜单中点Run，选择Edit Configurations,
选中Run main function in the script namespace。

3. 在Run菜单中执行Run "study"， 程序会打印出"Hello, World!"。

4. 执行lein run， 提示“No :main namespace specified in project.clj”.

~~~Clojure
需要修改project.clj

(defproject net.zuobin.study "0.1.0-SNAPSHOT"
  :main net.zuobin.study
  :dependencies [[org.clojure/clojure "1.6.0"]])
~~~

保存后，执行lein run，输出"Hello, world!"

##打包项目

- lein uberjar

>可以将项目打包成独立运行包，但是需要注意的是，打包加入以下这句话才行。
> (:gen-class :main true),启用main函数

~~~
(ns net.zuobin.study
  (:gen-class :main true)
  (:use [clojure.java.io]))
~~~  
