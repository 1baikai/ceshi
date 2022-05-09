# PTK

## 在线安装 PTK

```shell
curl --proto '=https' --tlsv1.2 -sSf https://cdn-mogdb.enmotech.com/ptk/install.sh | sh
```

## 安装包下载地址

- https://cdn-mogdb.enmotech.com/ptk/latest/ptk_darwin_arm64.tar.gz
- https://cdn-mogdb.enmotech.com/ptk/latest/ptk_darwin_x86_64.tar.gz
- https://cdn-mogdb.enmotech.com/ptk/latest/ptk_linux_arm64.tar.gz
- https://cdn-mogdb.enmotech.com/ptk/latest/ptk_linux_x86_64.tar.gz
- https://cdn-mogdb.enmotech.com/ptk/latest/ptk_windows_x86_64.tar.gz

## 数据库部署

- 生成配置模板

```shell
ptk template [--single|--cluster] > topo.yaml
```

- 执行安装

```shell
# 不指定 topo.yaml 时为本地单实例安装
ptk [-f topo.yaml] install
```


## 使用手册

### 配置说明

1. 通过`ptk template [--single|--cluster] > topo.yaml` 生成模板配置文件

- --single 打印单实例安装配置模板
- --cluster 打印集群安装配置模板

2. 根据需求修改自己的配置


```
global:
# # 集群名称，必填项
cluster_name: ""
# # 运行 MogDB 数据库的系统用户
# user: "omm"
# # 如果用户所属的组与user不同，则使用Group指定用户所属的组名
# group: "omm"
# # 系统用户密码
# user_password: ""
# # 数据库密码
# db_password: ""
# # 数据库监听端口, 实例级别未设置时使用该值
# db_port: 26000
# # 数据库部署文件、启动脚本和配置文件的存放路径
# app_dir: "/opt/mogdb/app"
# # MogDB运行日志和操作日志存储目录
# log_dir: "/opt/mogdb/log"
# # 数据库数据存储目录
# data_dir: "/opt/mogdb/data"
# # 工具目录
# tool_dir: "/opt/mogdb/tool"
# # tmp 目录
# tmp_dir: "/tmp"
# # corefile 目录
# core_file_dir: ""
# # ssh配置信息，可以通过实例级的字段覆盖该配置
# ssh_option:
#    port: 22
#    user: root
#    password: ""
#    key_file: ""
#    pasphrase: ""
#    conn_timeout: "5s"
#    exec_timeout: "1m"
#    proxy:
#      host: ""
#      user: ""
#      password: ""
#      key_file: ""
#      pasphrase: ""
#      conn_timeout: "5s"
#      exec_timeout: "1m"


db_servers:
- host: "10.0.1.100"
# # 数据库监听端口
# db_port: 26000
# # 用于主备复制的IP，不填默认使用 host 字段
# ha_ips:
# - "10.0.2.100"
# # 主备复制端口，不填默认使用数据库监听端口
# ha_port: 26000
# # 数据库实例角色, 支持 primary, standby, cascade_standby
role: "primary"
# # AZ名称
# az_name: "AZ1"
# AZ优先级，越小优先级越高
# az_priority: 1
# # 当实例角色为级联备时，需设置上游备库IP
# upstream_host: ""
# # 启动数据库之前将该列表设置到 postgresql.conf 中
# db_conf:
#   replication_type: 1
# # ssh连接信息，如果不配置，使用 global 级别的认证信息
# ssh_option:
#    port: 22
#    user: root
#    password: ""
#    key_file: ""
#    pasphrase: ""
#    conn_timeout: "5s"
#    exec_timeout: "1m"
#    proxy:
#      host: ""
#      user: ""
#      password: ""
#      key_file: ""
#      pasphrase: ""
#      conn_timeout: "5s"
#      exec_timeout: "1m"
- host: "10.0.1.101"
# db_port: 26000
# ha_ips:
# - "10.0.2.101"
# ha_port: 26000
role: "standby"
# az_name: "AZ1"
# az_priority: 1
# upstream_host: ""
# db_conf:
#   replication_type: 1
# ssh_option:
#    port: 22
#    user: root
#    password: ""
#    key_file: ""
#    pasphrase: ""
#    conn_timeout: "5s"
#    exec_timeout: "1m"
#    proxy:
#      host: ""
#      user: ""
#      password: ""
#      key_file: ""
#      pasphrase: ""
#      conn_timeout: "5s"
#      exec_timeout: "1m"
```
<!--注意事项:在使用ssh 远程操作的时候，用户需要时root或者有sudo权限-->

