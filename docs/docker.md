
# docker

## 基础知识

### 概述

### 安装

### 维护

### 使用

### 镜像

### 日志

### 变量

## 备份



![docker基本命令关系图](https://img2022.cnblogs.com/blog/451016/202204/451016-20220411150846656-893834584.png)

## 导出&导入

容器迁移(软件迁移)

### 导出

```shell
# export&import 保留镜像的层历史
# save&load 只保留一层操作记录
docker export -o mysql8.tar.gz purple-single-mysql8
docker import mysql8.tar.gz mysql8:v1

docker save -o image_name_version.tar image_name:version
docker load --input image_name_version.tar

```

## 上传

```shell
docker stop
docker commit -a "dev_mysql_8" -m "dev_mysql_8" 1740e0c4a1d9 swr.cn-east-3.myhuaweicloud.com/uv/mysql8:v1
# docker build -t swr.cn-east-3.myhuaweicloud.com/uv/mysql8:v1
docker push swr.cn-east-3.myhuaweicloud.com/uv/mysql8:v1
```
