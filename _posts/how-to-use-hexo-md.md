title: 如何用hexo搭建博客
date: 2016-04-07 17:48:47
tags: tool
---
用hexo搭建博客需要安装下面两个工具
- git
- node.js
步骤
1. 安装node.js
- 下载node.js软件
```
cd /home/wangkongming/software
git clone git@github.com:nodejs/node.git

```
- 编译安装
```
cd node
./configure
make
sudo make install (必须用sudo)

```
- 查看node.js的版本
```
nodejs -v
v0.10.25
```
2. 安装hexo
```
sudo npm install -g hexo-cli
```
3. 安装成功后，初始化hexo
```
hexo init blog_github
cd blog_github
npm install
```
安装成功后:
```
dwxr-xr-x 189 wangkongming wangkongming 4096 12月  7 18:56 node_modules
-rw-r--r--   1 wangkongming wangkongming 1477 12月  7 18:53 _config.yml
-rw-r--r--   1 wangkongming wangkongming  442 12月  7 18:53 package.json
drwxr-xr-x   2 wangkongming wangkongming 4096 12月  7 18:53 scaffolds
drwxr-xr-x   3 wangkongming wangkongming 4096 12月  7 18:53 source
drwxr-xr-x   3 wangkongming wangkongming 4096 12月  7 18:53 themes
```
5. 用hexo生成文章
新建文章 `hexo new "My first article"`,简写`hexo n "My first article"`
生成文章 `hexo generate`,简写`hexo g`
部署到github `hexo deploy`,简写`hexo d`
```
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```
6. 如何推送博客到github上
修改_config.yml文件
把
```
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type:
```
修改成
```
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: github
  repository: git@github.com:KoMiles/komiles.github.com.git
  branch: master
```

#### 这样修改可能会出现
在hexo deploy 时，出现``deployer not found:github``
原因是:
在hexo更新到3.0之后，deploy 的 type 需要改成 git。如果没有安装Git发布工具，使用 npm 命令安装：
``npm install hexo-deployer-git --save ``。
最后清除工程(``hexo clean``)，重新生成(``hexo generate``)、发布文件(``hexo deploy``)，检查是否发布成功。
最后改成这样：
```
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
    type: git
    repository: git@github.com:KoMiles/komiles.github.com.git
    branch: master 
```
执行：``npm install hexo-deployer-git --save``

#### 参考资料
- [Hexo官方文档](https://hexo.io/zh-cn/docs/)
- [Hexo搭建Github静态博客](http://www.cnblogs.com/zhcncn/p/4097881.html)
- [使用Github搭建静态博客（Hexo）](https://segmentfault.com/a/1190000000458953)
