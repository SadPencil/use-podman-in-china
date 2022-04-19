# use-podman-in-china
在中国大陆使用 Podman 的注意事项

## 更换镜像源

在 `/etc/containers/registries.conf` 文件中指定镜像源。本文件使用 `ustc-edu-cn.mirror.aliyuncs.com` 作为例子。

```ini
unqualified-search-registries = ["docker.io"]

[[registry]]
prefix = "docker.io"
location = "ustc-edu-cn.mirror.aliyuncs.com"
```

注意，针对 docker.io 的镜像源，不能使用以下写法：

```ini
unqualified-search-registries = ["ustc-edu-cn.mirror.aliyuncs.com"]
```

原因是 podman 在代码里做了特殊判断，只针对 `docker.io` 这个域名做如下判断：若镜像名是知名镜像（例如`podman pull nginx`），则自动在镜像名前面添加`library/`（例如 `podman pull library/nginx`）。判断是写死的，这个策略针对其他任何域名都不生效。

## 更换infra镜像

Podman 在创建 pod 时，默认会从 `k8s.gcr.io` 拉取 `pause` 的镜像。此仓库很可能在中国大陆无法访问，需要做替换。

检查 `/etc/containers/containers.conf` 文件是否存在，若不存在，则从 `/usr/share/containers/containers.conf` 复制到 `/etc/containers/containers.conf`。

在 `/etc/containers/containers.conf` 文件中找到并修改以下几句话：

```ini
[engine]
infra_command = "/pause"
infra_image = "ustc-edu-cn.mirror.aliyuncs.com/aiotceo/pause:3.1"
```

Podman v4.0.0 起默认会在本地构建 `pause` 镜像。
