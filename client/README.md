# Client Log

## 3月7日更新
看网上课程时，总觉得自己多出了许多神奇的能力，可以写出优美明确又有效率的代码。但现实是，一个你觉得很简单的问题，花了几个小时来找究竟哪里写错了.....

### Global样式变量是必需的，这需要scss

global样式中定义了theme和accent颜色，其他都是从这两色计算得来（比如重要文字是theme颜色变深40%等），定义了不同重要程度的文字大小等，这样比较协调一致，并且不同项目中修改也非常便利。

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
