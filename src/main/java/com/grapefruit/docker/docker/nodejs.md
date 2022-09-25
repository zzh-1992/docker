# nodejs

## 官网下载地址

https://nodejs.org/en/download/

## 解压

```shell
tar xf node-v16.17.0-linux-x64.tar.xz
```

## 创建软连接

```shell
ln -s /usr/local/nodejs/bin/npm   /usr/local/bin/
ln -s /usr/local/nodejs/bin/node   /usr/local/bin/
```

## 查看版本

```shell
[root@ecs-132644 nodejs]# node -v
v16.17.0
[root@ecs-132644 nodejs]# npm -v
8.15.0
```

## install cnpm

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

## 创建软连接

```shell
ln -s /usr/local/nodejs/bin/cnpm   /usr/local/bin/
```

