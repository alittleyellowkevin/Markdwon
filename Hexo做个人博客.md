# Hexo做个人博客

1、使用npm安装hexo

```
sudo npm install -g hexo-cli
```

2、使用利用hexo初始化一个项目

```
hexo init
```

3、安装hexo插件

```
npm install --save hexo-deployer-git
```

4、在github中创建一个repository

```apl
# 命名要符合规则alittleyellowkevin.github.io
```



4、设置_config.yml

```yaml
# 主要是设置git远程厂库
deploy:
  type: git
  repo: git@github.com:alittleyellowkevin/blog.git
```

5、部署到远端

```
hexo d
```

6、访问git网端

```
alittleyellowkevin.github.io
```

7、更换主题

```bash
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia path/to/themes/yilia
```

