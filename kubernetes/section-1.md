# 安装 Kubernetes 前的准备工作

## 禁用 swap

* 暂时关闭swap分区：`swapoff -a`
* 永久关闭swap分区：`sed -ri 's/.*swap.*/#&/' /etc/fstab`

## 更改主机名称

`hostnamectl hostname kube-alpha`

## 禁用 ipv6 (可选)

```shell
cat >> /etc/sysctl.conf << EOF
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
net.ipv6.conf.tun0.disable_ipv6 = 1
EOF
```

## 安装 Docker

### 使用 `get.docker.com` 脚本安装

```shell
sh -c "$(curl -fsSL https://get.docker.com)"
```

### 使用 APT 源安装

#### 卸载旧版本

```shell
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do apt remove $pkg; done
```

#### 安装 APT 源

```shell
# Add Docker's official GPG key:
apt update
apt install -y ca-certificates curl gnupg
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null
apt update
```

#### 安装 Docker CE

```shell
apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### hello-world

```shell
docker run hello-world
```

### 安装 cri-docker

**注意：** Docker Engine 不实现[CRI](https://kubernetes.io/docs/concepts/architecture/cri/)，而 CRI 是容器运行时与 Kubernetes 配合使用的要求。因此，必须安装额外的服务[cri-dockerd](https://github.com/Mirantis/cri-dockerd)。cri-dockerd 是一个基于旧版内置 Docker 引擎支持的项目，该支持已在 1.24 版中从 kubelet 中[删除](https://kubernetes.io/dockershim)。

```shell
curl -fsSL https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.7/cri-dockerd_0.3.7.3-0.debian-bullseye_amd64.deb -o cri-dockerd.deb
apt install -y ./cri-dockerd.deb
```

### 代理

```shell
cat << EOF > /etc/docker/daemon.json
{
  "proxies": {
    "http-proxy": "http://192.168.80.1:7890",
    "https-proxy": "http://192.168.80.1:7890",
    "no-proxy": "localhost,127.0.0.0/8,10.0.0.0/8,192.168.0.0/16"
  }
}
EOF
```

