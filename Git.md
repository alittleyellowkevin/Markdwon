# Git

## Git 概述

![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/66a80e15-5b75-4d39-a179-9472d5eaee99.png)

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

## Github操作

### 创建远程厂库

![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/e89fd3f4-5a46-42f0-b132-90c477faea70.png) alt="截屏2024-04-30 10.40.33" style="zoom:50%;" />

### 远程仓库操作

1. 查看当前所有远程仓库别名

```
git remote -v
```

2. 给远程仓库起别名

```
git remote add 别名 远程地址
```

3. 推送本地分支上的内容到远程仓库

```
git push 别名 分支
```

4. 将远程仓库的内容克隆到本地

```
git clone 远程地址
```

5. 将远程仓库以及分支最新内容拉下来后跟当前本地分支直接合并

```
git pull 远程库地址别名 远程分支名
```

### 团队内协作

![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/61bf51f7-5882-45f0-b03b-60ff606adb10.png)

### **跨团队协作**

1. 首先将他人的远端仓库Fork到自己的远程仓库![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/62692a5e-62f1-4004-99bb-c48834196adf.png)
2. 当修改完远端仓库，可以通过pull request请求与他人远端仓库合并

![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/898181d7-20c4-4897-a8b2-bcdc2b51daf0.png)

### SSH免密登录

1. 生成用户密钥

```cmd
ssh-keygen -t rsa -C "hello@kevin.cn" # 敲三次回车
```

2. 复制公钥id_rsa.pub

```
cat id_rsa.pub
```

3. 在github中设置公钥

![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/9816b7c0-79d8-45b4-8cf1-f717bde57531.png) alt="截屏2024-08-17 15.58.25" style="zoom: 50%;" />

4. 命令窗口中测试是否添加成功

```
ssh -T git@github.com
```

## IDEA集成Git

1. 初始化项目

![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/77c59298-5514-4cab-902d-42bb1caa6cd3.png) alt="截屏2024-08-17 16.07.43" style="zoom: 150%;" />

2. 恢复版本

![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/5ae4f335-73af-4457-a72f-19aac68b2b21.png)

3. 创建分支

![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/83889979-03e1-4207-8387-71d6610ec0cf.png) alt="截屏2024-08-17 16.15.46" style="zoom: 50%;" />

4. 合并分支

![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/32a72354-8716-441a-b28e-077189164afb.png) alt="截屏2024-08-17 16.18.59" style="zoom: 67%;" />

5. 设置github账号，先从github的setttings\developer settings中创建token，随后写入idea

   ![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/a53db77e-96f3-4e61-b3a5-9ce57b690855.png)

![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/5ea13437-447e-4d7a-8367-78bd67b1c27a.png)
