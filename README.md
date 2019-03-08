# Aqueduct & AngularDart Deployment

## 3月7日更新

由于我并没有网络前端经验，最开始我总以为静态页面需要一台服务器或是“某种程序”来提供、运行、管理它，因为我觉得页面中包含的是逻辑视图，正如java servlet模式中的servlet+jsp，这些servlet+jsp需要一个例如tomcat的容器来管理它们，在现有的例子中，aqueduct就是tomcat，angulardart app就是servlet+jsp。

这样想也对，也不对。如果非要类比的话，aqueduct应该相当于tomcat+servlet，angulardart app相当于jsp。一个*编译好*的angulardart程序，是一套可以访问的html页面。angulardart只是一个方便开发的框架，它的概念*只存在于开发过程*，使用它可以有条理分模块地开发逻辑，然后由编译器将他们整理成html页面+css格式+js逻辑功能+assets资源文件的形式，其他终端只要能访问到这个文件，就可以由终端上的浏览器来解析显示它。从目前的需求上来说，完全可以不要aqueduct。

aqueduct也是一个架子，它是真正运行在服务器上的一个进程，里面写好了路径匹配，多线程管理，数据库访问以及模块化管理等。它的中文名字是管道，里面的逻辑结构我很喜欢，因为清晰明确。

于是我想，虽然angulardart中提供了页面跳转，可是页面与页面之间并没有什么逻辑联系，比如说身份验证页面与房屋信息显示页面。dart中也提供了http支持，包括路由匹配数据库访问，可是模块结构仍然要自己建立。那不如就将页面间的跳转交由aqueduct来管理，每个页面都是单独的angulardart app，于我来说模块越细越独立，越方便调试管理。

我缺乏经验来判断，很难说这是不是一个坏主意，但好在目前的项目需求还比较简单，就算以后修改也不会很困难。

## 3月6日更新 

### Structure

这个project使用**Dart**语言开发。

**Server**目录下是一个aqueduct工程，它作为服务器运行，接收http消息，路由至指定的controller对消息进行处理。

**Client**目录下是各个页面的angulardart工程，做用户UI交互。

### Deployment

*Server*与*Client*目录下的代码都可以独立编译开发，若是使用，需要先将client目录下的agulardart app编译成html格式，然后使用aqueduct路由至该位置。

启动auqeduct服务器，使用如下命令：
```bash
# get dependencies
pub get
# start aqueduct server
aqueduct serve
# above command will omit config written in main.dart
# if specify port or threads should use:
dart ./bin/main.dart
```

AngularDart可以在本地调试，它会启动一个调试服务器挂在指定接口，用浏览器访问即可；部署时，需要将其打包成html文件，然后放在需要访问的路径下。
```bash
# get dependencies
pub get
# if debug on local server, could use
webdev serve
# webdev use 8080 as default, and test on 8081
webdev serve web:5100 test:5101
# when deploy, should compile files into javascript files, using
# then it would generate files in build dirctory
pub run build_runner build -r -o web:build
```

### Initial Version

现在的框架都提供了便利的初始工程生成命令，如下。这些工程都是生成后，编译一下就可以用了。
```bash
# angulardart part, should get into target dir
stagehand web-angular
# aqueduct part
aqueduct create project_name
```
