<h3 align = "center">spotlight无法搜有的结局办法</h3>
**今天突然发现spotlight搜东西没反应了，网上查找解决方案都说是要重建索引，于是按下面的方法重建索引：**
>将Mac下的所有磁盘全部重建索引，包括Mac本身的硬盘，磁盘镜像，还有外接硬盘等：   
**sudo mdutil -E /**

**结果出现如下错误：**

>~$ **sudo mdutil -E /**   
2015-09-15 11:24:14.110 mdutil[459:83404] Metadata.framework [Error]: mdsCopyStorePaths failed: (268435459) (ipc/send) invalid destination port   
==Spotlight server is disabled.==


**重点是最后一句：** ==*splotlight 服务不可用*==

**于是查找到开启splot服务的方法:**

 
>**打开spotlight server的命令:**   
sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.metadata.mds.plist    
**关闭spotlight server的命令：**   
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.metadata.mds.plist  

==***重新开启splot服务后问题即解决。***==