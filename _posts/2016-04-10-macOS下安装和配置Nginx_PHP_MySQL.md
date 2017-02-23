---
layout: post
title: macOS 下安装和配置 Nginx、PHP 和 MySQL
category: [macOS]
desc:  很多人都说 PHP 是最好的语言，也有很多人说 macOS 是最好的界面化操作系统，那么最好的语言如何在最好的系统上运行并工作呢？这篇文章将详细的进行解读。当然，事实上我已经转战 Lua，Python，Go...
---

# 0x00 准备#

## Homebrew 目录 ##
macOS 下怎么安装软件？当然，选择人见人爱的谁用谁爽的 [Homebrew]。使用 [Homebrew] 安装的软件的一些常见的目录如下：

| 说明  | 目录 |
| ------------- | ------------- |
| 软件安装位置  | ```/usr/local/Cellar/```  整个文件软链到 ```/usr/local/opt/``` 执行命令软链到  ```/usr/local/bin```|
| 软件配置文件位置  | ``` /usr/local/etc ```  |

## Homebrew 命令 ##

当然，事实上你直接键入 ```brew --help``` 就可以看到如下信息:

```
# 基本上见名知意
Example usage:
  brew search [TEXT|/REGEX/]
  brew (info|home|options) [FORMULA...]
  brew install FORMULA... 
  brew update
  brew upgrade [FORMULA...]
  brew uninstall FORMULA...
  brew list [FORMULA...]

Troubleshooting:
  brew config
  brew doctor
  brew install -vd FORMULA

Developers:
  brew create [URL [--no-fetch]]
  brew edit [FORMULA...]
  http://docs.brew.sh/Formula-Cookbook.html

Further help:
  man brew
  brew help [COMMAND]
  brew home
```

# 0x01 安装#

macOS 有了 [Homebrew] 安装软件变得非常简单。下面总共四条命令就可以完成整个 PHP，Nginx，MySQL 的安装。

```
# 安装 PHP
brew tap josegonzalez/php
brew install php71
# 安装 Nginx
brew install nginx
# 安装 MySQL
brew install mysql
```

# 0x02 配置#

## PHP 的配置 ##

修改 PHP 的启动用户。PHP 的配置文件路径为：```/usr/local/etc/php/7.1/php-fpm.d/www.conf```

```
# 将 PHP 的启动用户修改为 nobody
user = nobody
group = nobody
```

## Nginx 的配置 ##

修改 Nginx 的默认端口并且添加 default.conf 作为测试配置。Nginx 的配置文件路径为 ```/usr/local/etc/nginx/nginx.conf```

```
# 在 http {} 生效 include servers/*.conf;
include servers/*.conf;
```

在 ```/usr/local/etc/nginx/servers``` 里面加入 ```default.conf```  内容如下：

```
server {
    listen       80;
    server_name  default.com;
    
    location / {
        # 项目目录 注意启动用户对该目录的权限
        root   /usr/local/data/code/PHP/self/default;
        index  index.php;
    }

    location ~ \.php$ {
        # 项目目录 注意启动用户对该目录的权限
        root    /usr/local/data/code/PHP/self/default;
        # fastcgi 监听
        fastcgi_pass   127.0.0.1:9000;
        # 也可以使用 socket 方式，注意需要配置 php-fpm 的监听方式为 socket
        # fastcgi_pass   unix:/usr/local/var/run/php-fpm71.sock; 
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}

```

注：为了测试，在 ```/usr/local/data/code/PHP/self/default```，里面加入 index.php 并且输出 ```phpinfo()```。

## MySQL 的配置 ##

```
# 启动 MySQL
mysql.server start
# 按照提示设置密码
mysql_secure_installation
```

## HOSTS 的配置 ##

```
sudo echo "127.0.0.1     default.com" >> /etc/hosts
```

# 0x02 运行#

## 启动 PHP ##

指定配置文件进行启动

```
sudo  /usr/local/Cellar/php71/7.1.1_12/sbin/php-fpm -c /usr/local/etc/php/7.1/php.ini -y /usr/local/etc/php/7.1/php-fpm.conf -D
```

## 启动 Nginx ##

```
sudo nginx
```

## 启动 MySQL ##

```
mysql.server start
```
# 0x03 效果显示#

<img src="/assets/images/2017_02_10_phpinfo.png" title="jump　server" class='img-responsive'>

# 0x04 总结#

安装的过程之中肯定会有很多疑问。后续会陆续发布 ```Nginx``` 的详细的配置讲解，```PHP``` 的详细的配置的讲解，```MySQL``` 的详细的配置的讲解。

[Homebrew]:http://brew.sh/index_zh-cn.html
