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

使用`env`指令来定义环境变量。环境变量有两种形式：`$variable_name` 和 ``${variable_name} `。推荐使用后者

好处：

1. 可以复合值，如`${foo}_bar`，前者就无法做到

2. 支持部分bash语法

   

支持环境变量的指令：

`ADD`，`COPY`，`ENV`，`EXPOSE`，`LABEL`，`USER`，`WORKDIR`，`VOLUME`，`STOPSIGNAL`，`ONBUILD`



### 指令

#### FROM

```dockerfile
# 指明构建的新镜像是来自于哪个基础镜像

FROM [--platform=<platform>] <image> [AS <name>]
FROM [--platform=<platform>] <image>:<tag> [AS <name>]
FROM [--platform=<platform>] <image>@<digest> [AS <name>]

```



