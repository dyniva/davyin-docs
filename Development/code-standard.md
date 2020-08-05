# Drupal编码规范
Drupal编码标准与版本无关，并且“始终是最新的”。无论（核心）版本如何，所有新代码都应遵循当前标准。旧版本中的现有代码可能会更新，但不一定必须更新。特别是对于较大的代码库（如Drupal核心），更新当前标准的先前版本的代码可能过于庞大。但是，当前版本的代码应遵循当前标准。
注意：不要将编码标准更新/清理压缩到其他不相关的补丁中。只触摸实际相关的代码行。要更新当前标准的现有代码，请始终创建单独的专用问题和修补程序。
编码规范请参照drupal官网规范文档
https://www.drupal.org/docs/develop/standards
## 缩进与空白字符（Indenting and Whitespace）
-	使用 2 个空格而不使用 tab 键进行代码缩进（notepad++, Eclipse 等编辑器均支持此项配置）;
-	行尾不应该有空白字符
-	应使用 \n （Unix换行符），而不是 \r\n （Windows 换行符）
-	所有文件均应以一个空行结尾
## 运算符（Operators）
-	所有二元运算符（二个值之间的运算符），如 +, -, =, !=, ==, > 等等，在运算符两端均需留有一个空格，如应该使用 $foo = $bar 而不是 $foo=$bar。
-	所有一元运算符（只操作一个值班的运算符），例如 ++，在值与运算符之间则不应加入空格。
2.3	类型转换（Casting）
-	在 (type) 与要转型的变量之间应加入一个空格，如 (int) $mynumber.
## 控制结构（Control Structures）
控制结构包含 if, for, while, switch 等等，下面是一个简单的 if 语句结构示例：
```
if (condition1 || condition2) {
  action1;
}
elseif (condition3 && condition4) {
  action2;
}
else {
  defaultaction;
}
```
控制语句的关键词与左边括号之间应该有一个空格，以此来与函数调用进行区分。
即使在大括号是可选的情况下，也应当总是使用大括号。这样可以加强代码的可读性以及减少因嵌套带来的逻辑错误。
switch 语句结构示例：
```
switch (condition) {
  case 1:
    action1;
    break;

  case 2:
    action2;
    break;

  default:
    defaultaction;
}
do-while 语句结构示例：
do {
  actions;
} while ($condition);
```
## 行长度与封装（Line length and wrapping）
-	通常情况下，每行代码的长度不应超过80个字符
-	以下情况，行长度可超过80个字符：当行内包含过长的函数名称、函数/类定义、变量声明等
-	为方便阅读和理解，控制结构的行长度可超过80个字符
```
if ($something['with']['something']['else']['in']['here'] == mymodule_check_something($whatever['else'])) {
  ...
}
if (isset($something['what']['ever']) && $something['what']['ever'] > $infinite && user_access('galaxy')) {
  ...
}
// Non-obvious conditions of low complexity are also acceptable, but should
// always be documented, explaining WHY a particular check is done.
if (preg_match('@(/|\\)(\.\.|~)@', $target) && strpos($target_dir, $repository) !== 0) {
  return FALSE;
}
```
-	控制条件（condition）不应该写作多行
-	控制条件应该适当拆分以便于阅读和理解，编写代码时要避免以下情形：
```
if ((isset($key) && !empty($user->uid) && $key == $user->uid) || (isset($user->cache) ? $user->cache : '') == ip_address() || isset($value) && $value >= time())) {
  ...
}
```

