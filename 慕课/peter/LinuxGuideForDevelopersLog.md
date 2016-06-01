<!-- MarkdownTOC -->

- [Linux Guide for Developers Log](#linux-guide-for-developers-log)
	- [解压缩](#解压缩)
	- [进程](#进程)
	- [终端直接启动图形化工具](#终端直接启动图形化工具)
	- [查找](#查找)
	- [网络操作](#网络操作)
	- [软件安装](#软件安装)
	- [脚本编程](#脚本编程)

<!-- /MarkdownTOC -->
## Linux Guide for Developers Log
### 解压缩



```sh
unzip xxx.zip
zip -r xxx.zip ./
tar zxvf xxx.tar.gz
tar zcvf xxx.tar.gz ./
tar jxvf xxx.tar.bz2
tar jcfv xxx.tar.bz2 ./
```

### 进程

```sh
ps -aux | less
ps -aux | grep vim
kill 进程号
```

### 终端直接启动图形化工具

```sh
ex: firefox 
	firefox &

	bg
	fg
```

### 查找

1. locate

```sh
locate vimrc
locate --regexp xxx
updatedb
```

2. find

```sh
find .
find . | grep .txt
find . -type f
find . -type d
find . -type f -exec ls -l '{}' ';'
find . -type f -exec grep hello '{}' ';'
find . -type f -exec grep hello '{}' ';' -print
find . -type f -exec grep -n hello '{}' ';' -print
find . -type f -exec grep -ni hello '{}' ';' -print
```

### 网络操作

1. ssh
	
```sh
ssh-keygen
ssh-copy-id xxx@host
rsync -r mydir host: //将本地文件同步到服务器
rsync -r host:mydir . //将远程文件同步到本地
rsync -av mydir/ host:mydir/  //将本地新增文件同步到远程服务器
rsync -av --delete mydir/ host:mydir/  //将本地新增或修改的文件同步到远程服务器
rsync -av --delete mydir/ host:mydir/  --dry-run  //查看信息，并不真正同步
```

2. 一段与服务器同步数据脚本
	
```sh
sync_dryrun(){
	rsync -av --delete mydir/ host:mydir/  --dry-run
}

sync_server(){
	rsync -av --delete mydir/ host:mydir/
}

sync_dryrun

echo -n "Want to sync? (Y/n): "
read AAA
if [ "${AAA: -y}" = "y"];then
	sync_server
else
	echo Nothing done, bye.
fi
```

### 软件安装

- 手动安装
	1. 下载zip 解压  
	2. 设置path 或者 ln -s xxx  bin/xxx
- 自己编译
	1. 进入源码目录执行 ./configure
	2. 执行make
	3. sudo make install
- 包管理器
	1. 下载deb包
	2. 安装deb包 `sudo dpkg -i google-chrome.deb`
	3. `dpkg -l` 列出系统中多有的deb包  ex: dpkg -l | grep chrome
	4. `dpkg -L` 列出系统中已经安装的软件
	5. `dpkg -S 软件名称` 查看该软件来自哪个包
	  
- `apt-get` 从软件仓库安装(都是从源码打包好的deb包，放在操作系统的软件仓库如ubuntu的软件仓库)
	1. 安装软件 `sudo apt-get install 软件名称` 
	2. 删除软件 `sudo apt-get remove 软件名称`
	3. 删除软件连配置文件一起删除 `sudo apt-get purge 软件名称`
	4. 搜索可安装软件 `apt-cache search ncurse | less`
	5. `sudo apt-get install libncurses` 有tab补齐功能，按两下可列出所有被选项
	

### 脚本编程

1. 指定解析器 `#!/usr/bin/env bash` 放在脚本文件的第一行，不仅仅只是bash还可指定python，rube 等。
2. 语句即命令 if的执行原理是如果后面的命令执行成功就为真
3. 位置参数 如 `命令 参数一 参数二` 在脚本中 `$0`代表命令  `$1`代表参数一 `$2`代表参数二
4. 不在当前shell执行 
	* 脚本中执行的命令是在单独的一个shell中执行的
	* 如果一定要在当前shell中执行需要在加上`source` 如 `source xxx.sh`
5. 循环控制 
	ex:给当前目录下的所有文件加上.txt后缀
	```sh
	#!/usr/bin/env bash
	cd $1
	echo I am in `pwd`
	for file in `ls`
	do 
		mv $file $file.txt
	done
	```

6. 远程执行
	`ssh -t xxx@host 'touch a.txt'` 将会在远程服务器上执行 `touch a.txt`
7. 确认执行 
8. 建议用其他语言写脚本，如 python、ruby等。






















