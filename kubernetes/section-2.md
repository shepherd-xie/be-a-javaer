# 安装 kubeadm

## 配置软件源

```shell
apt update
# apt-transport-https may be a dummy package; if so, you can skip that package
apt install -y apt-transport-https ca-certificates curl gpg
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
# This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | tee /etc/apt/sources.list.d/kubernetes.list
```

## 安装 kubeadm

```shell
apt-get update
apt install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
```

## 修改配置文件

```shell
kubeadm config print init-defaults --kubeconfig ClusterConfiguration > kubeadm.yml
```

```yaml
apiVersion: kubeadm.k8s.io/v1beta3
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
kind: InitConfiguration
localAPIEndpoint:
  advertiseAddress: 192.168.80.144 # 改为主机地址
  bindPort: 6443
nodeRegistration:
  criSocket: unix:///var/run/cri-dockerd.sock # 改为 cri-docker
  imagePullPolicy: IfNotPresent
  name: kube-alpha # 改为主机名称
  taints: null
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta3
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controllerManager: {}
dns: {}
etcd:
  local:
    dataDir: /var/lib/etcd
imageRepository: registry.k8s.io
kind: ClusterConfiguration
kubernetesVersion: 1.28.0
networking:
  dnsDomain: cluster.local
  podSubnet: 192.168.0.0/16 # 指定 pod 网络为 calico
  serviceSubnet: 10.96.0.0/12
scheduler: {}
---
# 开启 IPVS
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
featureGates: 
  SupportIPVSProxyMode: true
mode: ipvs
```

## 拉取镜像

```shell
kubeadm config images list --config kubeadm.yml
kubeadm config images pull --config kubeadm.yml
```

