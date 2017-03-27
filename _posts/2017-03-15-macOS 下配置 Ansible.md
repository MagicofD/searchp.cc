---
layout: post
title: macOS 下配置 Ansible
category: [DevOps]
desc: Ansible is Simple IT Automation. Ansible 是一个简单的 IT 自动化利器。这边文章将简单介绍如何在 macOS 上安装并且使用 Ansible。
---


# 0x00 安装 #

只需要一条命令，前提是已经安装了 [Homebrew]。

```
brew install ansible
```

# 0x02 配置 #

安装完之后可以在如下目录找到 Ansible 相关文件：

* /usr/local/Cellar/ansible/2.2.1.0_2  # 安装的目录
* /usr/local/opt/ansible # 软链过来的最新的执行版本

但是，找来找去就是没有找到 Ansible 的配置文件，这个时候需要我们自己创建配置文件。

```
# 创建 Ansible 配置文件
touch ~/.ansible.cfg

# 创建 Ansbile 配置文件夹
mkdir /usr/local/etc/ansible/

# 创建 Ansible 依赖的操作 hosts 文件
touch /usr/local/etc/ansible/hosts
```

在常见的文件内写入配置内容：

指定 hosts 文件的路径

```
# vim ~/.ansible.cfg
[defaults]
inventory = /usr/local/etc/ansible/hosts
```

填充 hosts 文件的内容

```
# vim /usr/local/etc/ansible/hosts
[xxxx]
ip or hostname
```

# 0x03 使用 #

```
# 测试联通性 注意使用 ssh-copy-id 进行公钥登陆授权
ansible -m ping all

192.168.xxx.xxx | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
192.168.xxx.xxx | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

# 0x04 参考 #

* [ansible-tran]

[ansible-tran]:http://ansible-tran.readthedocs.io/en/latest/
[Homebrew]:https://brew.sh/index_zh-cn.html