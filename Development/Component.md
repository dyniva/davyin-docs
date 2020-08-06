##	组件简介
组件是内容管理系统的核心，是网站建设的基础元素。从使用场景来说，用户可以在着陆页新建一个组件，或者编辑现有组件，从而更新页面的内容，素材（图片，视频等）。
##	常用组件
基于多年的积累与实践，以及项目特定需求，一般会有如下常见组件：
<pre>
内容类：
  标题组件
列表类：
  列表1.0
  列表1.1
  列表2.0
  列表3.0
  列表4.0
  …
图文混排
  图文混排1.0
  图文混排1.1
  图文混排1.2
  …
轮播图
  轮播图1.0
  轮播图1.1
  轮播图2.0
  轮播图3.0
  …
导航
  自定义搜索
  快捷入口
媒体
  视频
Hero
  头条1.1
  头条2.0
</pre>

##	component_hub模块介绍
Component hub模块是自主研发的组件管理模块，该模块基于Drupal原生的Plugin机制，Config管理，theme机制等，使得组件的开发更加内聚化，功能与展现解耦，从而提升组件开发提供良好的开发体验。
## 组件开发简介
每一个具体的组件，可以视为一个小型的MVC（Model View Controller），用于实现数据，逻辑，显示分离的方法。
组件的开发声明必须放在Drupal的模块的指定路径：src/Plugin/ContentWidget
如下是一个名字为Example组件的典型结构：
<pre>
EXAMPLE_COMPONENTS
│  example_components.info.yml
│
└─src
    └─Plugin
        └─ContentWidget
            └─Example
                    Component.php
                    template.html.twig
</pre>

###	Component.php
```src/Plugin/ContentWidget/Example/Component.php```
是组件最重要的部分，它是组件的入口，也是组件的声明配置。
<pre>
/* */符号在PHP语言中代表注释，通常作为文字说明用途，一般不具有程序效果，但是这一段注释则不同，是代码的一部分，而且非常重要，如果配置出现错误，组件就无法加载出来。开发组件时，建议直接从其它组件抄过来，再修改其中的数值，避免遗漏。
id： id是组件的唯一识别，不能出现重复，建议与区块类型的机读名称相同。
label: 使用组件时候的名字，支持后台翻译。
admin_label：组件的管理名称，可以留空。
fields_display: 必须存在,并且值为 1
category：组件所属分类，它决定在着陆页添加组件时，它被分配到那一组中
description：组件详细说明
entity：entity是drupal里数据的单元，这里指的就是之前后台添加的区块类型。block_content就是代表区块，而example是区块类型的机读名称，故写为block_content:example，意思就是名为example的区块类型。
plugin_path = {
   "type" = "module",
   "name" = "example_components",
   "directory" = "src/Plugin/ContentWidget/Example",
},
代表组件的位置，"name" = "example_components"是因为组件在名为example_conponents的模块中，如果需要把组件迁移到其它模块中，name需要改成新的模块名。
template = "template" 是指模板名，也就是template.html.twig，必须存在。 
preview = "preview.jpg" 是指预览文件名，可以制作组件的预览效果图并改名为preview.jpg放到同一文件夹下，在选择该组件时就会出现。没有预览图时要把这一行去掉。

widgetView方法是组件的Controller部分，可以进行一些简单的逻辑处理或变量预处理。
widgetView方法是真正需要写PHP的部分，如果“管理显示”的功能和twig模板语言都无法都无法满足要求，就需要在方法内用PHP代码解决。具体可以看其它组件的实现方法。
</pre>

###	template.html.twig
```src/Plugin/ContentWidget/Example/template.html.twig```
 
模板文件主要是HTML代码+字段占位符，字段占位符可以调出对应的字段。例如图片字段机读名称是image，就可以用{{ content.image }}调出来。

Twig是一种模板语言，它可以不用编写PHP代码可实现的编程效果，Twig的语法较PHP容易上手，可以被前端开发人员所利用。

更多关于twig的信息可以参考如下网站。
- [Twig基础教程](https://twig.symfony.com/)
- [Drupal8 twig有那些函数](https://www.drupal.org/docs/8/theming/twig/functions-in-twig-templates)

###	组件的样式优化
经过上述步骤，可以创建新的组件，输入数据后，用户即可在前台看到符合结构的html结构。
如果需要对组件进行进一步美化，需要额外的css或js支持。

###	代码提交
使用git相关命令代码提交。
###	部署
在生产服务器，执行如下命令进行组件部署：
-	git pull
- drush cim -vy
-	drush cr
