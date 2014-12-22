#Docker 学习笔记

##基础知识

Docker 项目的目标是**实现轻量级的操作系统虚拟化解决方案**。 Docker 的基础是 Linux 容器（`LXC`）等技术。

在 LXC 的基础上 Docker 进行了进一步的封装，让用户不需要去关心容器的管理，使得操作更为简便。用户操作 Docker 的容器就像操作一个快速轻量级的虚拟机一样简单。

下面的图片比较了 Docker 和传统虚拟化方式的不同之处，可见容器是在操作系统层面上实现虚拟化，直接复用本地主机的操作系统，而传统方式则是在硬件层面实现。
![VM](http://yeasy.gitbooks.io/docker_practice/content/_images/virtualization.png)
![DOCKER](http://yeasy.gitbooks.io/docker_practice/content/_images/docker.png)