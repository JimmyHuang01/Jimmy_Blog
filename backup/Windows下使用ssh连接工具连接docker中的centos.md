### **- 实现路线：安装镜像 -->  开始ssh服务 -->  使用 ssh工具连接** 

**1. 下载windows的docker
2. 打开命令行，拉取centos镜像(默认拉取最新版本)**   `docker pull centos`
**3. 使用特权模式+指定映射端口创建并启动容器**  `docker run -tid --name=centos --privileged=true -p 22:22 centos /usr/sbin/init`   **-p 后面是要映射的端口，--privileged=true 使用特权模式**
**4. 进入容器**   `docker exec -it centos /bin/bash`
**5. 安装网络工具（此过程可能出现错误，见本文附的解决办法1）** `yum install net-tools`
**6. 启动ssh服务（此过程可能出现错误，见本文附的解决办法2）**    `systemctl start sshd.service`
**7. 安装密码设置工具**  `yum install passwd`
**8. 修改root密码**  `passwd root`
**9. 使用ssh工具连接，新建一个session，**主机写localhost，端口号写刚刚指定映射的端口号****

![Image](https://github.com/user-attachments/assets/c97ee2d2-96e0-4bf3-b3ea-98562ec0caf6)

![Image](https://github.com/user-attachments/assets/c7dd1afe-1ed2-4790-a891-eca21cb11e73)

_使用刚刚步骤8设置的密码即可登录进来从docker部署的centos，就不再需要虚拟机了

- [**解决办法1** ] 1、上面的操作步骤5安装网络工具，出现以下错误： `[root@d34a7fa0d2f3 /]# yum install net-tools
Failed to set locale, defaulting to C.UTF-8
CentOS Linux 8 - AppStream                                                              106  B/s |  38  B     00:00
Error: Failed to download metadata for repo 'appstream': Cannot prepare internal mirrorlist: No URLs in mirrorlist`
 解决办法：
    a.进入到 yum 的 repos 目录：  `cd /etc/yum.repos.d/`
    b.修改 centos 文件内容：  `sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*`
`sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*`
    c.生成缓存更新（第一次更新，速度稍微有点慢，耐心等待两分钟左右   `yum makecache`
    d.运行 yum update 并重新安装 net-tools  `yum update -y
yum -y install net-tools`
     此时安装net-tools即可成功。

- [**解决办法2** ]  2、启动ssh服务时报错如下  `[root@d34a7fa0d2f3 yum.repos.d]# systemctl start sshd.service
Failed to start sshd.service: Unit sshd.service not found.`
  
  解决办法：  `[root@d34a7fa0d2f3 yum.repos.d]# yum install openssh-server`
  安装成功后再次输入systemctl start sshd.service命令即可成功。
 
