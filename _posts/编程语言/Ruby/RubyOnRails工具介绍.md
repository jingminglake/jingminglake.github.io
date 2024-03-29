---
layout:     post
title:      RubyOnRails工具介绍
subtitle: 笔记
date: 2020-11-24
author: Jingming
status: public
catalog: true
tags:
    - Ruby
---

> https://juejin.cn/post/6844903745822670861<br/>
 https://www.codenong.com/6927442/<br/>

### 概念介绍

#### RubyGems

RubyGems是下载Ruby包的工具，例如帮我们下载Rails。

在Ruby中，如果想使用另外一个 Ruby 文件中的内容，需要使用 require 关键字来加载另外一个 Ruby 文件中的内容。

require 会在 Ruby 预设的 $LOAD_PATH 中去查找对应的文件。

可以通过执行 ruby -e 'puts $LOAD_PATH' 来看看当前 Ruby 中的 $LOAD_PATH 都有什么内容。

gem使用gemspec来描述一个ruby文件包的元信息，gem把脚本打包成一个.gem文件，就相当于java打的.jar包。

执行gem install 文件名.gem，下载gem（也就是ruby文件包）到GEM_PATH中。

安装了GEM的环境，require不仅会在$LOAD_PATH中查找文件，还会在GEM_PATH中找文件。


#### Bundler

GEM文件之间必然会出现版本依赖的问题，就像Java使用Maven管理包依赖，Ruby使用Bundle来解决GEM之间依赖管理。

使用Gemfile来规定所有需要下载的GEM（类似于Java的pom文件），执行bundle install来下载和安装GEM包，并把下载下来

的GEM包的版本记录在Gemfile.lock中。

Gemfile.lock与Gemfile不同。设计Gemfile.lock的原因是，Gemfile下载GEM包的时候与Java的Pom不同，

Gemfile指定的版本是模糊的，而不是具体版本，例如说大于某版本，这样的结果是，在不同时间下执行Gemfile，下载下来的GEM不同。。

因此设计了Gemfile.lock，bundle install的时候，会先查看Gemfile.lock，然后结合Gemfile来安装"最新"版本。

bundle exec作用是让当前执行ruby程序使用Gemfile.lock中的GEM版本，而不是$LOAD_PATH中的版本。

查找安装的gem的位置：bundle show <gemName>

#### RVM

RVM的作用是让我们可以同时在多个版本的Ruby环境下工作。这好比Java开发时候，必须先选择一种JDK版本(JVM版本)进行开发。

#### Rake

Rake和C++的make类似，它有着自己的语法来说明项目结构（Rakefile文件），并可以编译项目。

### Rails

Rails是Ruby的搭建Web程序的MVC框架，M对应Active Record， V对应Action View， C对应Action Controller。

流程是：点击http链接，routing功能将url和具体controller中具体action进行映射（例如在route.rb中配置 get "/xx/yy" => "controller#action"，其中action是定义在该controller下的一个方法名）。

action对应指定view（actionName.html.erb，也就是ruby引擎生成的html，类似于.JSP。application.html.erb是所有html.erb的"父文件"，原理是application.html.erb使用yield关键字），action方法名默认和view文件名第一段对应。

action的原理是找到对应的html.erb文件，然后替换到application.html.erb中。action会首先申请具体的model，然后将model中填值并交给html.erb用。

Ruby on rails使用ORM，也就是说，可以快速生成DB建表语句的Ruby语言版，且Rails本身就自带父类，方便的继承并添加建表语句就好。

DB管理表的语句放在migration里面，Model层里面放的是表对应的ORM对象定义。Rails把模型的类名转换成复数，然后查找对应的数据表。

参考：https://ruby-china.github.io/rails-guides/active_record_basics.html

#### 常用命令
- rake routes 查看所有路由信息
- rails g controller <名字> 新建以名字命名的rb文件，里面定义action函数，函数中主要是对一些@修饰的变量进行赋值，以便在View端进行访问。
- rails g model <名字> 生成以名字命名的rb文件，里面应该写入数据库操作Schema的语句（建表、新删行等）。
- rake db:migrate 执行db/migrate下的rb文件里的数据库操作Schema的语句，也就是把model和DB进行打通（同步）。migrate文件按时间戳命名，防止多人开发时候的冲突。
- rails c 进入包含项目环境的irb控制台
- rails s 启动server

#### Restful常用路由

routes.rb 中加入: resource :events