### 命令说明
```shell
PTK是一款部署和管理MogDB数据库集群的命令行工具

Usage:
ptk [flags] [command] [args...]
ptk [command]

Available Commands:
checkos     检查或设置集群服务器系统参数
env         打印PTK环境变量信息
install     基于给定的拓扑配置部署MogDB数据库集群
ls          列出所有MogDB集群列表
gen-xml     生成XML文件
template    打印拓扑配置模板
uninstall   卸载 MogDB 数据库集群
start       启动数据库实例或集群
stop        停止数据库实例或集群
restart     重启数据库实例或集群
status      显示数据库实例或集群状态
query       查询集群运行时信息
help        Help about any command
completion  Generate the autocompletion script for the specified shell

Flags:
-f, --file string         指定数据库集群拓扑配置文件
-h, --help                打印PTK帮助信息
--log-file string     指定日志输出文件
--log-format string   指定日志输出格式，可选项: [text, json] (default "text")
--log-level string    指定日志级别，可选项: [debug, info, warning, error, panic] (default "info")
--self-upgrade        ptk自升级
-v, --version             打印PTK版本

Use "ptk [command] --help" for more information about a command.

```

### 命令详情

#### 1. checkos命令总览--系统参数检查
- ptk checkos --help
```shell
Check or set cluster os parameters.
To check all items, enter "-i A".
To check multiple status, enter the items in the following format: "-i A1,A2,A3".
Item number description:
'A1':[ OS version status ]
'A2':[ Kernel version status ]
'A3':[ Unicode status ]
'A4':[ Time zone status ]
'A5':[ Swap memory status ]
'A6':[ System control parameters status ]
'A7':[ File system configuration status ]
'A8':[ Disk configuration status ]
'A9':[ Pre-read block size status ]
'A10':[ IO scheduler status ]
'A11':[ Network card configuration status ]
'A12':[ Time consistency status ]
'A13':[ Firewall service status ]
'A14':[ THP service status ]

Usage:
ptk checkos [flags]

Flags:
--detail          打印每个检查项的详细信息
-h, --help            help for checkos
-i, --item string     指定检查项编号，多个编号间使用英文逗号分隔 (default "A")
-o, --output string   指定结果输出文件路径
```

- ptk checkos 使用示例

```shell
ptk checkos -i A1,A2,A3   # 要检查多个状态，请按以下格式输入项目：“-i A1,A2,A3”。
ptk checkos -i A          # 检查全部检查项
ptk checkos -i A --detail # 加上--detail 会显示详细信息
```

#### 2. 单实例和集群安装

- ptk innstall --help

```shell
Flags:
-y, --assumeyes                    自动对所有提问回复Yes
--db-version string            指定数据库版本，如果采用下载链接方式安装 (default "2.1.1")
-e, --env strings                  环境变量将被添加到系统用户的配置文件中
--gsinitdb-opt strings         gs_initdb 的参数，将在初始化时使用
-h, --help                         help for install
--launch-db-timeout duration   启动数据库的超时时间 (default 5m0s)
-p, --pkg string                   指定数据库安装包路径或下载链接
--post-run string              该 Bash 脚本将在执行部署数据库之前分发到每个服务器上运行
--pre-run string               该 Bash 脚本将在执行部署数据库成功后分发到每个服务器上运行
--skip-check-distro            跳过检测系统发行版，直接安装
--skip-create-user             跳过创建系统用户
--skip-launch-db               初始化数据库后，跳过启动数据库
--skip-rollback                当部署失败时，不执行回滚操作
--skip-set-os                  跳过设置系统参数

Global Flags:
-f, --file string         指定数据库集群拓扑配置文件
--log-file string     指定日志输出文件
--log-format string   指定日志输出格式，可选项: [text, json] (default "text")
--log-level string    指定日志级别，可选项: [debug, info, warning, error, panic] (default "info")
-v, --version             打印PTK版本

```

