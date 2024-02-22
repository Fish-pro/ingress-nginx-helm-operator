## ingress nginx helm operator

This operator is a simple operator that helps you to install the ingress-nginx in your kubernetes cluster.

## Quick Start

### 1. Clone the repository

```shell
➜  ~ git clone https://github.com/Fish-pro/ingress-nginx-helm-operator.git
正克隆到 'ingress-nginx-helm-operator'...
remote: Enumerating objects: 354, done.
remote: Counting objects: 100% (354/354), done.
remote: Compressing objects: 100% (196/196), done.
remote: Total 354 (delta 195), reused 297 (delta 138), pack-reused 0
接收对象中: 100% (354/354), 95.14 KiB | 154.00 KiB/s, 完成.
处理 delta 中: 100% (195/195), 完成.
➜  ~ cd ingress-nginx-helm-operator
➜  ingress-nginx-helm-operator git:(main)
```

### 1. Install the ingress-nginx helm operator

build the operator image and load image to kind cluster

```shell
➜  ingress-nginx-helm-operator git:(main) make docker-build IMG="example.com/ingress-nginx-operator:v0.0.1"
docker build -t example.com/ingress-nginx-operator:v0.0.1 .
[+] Building 2.3s (9/9) FINISHED
 => [internal] load build definition from Dockerfile                       0.0s
 => => transferring dockerfile: 238B                                       0.0s
 => [internal] load .dockerignore                                          0.0s
 => => transferring context: 2B                                            0.0s
 => [internal] load metadata for quay.io/operator-framework/helm-operator  2.0s
 => [internal] load build context                                          0.0s
 => => transferring context: 19.80kB                                       0.0s
 => [1/4] FROM quay.io/operator-framework/helm-operator:v1.33.0@sha256:05  0.0s
 => CACHED [2/4] COPY watches.yaml /opt/helm/watches.yaml                  0.0s
 => [3/4] COPY helm-charts  /opt/helm/helm-charts                          0.0s
 => [4/4] WORKDIR /opt/helm                                                0.0s
 => exporting to image                                                     0.1s
 => => exporting layers                                                    0.1s
 => => writing image sha256:ef486fc8d7b78f6c7484049e99bda5f402caffe5b5f47  0.0s
 => => naming to example.com/ingress-nginx-operator:v0.0.1                 0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
➜  ingress-nginx-helm-operator git:(main) kind load docker-image example.com/ingress-nginx-operator:v0.0.1  --name ik8s
Image: "example.com/ingress-nginx-operator:v0.0.1" with ID "sha256:ef486fc8d7b78f6c7484049e99bda5f402caffe5b5f47de75fba89d4b4e74159" not yet present on node "ik8s-control-plane", loading...
```

### 2. Install the ingress-nginx helm operator

install the operator to the kubernetes cluster

```shell
➜  ingress-nginx-helm-operator git:(main) make deploy IMG="example.com/ingress-nginx-operator:v0.0.1"
cd config/manager && /Users/york/go/bin/kustomize edit set image controller=example.com/ingress-nginx-operator:v0.0.1
/Users/york/go/bin/kustomize build config/default | kubectl apply -f -
# Warning: 'patchesStrategicMerge' is deprecated. Please use 'patches' instead. Run 'kustomize edit fix' to update your Kustomization automatically.
namespace/ingress-nginx-helm-operator-system created
customresourcedefinition.apiextensions.k8s.io/nginxingresses.charts.nginx.org.nginx.io created
serviceaccount/ingress-nginx-helm-operator-controller-manager created
role.rbac.authorization.k8s.io/ingress-nginx-helm-operator-leader-election-role created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-helm-operator-manager-role created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-helm-operator-metrics-reader created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-helm-operator-proxy-role created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-helm-operator-leader-election-rolebinding created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-helm-operator-manager-rolebinding created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-helm-operator-proxy-rolebinding created
service/ingress-nginx-helm-operator-controller-manager-metrics-service created
deployment.apps/ingress-nginx-helm-operator-controller-manager created
```

wait for the operator to be ready

```shell
➜  ingress-nginx-helm-operator git:(main) kubectl -n ingress-nginx-helm-operator-system get po
NAME                                                              READY   STATUS    RESTARTS   AGE
ingress-nginx-helm-operator-controller-manager-7b95b6d874-nlbws   2/2     Running   0          72s
```

### 3. Create a nginxingress

create a nginxingress resource

```shell
➜  ingress-nginx-helm-operator git:(main) kubectl create ns ingress-nginx
namespace/ingress-nginx created
➜  ingress-nginx-helm-operator git:(main) kubectl -n ingress-nginx create -f config/samples/charts.nginx.org_v1alpha1_nginxingress.yaml
nginxingress.charts.nginx.org.nginx.io/nginxingress-sample created
```

wait for the ingress-nginx to be ready

```shell
➜  ingress-nginx-helm-operator git:(main) kubectl -n ingress-nginx get po
NAME                                         READY   STATUS      RESTARTS   AGE
nginxingress-sample-65688d8c45-v8vpz         1/1     Running     0          51s
nginxingress-sample-admission-create-cmbtm   0/1     Completed   0          51s
nginxingress-sample-admission-patch-sz5df    0/1     Completed   1          51s
➜  ingress-nginx-helm-operator git:(main) kubectl get ingressclass
NAME    CONTROLLER             PARAMETERS   AGE
nginx   k8s.io/ingress-nginx   <none>       60s
```

At this point, ingress-nginx has been installed


## What's Next

More will be coming Soon. Welcome to [open an issue](https://github.com/Fish-pro/ingress-nginx-helm-operator/issues)
and [propose a PR](https://github.com/Fish-pro/ingress-nginx-helm-operator/pulls). 🎉🎉🎉

## Contributors

<a href="https://github.com/Fish-pro/ingress-nginx-helm-operator/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=Fish-pro/ingress-nginx-helm-operator" />
</a>

Made with [contrib.rocks](https://contrib.rocks).

## License

Fast is under the Apache 2.0 license. See the [LICENSE](LICENSE) file for details.