| 访问方式 | action | Ruby默认Helper | 说明 |
|:---|:---|:---|:---|
| get '/events' | events#index | events_path
| post '/events' | events#create | events_path + 表单默认提交方式是post
| get '/events/:id' | events#show | event_path(event)
| patch '/events/:id' | events#update | event_path(@event) + 表单默认提交方式是patch
| delete '/events/:id' | events#destroy | event_path(@event) + 超链接指定method为delete
| get '/events/new' | events#new | new_event_path
| get '/events/:id/edit' | events#edit | edit_event_path(event)

#### 特殊语法

- filename.html.erb 其实是action.minetype.renderer
- Render关键字的意思是Render一个页面，并作为response返回；action函数中如果不写Render，默认都是取出函数名.html.erb并进行Render；使用redirect_to来跳转，这样好处是进入下一个页面，例如
点击"提交"按钮之后，由于包含提交按钮的页面里面往往有一些全局的提示弹窗，因此我们并不希望当前页面被刷新。
- 文件名需要是下划线分割的格式
- <%= %>与<%%>相比，不仅eval，而且会进行render

### Ruby程序部署到Docker

先复制Gemfile到容器，然后执行bundle install，最后拷贝源码到容器。

而不是：拷贝Gemfile和源码，然后执行bundle install。

原因：Dockerfile里面的命令都会创建中间镜像层，且都会缓存下来，只有在当前命令使得镜像变化的时候才会生成新的镜像层并缓存；

如果Ruby项目的Gemfile不变，变的是源码，那么这个镜像构建过程会快很多。

### DB migration

数据库Version概念：某个时间点的数据库表结构。

数据库Migration概念：Versions之间的表结构变化。

要做migration，可以通过rake db:migrate + 方法来做。该方法所属类需要继承ActiveRecord::Migration，
然后方法里面写入表操作语句。

所有migration之后的最终结果放入db/schema.rb。

操作举例：更新多个表

rails generate migration addFieldsForXXFeature  # 会在migration目录下创建带时间戳命名的migration文件

打开该migration文件，在其中加入更新各表格代码

进入rails部署（实际）环境，例如进入docker内，执行rake db:migrate，将根据migration文件在schema.rb中更新字段

删除migration：https://stackoverflow.com/questions/31843363/rails-how-to-delete-a-pending-migration/31843442

### Active Record

#### 关联关系

定义关联关系之后，写代码变简单了，例如定义作者和书一对多关系（飞雪连天射白鹿...，属于作者金庸、默认书只有一位作者），那么删除作者时候作者写的书也会被自动删除掉。

如果在表A中加入如下字段：

| 字段 | 说明 | 实际作用 | 举例 |
|:---:|:---:|:---:|:---:|
| belong_to B | 强调任何一个A对象只属于一个B对象，A对象（A1，A2）属于一个B（B1）对象 | 作用是在A里面加入B的id字段 | 在一个特定时间点：一个球员（例如梅西、哈维）只能属于一支球队（巴萨），球员信息里面加入所属球队的名字 | 编程环境中，通常会做出很多的限制而成立，例如规定一本书只有一个作者
| has_one B | 强调任何一个A对象只有一个唯一的B对象，例如A1对象只有一个B1对象，A2对象有一个B2对象（不能是B1） | 作用是在B里面加入A的id | 在一个特定时间点：一个俱乐部有多个球员，不符合只有一个的要求，因此不可以使用has_one；但一支球队（巴萨、皇马）只有一个主场（诺坎普、伯纳乌） | has_one在现实中例子很少（例如国米a米主场就是同一个），但是编程环境中通过做出限制而成立，例如一个用户只能有一个登陆账号
| has_many B | 强调任何一个A对象有多个B，一对多 | 在A里面维持一个B的集合 | 一个作者有很多书 |
| has_many B, through C | 有多个B，且是多对多 | 作用是采用中间表C记录A，B对应关系 |
| has_one B, through C | 一对一 | 采用中间表C | 我只有一个手机，手机只有一个主人，手机上的所有使用数据记录，属于我和手机共同作用 |
| has_and_belongs_to_many | 不采用中间表的多对多 | 适合建立简单的多对多关系 ||

belong_to和has_one结合使用就是一对一。

delegate :name, :to => :user
意思是：该表里面有name方法，但是实际上该方法是调用user的name方法。

### 其他

includes关键字：https://apidock.com/rails/ActiveRecord/QueryMethods/includes
作用是减少访问DB次数。