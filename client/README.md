# Client Log

## 3月8日更新

这一周都在忙着看各种语言写代码，测试所需功能，这里补充一个AngularDart的整体说明。

这里先从开发角度解释结构连接，直到最后才会部署

### 设计思路

现在的框架设计基本是将庞大的功能需求划分为小的独立的模块，每个模块可以单独调试开发，再依据模块功能给出一个逻辑清晰的模板。

AngularDart是Google用Dart语言重写的Angular框架。在AngularDart中，这种最小模块定义为**Component**，这个Component包括绑定了视图、数据与操作。

### Component目录结构

由于AngularDart是用于Web app的，所以单独一个Component的结构如下(里面的html和css是针对此模块封装的)，后面的解释将会基于此开展：

```bash
└─todo_list
    └─todo_list_component.html # 定义视图构成
    └─todo_list_component.css  # 定义html样式
    # 配置component，在视图生命周期的正确位置连接data/action等等
    └─todo_list_component.dart
    # 任何func都可称为service，比如拉数据，比如计算
    └─todo_list_service.dart
```

#### todo_list_component.dart

上述命名并无强制规范，并不需要都是用同一个名字，后面加component，它们之间的关联是定义在todo_list_component.dart文件中。AngularDart中并不能自动import，每一个用到的部分都需要在@Component中注册。

```dart
 ...
 // @Component关键字，在build的时候表明自己是一个component，是Angular识别它的依据
 // 这是component配置部分
 @Component {
   // 前面提到css封装，selector指明styleUrls中的css文件只适用于'todo-list'节点
   selector: 'todo-list',
   styleUrls: ['todo_list_component.css'],
   // component的html
   templateUrl: 'todo_list_component.html',
   // directives可以看作功能性视图的import
   directives: [ MaterialIconComponent, NgFor, ...],
   // 用到了哪些服务类
   providers: [ClassProvider(TodoListService)],
 }

```

每个视图也有[lifecycle](https://webdev.dartlang.org/angular/guide/lifecycle-hooks)，比如说这个component引用了其他component，适用于在了解视图状态，并在固定时间点做一些处理。

```dart
@Component { ... }

class TodoListComponent implements OnInit {
  ...
  final TodoListService todoListService;
  TodoListComponent(this.todoListService);

  // fetch data in onInit
  @override
  Future<Null> ngOnInit() async {
    items = await todoListService.getTodoList();
  }
  ...
}

```

#### todo_list_service.dart

我本该再写完的...不过半天假在召唤我.....


## 3月7日更新
看网上课程时，总觉得自己多出了许多神奇的能力，可以写出优美明确又有效率的代码。但现实是，一个你觉得很简单的问题，花了几个小时来找究竟哪里写错了.....

### Global样式变量是必需的，这需要scss

global样式中只定义了theme和accent颜色，其他都是从这两色计算得来（比如重要文字是theme颜色变深40%等），定义了不同重要程度的文字大小等，这样比较协调一致，并且不同项目中修改也非常便利。

angulardart中使用scss并关联，需要如下步骤（网上解答很多，也许是哪里设置的不对，唯有以下这种我尝试成功）：
1. add a build.yaml in root dir, in this case is client dir adding following lines:
```yaml
targets:
  $default:
    builders:
      angular_components|scss_builder:
        enabled: True
```
2. in my case I add a _styles.scss as a globle styling file then in other files could using following to refer it: (path depending on where to access it)
```scss
@import 'styles';
```
3. in component dart file, it should be linked as:
```dart
@Component {
  ...
  styleUrls: ['app_component.scss.css'],
  ...
}
```

### Font is a pain
国内访问不到google api，但是对于数字显示，极细字体是必要的，于是需要load本地字体（只是Roboto-Light.ttf，只有78k，我觉得应该不会影响太多）。

这个路径让我真是头疼了好久，语法明明很简单，它就是找不到...搜了许久，大家总是讨论如何写这个语法...可能他们都不是新手吧，最终找到了正确显示的方法，还好这只需要定义一次。

1. 在web文件夹下建立assets/fonts文件夹，将字体放在这里；

2. 在web文件夹下的styles.css文件中添加：
```css
@font-face {
  font-family: 'roboto-light';
  /* this url refers to AFTER build script, 
  * under the web dir, exists an ASSETS dir,
  * which made me crazy... afterall, china cannot access google api
  */
  src: url('/assets/fonts/Roboto-Light.ttf') format("truetype");
  font-weight: normal;
}
```
3. 引用时的scss文件：
```scss
@mixin text-style {
    ...
    font-family: 'roboto-light';
    ...
}
```