- 单实例安装

- 集群安装

#### 3. 单实例和集群的卸载

卸载MogDB数据库集群

- 通过配置文件 topo.yaml 是用户自己生成的yaml
```shell
ptk unstall -f topo.yaml
```

- 通过集群名字来卸载
```shell
ptk unstall --name CLUSTER_NAME
```

#### 4. 基于ptk配置文件生成OM使用的xml

根据topo.yaml (用户配置的yaml)生成适用于OM工具的yaml文件

- 使用方式

```shell
ptk gen-xml -f topo.yaml
```

如果需要指定路径可在命令后加 `-o` 可输出到指定路径（默认是ptk当前路径）

#### 5. 实例和集群管理功能

- 启动 (start)

```shell
Usage:
ptk start [flags]

Flags:
-D, --data-dir string        path of data dir
-h, --help                   help for start
-H, --host string            ip of host to be operated
--name string            cluster name
--security-mode string   是否以安全模式启动
选项: on/off
--time-out duration      启动超时时间 (default 2m0s)
```

使用示例：

```shell
ptk start -D 数据库目录 # 如果不指定数据库目录，默认根据topo.yaml 中的数据库目录
ptk start -H ip       # 如果指定ip（集群中节点IP） 则启动的是集群中单节点
ptk start -H ip -D 数据库目录  # 启动指定节点指定的数据库目录
ptk start --name      # 启动指定名字的集群
```

- 停止 (stop)

```shell
Usage:
ptk stop [flags]

Flags:
-D, --data-dir string     path of data dir
-h, --help                help for stop
-H, --host string         ip of host to be operated
--mode string         关闭模式
f: fast, i: immediate
--name string         cluster name
--time-out duration   停止超时时间 (default 2m0s)
```

使用示例：

```shell
ptk stop -D 数据库目录 # 如果不指定数据库目录，默认根据topo.yaml 中的数据库目录
ptk stop -H ip       # 如果指定ip（集群中节点IP） 则停止的是集群中单节点
ptk stop -H ip -D 数据库目录  # 停止指定节点指定的数据库目录
ptk stop --name      # 停止指定名字的集群
```

- 重启 (restart)
```shell
Usage:
ptk restart [flags]

Flags:
--azname string          name of available zone
-D, --dataDir string         path of data dir
-h, --help                   help for restart
-H, --host string            ip of host to be operated
--mode string            关闭模式
f: fast, i: immediate
--name string            cluster name
--security-mode string   是否以安全模式启动
选项: on/off
--time-out duration      启动超时时间 (default 2m0s)
```

使用示例：

```shell
ptk restart -D 数据库目录         # 如果不指定数据库目录，默认根据topo.yaml 中的数据库目录
ptk restart -H ip               # 如果指定ip（集群中节点IP） 则重启的是集群中单节点
ptk restart -H ip -D 数据库目录   # 重启指定节点指定的数据库目录
ptk restart --name              # 重启指定名字的集群
```

- 查询集群运行时信息 (query)

```shell
Usage:
ptk query [flags]

Flags:
-h, --help                help for query
--name string         cluster name
--time-out duration   查询超时时间 (default 2m0s)
```

使用示例：

```shell
ptk query --name 集群名字
```

- 状态查询 (status)

```shell
Usage:
ptk status [flags]

Flags:
--all           显示所有数据库实例状态信息
--detail        显示详细状态信息
-h, --help          help for status
-H, --host string   ip of host to be operated
--name string   cluster name
```

使用示例：

```shell
ptk status --all      # 显示所有数据库实例状态信息
ptk status --detail   # 显示详细状态信息
ptk status -H ip      # 显示指定实例状态信息
```

#### 6.PTK自升级 (--self-upgrade)

使用示例：

```shell
ptk --self-upgrade
```

#### 7.本地已安装多集群列表查看 (ls)

使用示例：

```shell
ptk ls
```
