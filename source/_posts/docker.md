---
title: docker
date: 2024-05-20 22:39:44
tags:
---

* docker build -t {name} .
* docker run -di --name={dockername} -p 8099:80 {imagename} 
* docker exec -it {dockerid} /bin/bash
* docker ps | grep {dockername}

```dockerfile
FROM nginx
RUN mkdir /usr/share/nginx/dist \
    && rm -rf /etc/nginx/nginx.conf \
    && /bin/cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo 'Asia/Shanghai' >/etc/timezone
COPY ./nginx.conf /etc/nginx/nginx.conf
COPY ./dist/ /usr/share/nginx/dist
EXPOSE 8087
```
# 打tag
docker tag myimage:1.0(或者imageid) myharbor.example.com/myproject/myimage:latest
# 解压已归档image
docker load -i myimage.tar
# 推送私有harbor
docker login myharbor.example.com
docker push myharbor.example.com/myproject/myimage:latest
# 查找带有my的image
docker images | awk '$1~/my/
