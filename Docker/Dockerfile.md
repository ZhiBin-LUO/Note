[Toc]

## Dockerfile 参考



### 说明

#### 格式（Format）

```dockerfile
INSTRUCTION arguments
```

- 指令不区分大小写，通常使用大写与参数做区分
- 必须以`FROM`指令开始，`Parser directives` `Comments` `ARGs` 除外



#### 解析指令（Parser directives ）

- `syntax`
- `escape`



#### 环境变量

使用`env`指令来定义环境变量。环境变量有两种形式：`$variable_name` 和 `${variable_name} `。推荐使用后者

支持环境变量的指令：

`ADD`，`COPY`，`ENV`，`EXPOSE`，`LABEL`，`USER`，`WORKDIR`，`VOLUME`，`STOPSIGNAL`，`ONBUILD`



### 指令

#### FROM

```dockerfile
# 指明构建的新镜像是来自于哪个基础镜像

FROM [--platform=<platform>] <image> [AS <name>]
FROM [--platform=<platform>] <image>:<tag> [AS <name>]
FROM [--platform=<platform>] <image>@<digest> [AS <name>]

- FROM指令必须是Dockerfile的第一个指令（ARG指令可以优先于FROM）
- tag 和 digest 是可选的，默认使用latest

```



#### RUN

```dockerfile
# 构建镜像时运行的Shell命令

RUN <command>														# shell格式
RUN ["executable", "param1", "param2"]	 # exec格式

```



#### CMD

```dockerfile
# 指定容器运行时的默认参数，如果出现多次以最后一次为准

CMD ["executable","param1","param2"]	# exec格式(首选)
CMD ["param1","param2"]								# ENTRYPOINT 的默认参数
CMD command param1 param2							# shell格式

- 与RUN指令的区别：RUN在构建的时候执行，并生成一个新的镜像，CMD在容器运行的时候执行，在构建时不进行任何操作
```



#### LABEL

```dockerfile
# 给构建的镜像打标签

LABEL <key>=<value> <key>=<value> <key>=<value> ...
 
- 如果base image中也有标签，则继承，如果是同名标签，则覆盖
- 查看镜像标签 Labels，可以使用--format筛选:
	docker image inspect --format='' imageName:tag

```



#### EXPOSE

```dockerfile
# 声明容器运行的服务端口

EXPOSE <port> [<port>/<protocol>...] # EXPOSE 80/TCP 默认TCP

- EXPOSE指令实际上并不发布端口，它充当构建映像的人和运行容器的人之间的一种文档类型，打算发布哪些端口
```



#### ENV

```dockerfile
# 在构建的镜像中设置环境变量，在后续的Dockerfile指令中可以直接使用

ENV <key>=<value> ...

- 当容器从生成的镜像运行时，环境变量仍然有效。可以在容器运行时指定环境变量，替换镜像中的已有变量。docker run --env <key>=<value>
- 使用ENV可能会对后续的Dockerfile指令造成影响，如果只需要对一条指令设置环境变量
	1.RUN <key>=<value> <command>
	2.使用ARG指令，不会保留在最终镜像中

```



#### ADD

```dockerfile
# 拷贝文件或目录到镜像中

ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]	# 包含空格的路径需要后一种形式

- <src>可以是文件、目录，也可以是文件URL。可以使用模糊匹配，可以指定多个<src>，必须是在上下文目录和子目录中，无法添加../a.txt这样的文件。如果<src>是个目录，则复制的是目录下的所有内容，但不包括该目录。如果<src>是个可被docker识别的压缩包，docker会以tar -x的方式解压后将内容复制到<desct>
- <dest>可以是绝对路径，也可以是相对WORKDIR目录的相对路径
- 所有文件的UID和GID都是0

```



#### COPY

```dockerfile
# 拷贝文件或目录到镜像中，用法同ADD，只是不支持自动下载和解压

COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]

- 如果<dest>不存在，COPY指令会自动创建所有目录，包括子目录

```



#### ENTRYPOINT

```dockerfile
# 

ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT command param1 param2

TODO 
```



#### VOLUME

```dockerfile
# 创建一个具有指定名称的挂载点，并将其标记为保存来自本机主机或其他容器的外部挂载卷

VOLUME ["/data"]						# JSON array
VOLUME /var/log /var/db			# 具有多个参数的纯字符串

- 容器运行的时候，docker会把镜像中的数据卷的内容复制到容器的数据卷中去

```



#### USER

```dockerfile
# 为接下来的Dockerfile指令指定运行用户

USER <user>[:<group>]
USER <UID>[:<GID>]

- 影响的指令：RUN、CMD、ENTRYPOINT.

```



#### WORKDIR

```dockerfile
# 为接下来的Dockerfile指令指定当前工作目录，可多次使用

WORKDIR /path/to/workdir

- 影响的指令：RUN、CMD、ENTRYPOINT、COPY、ADD.

```



#### ARG

```dockerfile
# 构建镜像时(docker build)，指定的参数

ARG <name>[=<default value>]

- 构建：docker build --build-arg <varname>=<value>
- 范围：构建参数在定义的时候生效而不是在使用的时候
-	内置了一批构建参数，可以不用在Dockerfile中声明：
		HTTP_PROXY、http_proxy
		HTTPS_PROXY、https_proxy
		FTP_PROXY、ftp_proxy
		NO_PROXY、no_proxy
		ALL_PROXY、all_proxy
	
```



#### ONBUILD

```dockerfile
# 向镜像中添加一个触发器，当以该镜像为base image再次构建新的镜像时，会触发执行其中的指令

ONBUILD <INSTRUCTION>

- ONBUILD只会继承给子节点的镜像，不会再继承给孙子节点
- ONBUILD ONBUILD或者ONBUILD FROM或者ONBUILD MAINTAINER是不允许的
```



#### STOPSIGNAL

```dockerfile
# 触发系统信号

STOPSIGNAL signal

```



#### HEALTHCHECK

```dockerfile
# 增加自定义的心跳检测功能，多次使用只有最后一次有效

HEALTHCHECK [OPTIONS] CMD command		# 通过在容器内运行command来检查心跳
HEALTHCHECK NONE										# 取消从base image继承来的心跳检测

OPTIONS:
	--interval=DURATION				# 检测间隔，默认30秒
	--timeout=DURATION				# 命令超时时间，默认30秒
	--start-period=DURATION		# 启动时间，默认0
	--retries=N								# 连续N次失败后标记为不健康，默认3次
	
command:	# docker以<command>的退出状态码来区分容器是否健康
	0：命令返回成功，容器健康
	1：命令返回失败，容器不健康
	2：保留状态码，不要使用
	
- 可以使用docker inspect命令来查看健康状态
```



#### SHELL

```dockerfile
# 更改后续的Dockerfile指令中所使用的shell

SHELL ["executable", "parameters"]

- Linux默认：["bin/sh", "-c"]
- 影响的指令：RUN、CMD、ENTRYPOINT.

```



### 参考链接

[Docker官方文档](https://docs.docker.com/engine/reference/builder/)
[深入Dockerfile（一）: 语法指南](https://github.com/qianlei90/Blog/issues/35)
