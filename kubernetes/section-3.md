# 使用 kubeadm 创建集群

## 初始化主节点

```shell
kubeadm init --config=kubeadm.yml --upload-certs | tee kubeadm-init.log
```

安装成功会看到如下提示：

```shell
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join <control-plane-host>:<control-plane-port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

## 加入从节点

在执行加入主节点之前：

* 修改主机名
* 修改 `kubeadm.yml` 

在主节点可以使用以下命令获取加入命令

```shell
kubeadm token create --print-join-command
```

加入成功会提示

```shell
This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
```

如果出现如下错误

```shell
Found multiple CRI endpoints on the host. Please define which one do you wish to use by setting the 'criSocket' field in the kubeadm configuration file: unix:///var/run/containerd/containerd.sock, unix:///var/run/cri-dockerd.sock
```

可以在加入命令之后添加 `--cri-socket unix:///var/run/cri-dockerd.sock` ，指定对应的 CRI 套接字。

## 查看节点

```shell
kubectl get nodes
```

## 查看 Pod

```shell
kubectl get pods -n kube-system -o wide
```

