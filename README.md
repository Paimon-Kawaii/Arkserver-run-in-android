# 使用Termux在安卓部署手游服务器

## 1 环境
可以安装termux终端的移动设备(建议安卓8以上)

## 2 教程

### 2.1 准备工作
下载Release提供的所有文件

安装Release提供的termux终端，并将服务端文件(arkserver.zip)移动到手机**sdcard0**目录(不会请自行百度)，随后解压。

保证服务端文件夹名为`arkserver`，文件夹目录结构应保证为如下所示
```
arkserver
├─data
│  └─...
├─id.txt
├─config.json
└─server.jar
```
随后复制下文的**一键部署脚本**，在**sdcard0**目录下创建setup.sh，将脚本粘贴并保存(不会请自行百度)
#### 2.1.1 一键部署环境脚本
```bash
#更换清华源并更新
echo 更换清华源并更新
echo 一直回车即可
sed -i 's@^\(deb.*stable main\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/apt/termux-main stable main@' $PREFIX/etc/apt/sources.list
pkg update && pkg upgrade -y
#安装Java17环境
echo 安装Java17环境
pkg update && pkg install openjdk-17 -y
#安装MariaDB数据库
echo 安装MariaDB数据库
pkg update && pkg install mariadb -y
#开启数据库服务
echo 开启数据库服务
nohup mysqld &
sleep 5
#数据库更新
echo 数据库更新
mysql_upgrade
#创建Sql文件并执行
echo 创建Sql文件并执行
echo 'create database if not exists arknights' >> ark.sql
mysql -uroot < ark.sql
sleep 2
kill -9 `pgrep mysql`
#删除Sql文件
echo 删除Sql文件
rm ark.sql
#创建启动脚本
echo 创建启动脚本
cd ~
rm arkserver.sh
ln -s ~/storage/shared/arkserver arkserver
echo 'cd ~/arkserver' >> arkserver.sh
echo 'kill -9 `pgrep mysql`' >> arkserver.sh
echo 'nohup mysqld &' >> arkserver.sh
echo 'java -jar server.jar' >> arkserver.sh
echo 'kill -9 `pgrep mysql`' >> arkserver.sh
echo 部署完毕
```

### 2.2 部署服务端环境
打开termux终端，等待加载完成后，输入指令：`termux-setup-storage`并回车，并在弹出的弹窗中选择允许。

随后输入：`ln -s ~/storage/shared sdcard`并回车。

然后输入：`sh ~/sdcard/setup.sh`并回车。

**在部署过程中，可能会出现需要输入的情况，在这种情况下，*不需要输入多余内容*，只需要*按下回车键即可***

### 2.3 启动服务端
当提示部署完毕时，输入：`sh ~/arkserver.sh`并回车。以后启动也是这个指令。

此时若无问题，服务端应正常启动，若**出现红色字样的ERROR提示**，请清除termux终端的数据并从[部署服务端环境](#22-部署服务端环境)开始重新执行指令。

***请勿重复执行setup.sh***

## 3 启动客户端
打开代理软件，并在服务器地址栏输入：`localhost:38660`，随后点击启动客户端即可

本人不会提供客户端与代理软件，请自行百度

## 4 免责声明
本项目仅供学习交流使用，禁止用于非法目的。使用者需对其使用行为负责，开发者不对使用者造成的任何后果承担责任。请在下载后24小时内删除本项目。若因使用本项目造成的任何法律纠纷，由使用者自行承担全部责任，开发者不承担任何责任。使用本项目即表示接受本免责声明。