将控制条件进行拆分，不仅便于阅读，同时也方便添加注释让人知道为何进行这样的条件判断
```
<?php
  // Key is only valid if it matches the current user's ID, as otherwise other
  // users could access any user's things.
  $is_valid_user = (isset($key) && !empty($user->uid) && $key == $user->uid);

  // IP must match the cache to prevent session spoofing.
  $is_valid_query = (isset($user->cache) ? $user->cache == ip_address() : FALSE);

  // Alternatively, if the request query parameter is in the future, then it
  // is always valid, because the galaxy will implode and collapse anyway.
  $is_valid_query = $is_valid_cache || (isset($value) && $value >= time());

  if ($is_valid_user || $is_valid_query) {
    ...
  }
?>
```
##	函数调用（Function Calls）
调用函数时，函数名与左括号之间没有空格，除最后一个参数外，每个参数后的 , 都应跟上一个空格，如：
```
<?php
$var = foo($bar, $baz, $quux);
?>
```
如之前所说，等号两边应该各有一个空格。当有一系列相关语句时，出于可读性的考虑，可以适当增加空格的数量，如：
```
<?php
$short         = foo($bar);
$long_variable = foo($baz);
?>
```
##	函数声明（Function Declarations）
包含默认值的参数应当放在最后，当函数拥有返回值时，尽量返回便于理解的值：
```
<?php
function funstuff_system($system, $field = 'description') {
  $system["description"] = t("This module inserts funny text into posts randomly.");
  return $system[$field];
}
?>
```
##	类构造器调用（Class Constructor Calls）
当调用不带参数的类构造器时，始终包含括号
```
<?php
$foo = new MyClassName();
?>
```
带参数的类构造器
```
<?php
$foo = new MyClassName($arg1, $arg2);
?>
```
如果使用变量做为类名，需先为变量赋值，然后才调用类构造器：
```
<?php
$bar = 'MyClassName';
$foo = new $bar();
$baz = new $bar($arg1, $arg2);
?>
```
##	数组（Array）
数组的值之间应使用空格分隔，赋值操作符号（=>）左右也应包含空格：
```
<?php
$some_array = array('hello', 'word', 'foo' => 'bar');
?>
```
当声明数组的字符长度超过80个字符（通常在构造表单和菜单时），应该将各元素分行、缩进编写：
```
<?php
$form['title'] = array(
  '#type' => 'textfield',
  '#title' => t('Title'),
  '#size' => 60,
  '#maxlength' => 128,
  '#description' => t('The title of your node.'),
);
?>
```
注意：最后一个数组元素末尾有一个逗号，这并不是手误，而是避免有新元素加入到最后之后因缺少逗号而出现解析错误。（从某种程度上来讲，在最后一个数组元素末尾加上逗号是一种推荐的做法，甚至在向drupal.org提交代码时，一些代码规范检测脚本会因为最后一个元素没有添加逗号而出现警告提示。）
##	引号（Quotes）
Drupal 对于单引号和双引号的使用并没有很强硬的标准，只需在同一模块内保持用法的统一即可。
使用单引号的效率要高于双引号，因为解析器不需要到引号之间查找变量。以下是使用双引号的两种情况：
-	引号中间带有变量，如
```
"<h2>$header</h2>"
```
-	引号中间带有单引号，使用双引号可避免对单引号的转义
"He's a good person."

