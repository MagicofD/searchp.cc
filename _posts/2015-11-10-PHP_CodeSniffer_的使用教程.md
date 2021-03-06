---
layout: post
title: PHP CodeSniffer
category: [PHP]
desc:  如何规范我们的 PHP 代码？人工的检查 OR 工具的检查。这篇文章介绍 PHP CodeSniffer 的使用，帮助 PHPer 修正代码格式，养成良好的编码习惯。
---

# 0x00 安装 PHP CodeSniffer #

## 安装 phpcs ##

phpcs 是 PHP 代码规范的检测工具。

```shell
# 下载
$ curl -OL https://squizlabs.github.io/PHP_CodeSniffer/phpcs.phar
# 加入到命令目录
$ mv phpcs.phar /usr/local/bin/phpcs
# 赋予执行权限
$ sudo chmod +x /usr/local/bin/phpcs
# 检验是否成功
$ phpcs -h
```

## 安装 phpcbf ##

phpcbf 是 PHP 代码规范的修复工具。

```shell
# 下载
$ curl -OL https://squizlabs.github.io/PHP_CodeSniffer/phpcbf.phar
# 加入到命令目录
$ mv phpcbf.phar /usr/local/bin/phpcbf
# 赋予执行权限
$ sudo chmod +x /usr/local/bin/phpcbf
#  检验是否成功
$ phpcbf -h
```

# 0x01 使用 phpcs #

## phpcs 配置 ##

1.查看详细配置。使用命令：```phpcs --config-show``` （下面是我当前的配置）

```shell
colors:                  1
default_standard:        PSR2
encoding:                utf-8
error_severity:          1
ignore_errors_on_exit:   1
ignore_warnings_on_exit: 1
report:                  json
report_format:           summary
report_width:            auto
severity:                1
show_progress:           1
show_warnings:           1
tab_width:               4
warning_severity:        5
```

2.设置默认的编码标准。（这个很重要，建议使用 ```PSR2``` 的标准）

```shell
# 查看配置
$ phpcs -i
The installed coding standards are MySource, PEAR, PHPCS, PSR1, PSR2, Squiz and Zend

# 设置编码标准为 PSR2
$ phpcs --config-set default_standard PSR2
```

3.隐藏警告。（当然，对于强迫症来说，警告都是不允许的，非强迫症患者可以使用此配置项）

```shell
# 隐藏警告提醒
$ phpcs --config-set show_warnings 0
# 开启警告提醒
$ phpcs --config-set show_warnings 1
```

4.显示检查进程。（如果项目需要检查的文件较多可以开启这个）

```shell
# 显示检查进程
$ phpcs --config-set show_progress 1
# 关闭进程显示
$ phpcs --config-set show_progress 0
```

5.显示颜色。 (给自己点颜色看看哈)

```shell
# 显示颜色
$ phpcs --config-set colors 1
# 关闭颜色显示
$ phpcs --config-set colors 0
```

6.修改错误和警告等级

```shell
# 显示所有的错误和警告
$ phpcs --config-set severity 1
# 显示所有的错误，部分警告 注意等级可有从 5-8 5 的警告显示会更多，8 的更少
$ phpcs --config-set severity 1
$ phpcs --config-set warning_severity 5 
```

7.设置默认编码

```shell
# 设置 utf-8
$ phpcs --config-set encoding utf-8
```

8.设置 tab 的宽度

```
# tab 为 4 个空格
$ phpcs --config-set tab_width 4
# 也可以对单独文件生效
$ phpcs --tab-width=0 /path/to/code
```

## 代码验证 ##

1.校验单个文件

```shell
# 校验单个文件
$ phpcs filename
```

2.校验目录（如：整个项目）

```shell
＃ 校验目录 注意这个时候别因为 linux 学的太好加个 -R 哈。
$ phpcs /path/dir
```

### 结果分析 ###

结果展现的形式：

full, xml, checkstyle, csv, json, emacs, source, summary, diff, svnblame, gitblame, hgblame or notifysend

指定展现形式：

```shell
# 汇总的形式
phpcs --report=summary test01.php

# json 形式 （个人觉得这个形式更清晰）
phpcs --report=json test01.php
```

