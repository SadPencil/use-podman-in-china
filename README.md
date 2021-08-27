# use-podman-in-china
在中国大陆使用 Podman 的注意事项

## 更换镜像源

在 `/etc/containers/registries.conf` 文件中指定镜像源。本文件使用 `ustc-edu-cn.mirror.aliyuncs.com` 作为例子。

```ini
unqualified-search-registries = ["ustc-edu-cn.mirror.aliyuncs.com"]
```

## 更换infra镜像

Podman 在创建 pod 时，默认会从 `k8s.gcr.io` 拉取 `pause` 的镜像。此仓库很可能在中国大陆无法访问，需要做替换。

检查 `/etc/containers/containers.conf` 文件是否存在，若不存在，则从 `/usr/share/containers/containers.conf` 复制到 `/etc/containers/containers.conf`。

在 `/etc/containers/containers.conf` 文件中找到并修改以下几句话：

```ini
[engine]
infra_command = "/pause"
infra_image = "ustc-edu-cn.mirror.aliyuncs.com/aiotceo/pause:3.1"
```

## 知名镜像使用注意事项

对于所有不需要写用户名的知名镜像，例如 `nginx`, 不能再直接写 
```sh
podman pull nginx
```
而要改为
```sh
podman pull library/nginx
```
即，在知名镜像名称前面加 `library/`。
这也会影响对应的 Dockerfile 文件。