当然也可以使用单引号，但 .pot 解析器不能很好的处理这种情况，而且看起来怪怪的
'He\'s a good person.'
2.11	字符串连接（String Concatenations）
在点与要连接字符串之间需要加入空格以加强代码可读性：
```
<?php
$string = 'Foo' . $bar;
$string = $bar . 'foo';
$string = bar() . 'foo';
$string = 'foo' . 'bar';
?>
```
如果只是简单地连接变量，可以使用双引号
```
<?php
$string = "foo $bar";
?>
```
使用连接赋值符（.=）时，需要在符号两侧预留空格
```
<?php
$string .= 'Foo';
$string .= $bar;
$string .= baz();
?>
```
##	注释（Comment）
注释规范单独在 Doxygen及注释格式规范页面 讨论
引入代码（Including Code）
任何无条件引用文件的情况下，使用 require_once(), 任何有条件引用文件的情况，则使用 include_once(). 这两条语句都会保证文件只被引入一次。
当从当前目录或子目录引入代码时，始终以点路径开头
```
<?php
include_once ./includes/mymodule_formatting.inc
?>
```
在 Drupal 7 及更新版本中，使用 DRUPAL_ROOT 常量：
```
<?php
require_once DRUPAL_ROOT . '/' . variable_get('cache_inc', 'includes/cache.inc');
?>
```
##	PHP 代码标签（PHP Code Tags）
始终使用<?php ?>来界定PHP代码而不使用要 ?>。这是为了遵循Drupal规范，同时也便于代码在其它系统和平台中被引用。
自 Drupal 4.7 开始，最后的 ?> 都故意被忽略不写，原因如下：
-	移除它可以避免在文件末尾出现空白字符，这些空白字符可能导致“文件头已发送（header already sent）”错误，XHTML/XML验证错误，及其它问题
-	PHP 官方说明结尾的PHP界定符是可选项
-	PHP.net 自身也移除了文件末尾的界定符（如 prepend.inc ）
##	分号（Semicolons）
PHP 语言要求除了代码块以外，大多数行尾都要跟上分号。Drupal 代码规范同样有此要求，并且对于代码块也是如此。以下是一个单行代码块的示例：
<?php print $tax; ?> -- YES
<?php print $tax ?> -- NO
示例 URL（Example URL）
使用 example.com 表示所有示例 URLs
##	命名规范（Naming Conventions）
###	函数与变量（Functions and Variables）
函数与变量名称应该使用小写字母，且单词之间使用下划线分隔。函数应该使用模块组/模块名称作为前缀，以避免与不同模块间的冲突。
###	持久变量（Persistent Variables）
持久变量是指通过 variable_get()/variable_set() 函数取得和设置的变量，变量名称应该使用小写字母，且单词之间使用下划线进行分隔。持久变量也应该使用模块组/模块名称作为前缀，以避免与不同模块间的冲突。
###	常量（Constants）
-	常量始终要求使用全大写字母，且单词之间使用下划线进行分隔。（包括PHP内置常量 TRUE, FALSE, NULL）
-	模块中定义的常量需始终使用大写的模块名称作为前缀。
-	在 Drupal 8 及之后，应使用 const 关键词代替 define() 函数来定义常量，因为效率更高
```
<?php
/**
* Indicates that the item should be removed at the next general cache wipe.
*/
const CACHE_TEMPORARY = -1;
?>
```

注意 const 不能用于PHP表达式，因此在条件判断和非字面值（non-literal value ???）时，还是应当使用 define() 函数
```
<?php
if (!defined('MAINTENANCE_MODE')) {
  define('MAINTENANCE_MODE', 'error');
}
?>
```
2.15.4	全局变量（Global Variables）
定义全局变量时，应当使用下划线加模块/主题名称开头
2.15.5	类（Class）
类名应使用驼峰式命名（即单词首字母大写）
```
<?php
abstract class DatabaseConnection extends PDO {
?>
```
类中的方法（函数）和属性（成员变量）应使用首字母小写的驼峰式
```
<?php
public $lastStatement;
?>
```
定义访问权限时，使用 protected 而代替 private，从而其它的类可以在必要时扩展和更新方法。Protected 和 public 函数和变量不应以下划线开头。
更多关于面向对象的编码规范
###	文件名（Filename）
所有文档文件都应加上 .txt 后缀，以便于 Windows 用户查看。同时，所有文件名称应该全部大写，而文件后缀应该全部小写。
如 README.txt, INSTALL.txt, TODO.txt, CHANGELOG.txt 等等。
##	辅助模块及工具
-	Coder 模块：可以遵循部分以上代码规范，对代码进行审查及修改建议
-	Drupal Code Sniffer：代码规范检测工具
-	PAReview.sh：还处理沙盒中的代码规范检测脚本，几乎严格遵守以上所有代码规范并给出修改建议。