# 0x02 修复代码 #

## 使用 phpcbf ##

覆盖式修复

```shell
# 直接覆盖
$ phpcbf /path/code
```

生成中间文件

```shell
# 生成新文件
$ phpcbf /path/to/code --suffix=.fixed
```

## 使用 diff ##

```
# 以 test.php 为例 会生成 test.php.diff 文件
$ phpcs --report-diff=test.php.diff test01.php
```

## 高级应用 ##

上面的配置足够适用一部分场景，但是不能完全适用所有的场景。比如，一些老的框架，它本身不是很符合 PSR 的规范。一个很简单的例子比如连命名空间都没有。这个问题怎么解决？这个高级应用模块就是来解决这个问题的。

1.新建自己的配置文件 （例子）

```xml
<?xml version="1.0"?> 
<ruleset name="My Standards"> 
    <description>My Coding Standards enforcement rule set for PHP_CodeSniffer</description> 
    <rule ref="PSR2.Classes.PropertyDeclaration" /> 
    <rule ref="PSR2.ControlStructures.ElseIfDeclaration" /> 
    <rule ref="PSR2.Files.EndFileNewline" /> 
    <rule ref="PSR2.Methods.MethodDeclaration" /> 
    <rule ref="PSR2.Namespaces.NamespaceDeclaration" />    
    <rule ref="PSR2.Namespaces.UseDeclaration" />  
    <rule ref="Squiz.Arrays.ArrayBracketSpacing" /> 
    <rule ref="Squiz.Arrays.ArrayDeclaration" /> 
    <rule ref="Squiz.PHP.DisallowSizeFunctionsInLoops" />  

    <!-- Removing Sniffs from Generic as we do not want these -->
    <rule ref="Generic"> 
        <exclude name="Generic.CodeAnalysis.ForLoopShouldBeWhileLoop" />       
        <exclude name="Generic.Commenting.Fixme" /> 
        <exclude name="Generic.Commenting.Todo" /> 
        <exclude name="Generic.Files.EndFileNoNewline" /> 
        <exclude name="Generic.Files.LineEndings" /> 
        <exclude name="Generic.Files.LineLength" /> 
        <exclude name="Generic.Files.OneClassPerFile" /> 
        <exclude name="Generic.Files.OneInterfacePerFile" /> 
        <exclude name="Generic.Formatting.NoSpaceAfterCast" /> 
        <exclude name="Generic.Functions.OpeningFunctionBraceKernighanRitchie" /> 
        <exclude name="Generic.NamingConventions.CamelCapsFunctionName" /> 
        <exclude name="Generic.PHP.ClosingPHPTag" /> 
        <exclude name="Generic.PHP.LowerCaseConstant" /> 
        <exclude name="Generic.VersionControl.SubversionProperties" /> 
        <exclude name="Generic.WhiteSpace.DisallowSpaceIndent" /> 
        <exclude name="Generic.WhiteSpace.DisallowTabIndent" /> 
        <exclude name="Generic.Files.LowercasedFilename" /> 
    </rule> 
</ruleset> 
```

2.使用自定义配置的标准

```
phpcs --standard=/path/to/ruleset.xml /path/to/code
```

当然，我们自身可能对 PSR 的标准不太清楚。那么请参考 [PSR2-ruleset.xml]。


# 0x03 总结 #

记住，这只是一个工具。但是，```工欲善其事，必先利其器```。这里可以打个小广告 [利器] 里面是有很多好用的工具的。

如果，你需要应用到团队，需要看你团队使用的什么框架。然后，根据框架适当的调整一下配置的 **错误等级** 和 **警告等级**。

# 0x04 参考链接 #

* [PHP CodeSniffer wiki]

* [Configuration Options]


[Configuration Options]:https://github.com/squizlabs/PHP_CodeSniffer/wiki/Configuration-Options#ignoring-warnings-when-generating-the-exit-code
[PHP CodeSniffer wiki]:https://github.com/squizlabs/PHP_CodeSniffer/wiki
[PSR2-ruleset.xml]:https://github.com/squizlabs/PHP_CodeSniffer/blob/master/CodeSniffer/Standards/PSR2/ruleset.xml