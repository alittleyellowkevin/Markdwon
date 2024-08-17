# Git

## Git 概述

![截屏2024-08-17 14.10.30](/Users/huangkaiwen/Library/Application Support/typora-user-images/截屏2024-08-17 14.10.30.png)

## Git 命令

- 设置用户签名

```cmd
git config --global user.name 用户名
git condig --global user.email 邮箱
```

- 初始化本地库

```
git init
```

- 查看本地库状态

```
git status
```

- 添加到暂存区

```
git add 文件名/.
```

- 提交到本地库

```
git commit -m "日志信息" 文件名
```

- 查看历史记录

```
git reflog
```

- 版本穿梭

```
git reselt --hard 版本号
```

## Git 分支

- 分支概念

在版本控制过程中，同时推进多个任务，为每个任务，我们就可以创建每个任务单独的分支，使用分支意味着程序员可以把自己的工作从主线的开发分离开来，开发自己的分支时，不会影响主线分支的运行。

- 使用分支的优点

同时并行推进多个功能开发，提高开发效率

- 分支的操作

1. 创建分支

```
git branch 分支名
```

2. 查看分支

```
git branch -v
```

3. 切换分支

```
git checkout 分支名
```

4. 将指定分支合并到当前分支上

```
git merge 分支名
```

5. 删除分支

```
git branch -d 分支名
```





hello

### 1.注册github并创建repository

![截屏2024-04-30 10.40.33](/Users/huangkaiwen/Library/Application Support/typora-user-images/截屏2024-04-30 10.40.33.png)

### 2.下载git并配置

#### 2.1 配置用户名称以及用户Email

```
git config —global user.name “kevin”
git condig —global user.email “hello@kevin.cn”
```

#### 2.2 生成用户密匙

```
ssh-keygen-t rsa-C "hello@kevin.cn"
```

#### 2.3 将生成密匙配置到github中测试连接

1.通过命令得到密匙

<img src="/Users/huangkaiwen/Library/Application Support/typora-user-images/截屏2024-04-30 10.47.09.png" alt="截屏2024-04-30 10.47.09" style="zoom:67%;" />

2.随后复制密匙



<img src="/Users/huangkaiwen/Library/Application Support/typora-user-images/截屏2024-04-30 10.51.51.png" alt="截屏2024-04-30 10.51.51" style="zoom: 67%;" />



3.管理公钥，选择setting中的SSH和GPGkeys 

<img src="/Users/huangkaiwen/Library/Application Support/typora-user-images/截屏2024-04-30 11.12.37.png" alt="截屏2024-04-30 11.12.37" style="zoom:30%;" />

4.将钥匙复制title可以写成自己然后测试

<img src="/Users/huangkaiwen/Library/Application Support/typora-user-images/截屏2024-04-30 11.13.38.png" alt="截屏2024-04-30 11.13.38" style="zoom:50%;" />

```
测试是否连接成功：ssh -T git@github.com
```

### 3.随后在idea中进行配置

#### 3.1 首先将本项目作为本地厂库

![截屏2024-04-30 11.19.46](/Users/huangkaiwen/Library/Application Support/typora-user-images/截屏2024-04-30 11.20.11.png)

#### 3.2 设置远端厂库  

![截屏2024-04-30 11.21.56](/Users/huangkaiwen/Library/Application Support/typora-user-images/截屏2024-04-30 11.21.56.png)