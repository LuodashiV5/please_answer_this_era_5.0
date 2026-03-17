

# Docker镜像源加速器

镜像源：

```txt
详见：
https://github.com/dongyubin/DockerHub
https://www.wangdu.site/course/2109.html
```

![Image|771](https://mmbiz.qpic.cn/sz_mmbiz_png/AxnOVHbribZYFjbRyCCQLlK0cLYQedQcEKpvx3QqYeNOCXljxTDHZ46C2EdSic2Ylp0vfOeD2GPhs01nibTx1KCdaHsvibnhI87z0a1S2eicEnWg/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

```
"腾讯云（只支持内网访问，不支持外网域名访问加速。轻量应用服务器 安装 Docker 并配置镜像加速源）"
# 详见：
# https://cloud.tencent.com/document/product/457/9113
# https://cloud.tencent.com/document/product/1207/45596

# 镜像加速器地址：
https://mirror.ccs.tencentyun.com
```

```
"阿里云（需登录，系统分配）"
# 详见：https://cr.console.aliyun.com/

# 镜像加速器地址：
https://<your_code>.mirror.aliyuncs.com
```

```
"DaoCloud 镜像站"
# 详见：https://github.com/DaoCloud/public-image-mirror

# 镜像加速器地址：
https://docker.m.daocloud.io
```

```
"毫秒镜像"
# 详见：https://1ms.run/

# 镜像加速器地址：
https://docker.1ms.run
```

```
# 镜像加速器地址：（限制只能中国地区）
https://docker.1panel.live
```

```
"Docker离线镜像下载"
# 详见：https://proxy.vvvv.ee/images.html

# 镜像加速器地址：
https://proxy.vvvv.ee
```

```
"容器镜像管理中心 - Docker & GitHub"
# 详见：https://registry.cyou/

# 镜像加速器地址：
https://registry.cyou
```

```
"Docker 离线镜像下载器"
# 详见：https://registry.lfree.org/

# 镜像加速器地址：
https://registry.lfree.org
```

配置镜像源：

```
# 创建目录：
mkdir -p /etc/docker

# daemon.json配置文件：
tee /etc/docker/daemon.json <<EOF
{
  "registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://docker.1ms.run"
  ]
}
EOF

# 重新加载配置：
systemctl daemon-reload

# 重启docker：
systemctl restart docker

# 查看docker配置：
docker info

```

其他：

```
# 国内的 Docker Hub 镜像加速器
https://gist.github.com/y0ngb1n/7e8f16af3242c7815e7ca2f0833d3ea6?permalink_comment_id=5068535

# Dockerized 实践
https://github.com/y0ngb1n/dockerized

# 自建镜像仓库代理服务
https://github.com/bboysoulcn/registry-mirror

# 利用 Cloudflare Workers 自建 Docker Hub 镜像
https://github.com/ImSingee/hammal

# CF-Workers-docker.io：Docker仓库镜像代理工具
https://github.com/cmliu/CF-Workers-docker.io

# cloudflare-docker-proxy
#（docker 注册表代理在 cloudflare Worker 上运行）
https://github.com/ciiiii/cloudflare-docker-proxy

# 多平台容器镜像代理服务
https://github.com/kubesre/docker-registry-mirrors

# 自建Docker镜像加速服务
https://github.com/dqzboy/Docker-Proxy

# Docker 和 GitHub 加速代理服务器
https://github.com/sky22333/hubproxy

# Xget
https://github.com/xixu-me/Xget
```

关联

[[Docker Engine 没有那么容易启动]]