# 安装网络插件

## 安装 calico

### 下载配置文件

```shell
curl https://raw.githubusercontent.com/projectcalico/calico/v3.26.4/manifests/calico.yaml -O
```

### 修改配置项（可选）

如果之前 `kubeadm.yml` 中 pod CIDR 不是 `192.168.0.0/16` ，修改配置文件中的 `CALICO_IPV4POOL_CIDR` 使其保持一致。

```yaml
            # The default IPv4 pool to create on startup if none exists. Pod IPs will be
            # chosen from this range. Changing this value after installation will have
            # no effect. This should fall within `--cluster-cidr`.
            # - name: CALICO_IPV4POOL_CIDR
            #   value: "192.168.0.0/16"
```

### 安装 calico

```shell
kubectl apply -f calico.yaml
```





