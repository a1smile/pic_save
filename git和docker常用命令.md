# git常用指令

```
初始化和配置

git init: 初始化一个新的Git仓库。

git config --global user.name "xxx": 配置全局用户名。

git config --global user.email "xxx@xxx.com": 配置全局用户邮箱。

基本操作

git add <file>: 将文件添加到暂存区。

git commit -m 'message': 提交暂存区的内容到仓库。

git status: 查看仓g库当前状态，显示有变更的文件。

git rm <file>: 从仓库中删除文件。

git log: 查看提交历史。

git reset --<soft><hard>[mixed]: 回退

git ls-files  : 查看暂存区内容

rm <file>   ： 删除工作区文件

git rm <file> 删除暂存区和工作区 文件

git rm -- cache <file> :删除暂存区

分支管理

git branch: 列出所有本地分支。

git checkout <branch>: 切换到指定分支。

git merge <branch>: 合并指定分支到当前分支。

git branch -d <branch>: 删除本地分支。

远程仓库

git clone <url>: 克隆远程仓库。

git push <remote> <branch>: 将本地分支推送到远程仓库。

git pull <remote> <branch>: 从远程仓库拉取最新内容并合并到本地分支。

git remote add <name> <url>: 添加新的远程仓库。
```

# docker

## 常用命令

```
命令	            说明	             文档地址
docker pull	    拉取镜像	  	 docker pull
docker push	    推送镜像到     	 DockerRegistry	docker push
docker images	查看本地镜像 		docker images
docker rmi	    删除本地镜像		docker rmi
docker run	    创建并运行容器（不能重复创建）	docker run
docker stop	    停止指定容器		docker stop
docker start	启动指定容器		docker start
docker restart	重新启动容器		docker restart
docker rm	    删除指定容器		docs.docker.com
docker ps	    查看容器		  docker ps
docker logs	    查看容器运行日志   docker logs
docker exec	    进入容器		  docker exec
docker save	    保存镜像到本地压缩文件	docker save
docker load	    加载本地压缩文件到镜像	docker load
docker inspect	查看容器详细信息	docker inspect
```



## 容器

```
命令	                     说明	        文档地址
docker network create	创建一个网络	docker network create
docker network ls	    查看所有网络	docs.docker.com
docker network rm	    删除指定网络	docs.docker.com
docker network prune	清除未使用的网络	docs.docker.com
docker network connect	使指定容器连接加入某网络	docs.docker.com
docker network disconnect	使指定容器连接离开某网络	docker network disconnect
docker network inspect	查看网络详细信息	docker network inspect
```

## DockerCompose是什么，如何编写一个Dockerfile

```
指令	    说明	     示例
FROM	指定基础镜像	FROM centos:6
ENV		设置环境变量，可在后面指令使用	ENV key value
COPY	拷贝本地文件到镜像的指定目录	COPY ./xx.jar /tmp/app.jar
RUN		执行Linux的shell命令，一般是安装过程的命令	RUN yum install gcc
EXPOSE	指定容器运行时监听的端口，是给镜像使用者看的	EXPOSE 8080
ENTRYPOINT	镜像中应用的启动命令，容器运行时调用	ENTRYPOINT java -jar xx.jar
```

