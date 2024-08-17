# Git的使用 

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