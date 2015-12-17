##PlantUML,高效的UML画图工具

PlantUML是一个画图脚本语言，用它可以快速地画出：时序图、流程图、用例图、状态图、组件图。
官网为[plantuml.com](http://plantuml.com/)

####一个简单的例子

~~~BASH
@startuml
skinparam monochrome true

start
:"步骤1处理";
:"步骤2处理";
if ("条件1判断") then (true)
    :条件1成立时执行的动作;
    if ("分支条件2判断") then (no)
        :"条件2不成立时执行的动作";
    else
        if ("条件3判断") then (yes)
            :"条件3成立时的动作";
        else (no)
            :"条件3不成立时的动作";
        endif
    endif
    :"顺序步骤3处理";
endif

if ("条件4判断") then (yes)
:"条件4成立的动作";
else
    if ("条件5判断") then (yes)
        :"条件5成立时的动作";
    else (no)
        :"条件5不成立时的动作";
    endif
endif
stop

@enduml
~~~

渲染之后显示结果如下:
![plantuml](https://github.com/ericzuobin/notes/blob/master/pic/img/plantuml.jpg)


#### 安装教程

- Java
- Graphviz(开源的图片渲染库)
- Sublime Text 2 or 3

介绍下Graphviz的安装

Mac执行brew install graphviz 直接安装。

有点需要解决的是，安装的时候graphviz的依赖包libpng-1.6.18.tar.xz下载出错，所以这里介绍下如何解决

- (http://downloads.sourceforge.net/libpng/libpng-1.6.18.tar.xz) 在这个地址下载安装包
- 将下载好的源码包放到/Library/Caches/Homebrew/后重新执行安装命令即可


####Sublime Text 配置

需使用sublime_diagram_plugin,默认的包管理中没有，需要自己添加源。

- 使用 Command-Shift-P 打开 Command Palette
- 输入 add repository 找到 Package Control:Add Repository
- 在下方出现的输入框中输入 https://github.com/jvantuyl/sublime_diagram_plugin.git 然后回车
- 等待添加完成后再次使用 Command-Shift-P 打开 Command Palette
- 输入 install package 找到 Package Control:Install Package
- 等待列表加载完毕，输入 diagram 找到 sublime_diagram_plugin 安装
- 重启 Sublime Text

重启后可以在 Preferences -> Packages Setting 看到 Diagram，快捷键是Command + m 如果不冲突直接使用即可。

更多使用方法参考官方中文文档：http://translate.plantuml.com/zh
推荐官网的在线生成工具: http://plantuml.com/plantuml/
