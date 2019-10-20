---
title: jekyll模板使用错误记录
keywords: jekyll模板使用
summary: "jekyll模板开发过程的错误点记录"
sidebar: product1_sidebar
permalink: jekyll_developing.html
folder: product1
---

## [jekyll docker下载](https://github.com/envygeeks/jekyll-docker)
* 使用jekyll-jekyll镜像jekyll即可完成build,serve等功能, build镜像供CI等使用

## 避免版本兼容问题,先进行bundle update.
* 使用docker操作前检查Gemfile.lock权限是否对其他用户可见. 不可则chmod a+w Gemfile.lock
```bash
docker run --rm -v "$PWD:/srv/jekyll" -v "$PWD/vendor/bundle:/usr/local/bundle" -it jekyll/jekyll bundle update
```

## 出现NO Github API和权限问题
![](images/jekyll_developing_1.png)
* 使用github-pages插件时记得配置环境变量JEKYLL_GITHUB_TOKEN。
* 权限问题： 容器内jekyll角色无法创建_site文件夹: 手动创建文件夹mkdir _site; chmod a+x _site;
* 最后命令使用image_id避免从远端下载镜像,bundle目录映射是为了缓存. 
```bash
docker run --rm -v "$PWD:/srv/jekyll" -v "$PWD/vendor/bundle:/usr/local/bundle" -e JEKYLL_GITHUB_TOKEN=`your_github_token` -it `image_id` __jekyll build__  
```

## 部署daemon实现jekyll serve并自动化更新
* 最后部署一个监听, 一劳永逸. -d开启daemon, 开放4000,35729端口, 加上动态刷新配置livereload.
```bash
docker run -d --rm -p 4000:4000 -p 35729:35729 -v "$PWD:/srv/jekyll" -v "$PWD/vendor/bundle:/usr/local/bundle" -e JEKYLL_GITHUB_TOKEN=`github_token` -it `image_id` jekyll serve --livereload 
```
{% include links.html %}
