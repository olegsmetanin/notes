# 2020-01-05 Install Kubernetes with cert-manager, nginx-ingress, nfs-server-provisioner, helm in CentOS 8

## Install Docker

```
$ sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
Adding repo from: https://download.docker.com/linux/centos/docker-ce.repo

$ sudo dnf -y install docker-ce --nobest
CentOS-8 - AppStream                                                   14 kB/s | 4.3 kB     00:00
CentOS-8 - Base                                                        12 kB/s | 3.9 kB     00:00
CentOS-8 - Extras                                                      23 kB/s | 1.5 kB     00:00
Docker CE Stable - x86_64                                              73 kB/s | 3.5 kB     00:00
Kubernetes                                                            456  B/s | 454  B     00:00
Dependencies resolved.

 Problem: package docker-ce-3:19.03.5-3.el7.x86_64 requires containerd.io >= 1.2.2-3, but none of the providers can be installed
  - cannot install the best candidate for the job
  - package containerd.io-1.2.10-3.2.el7.x86_64 is excluded
  - package containerd.io-1.2.2-3.3.el7.x86_64 is excluded
  - package containerd.io-1.2.2-3.el7.x86_64 is excluded
  - package containerd.io-1.2.4-3.1.el7.x86_64 is excluded
  - package containerd.io-1.2.5-3.1.el7.x86_64 is excluded
  - package containerd.io-1.2.6-3.3.el7.x86_64 is excluded
======================================================================================================
 Package                 Arch   Version                                        Repository        Size
======================================================================================================
Installing:
 docker-ce               x86_64 3:18.09.1-3.el7                                docker-ce-stable  19 M
Installing dependencies:
 container-selinux       noarch 2:2.94-1.git1e99f1d.module_el8.0.0+58+91b614e7 AppStream         43 k
 libcgroup               x86_64 0.41-19.el8                                    BaseOS            70 k
 policycoreutils-python-utils
                         noarch 2.8-16.1.el8                                   BaseOS           228 k
 tar                     x86_64 2:1.30-4.el8                                   BaseOS           838 k
 containerd.io           x86_64 1.2.0-3.el7                                    docker-ce-stable  22 M
 docker-ce-cli           x86_64 1:19.03.5-3.el7                                docker-ce-stable  39 M
Enabling module streams:
 container-tools                rhel8
Skipping packages with broken dependencies:
 docker-ce               x86_64 3:19.03.5-3.el7                                docker-ce-stable  24 M

Transaction Summary
======================================================================================================
Install  7 Packages
Skip     1 Package

Total download size: 81 M
Installed size: 341 M
Downloading Packages:
(1/7): container-selinux-2.94-1.git1e99f1d.module_el8.0.0+58+91b614e7 1.2 MB/s |  43 kB     00:00
(2/7): libcgroup-0.41-19.el8.x86_64.rpm                               1.6 MB/s |  70 kB     00:00
(3/7): policycoreutils-python-utils-2.8-16.1.el8.noarch.rpm           4.1 MB/s | 228 kB     00:00
(4/7): tar-1.30-4.el8.x86_64.rpm                                      8.9 MB/s | 838 kB     00:00
(5/7): docker-ce-18.09.1-3.el7.x86_64.rpm                              31 MB/s |  19 MB     00:00
(6/7): docker-ce-cli-19.03.5-3.el7.x86_64.rpm                          56 MB/s |  39 MB     00:00
(7/7): containerd.io-1.2.0-3.el7.x86_64.rpm                            23 MB/s |  22 MB     00:00
------------------------------------------------------------------------------------------------------
Total                                                                  64 MB/s |  81 MB     00:01
warning: /var/cache/dnf/docker-ce-stable-091d8a9c23201250/packages/containerd.io-1.2.0-3.el7.x86_64.rpm: Header V4 RSA/SHA512 Signature, key ID 621e9f35: NOKEY
Docker CE Stable - x86_64                                              41 kB/s | 1.6 kB     00:00
Importing GPG key 0x621E9F35:
 Userid     : "Docker Release (CE rpm) <docker@docker.com>"
 Fingerprint: 060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35
 From       : https://download.docker.com/linux/centos/gpg
Key imported successfully
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                              1/1
  Installing       : docker-ce-cli-1:19.03.5-3.el7.x86_64                                         1/7
  Running scriptlet: docker-ce-cli-1:19.03.5-3.el7.x86_64                                         1/7
  Installing       : containerd.io-1.2.0-3.el7.x86_64                                             2/7
  Running scriptlet: containerd.io-1.2.0-3.el7.x86_64                                             2/7
  Installing       : tar-2:1.30-4.el8.x86_64                                                      3/7
  Running scriptlet: tar-2:1.30-4.el8.x86_64                                                      3/7
  Installing       : policycoreutils-python-utils-2.8-16.1.el8.noarch                             4/7
  Installing       : container-selinux-2:2.94-1.git1e99f1d.module_el8.0.0+58+91b614e7.noarch      5/7
  Running scriptlet: container-selinux-2:2.94-1.git1e99f1d.module_el8.0.0+58+91b614e7.noarch      5/7
  Running scriptlet: libcgroup-0.41-19.el8.x86_64                                                 6/7
  Installing       : libcgroup-0.41-19.el8.x86_64                                                 6/7
  Running scriptlet: libcgroup-0.41-19.el8.x86_64                                                 6/7
  Running scriptlet: docker-ce-3:18.09.1-3.el7.x86_64                                             7/7
  Installing       : docker-ce-3:18.09.1-3.el7.x86_64                                             7/7
  Running scriptlet: docker-ce-3:18.09.1-3.el7.x86_64                                             7/7
  Verifying        : container-selinux-2:2.94-1.git1e99f1d.module_el8.0.0+58+91b614e7.noarch      1/7
  Verifying        : libcgroup-0.41-19.el8.x86_64                                                 2/7
  Verifying        : policycoreutils-python-utils-2.8-16.1.el8.noarch                             3/7
  Verifying        : tar-2:1.30-4.el8.x86_64                                                      4/7
  Verifying        : containerd.io-1.2.0-3.el7.x86_64                                             5/7
  Verifying        : docker-ce-3:18.09.1-3.el7.x86_64                                             6/7
  Verifying        : docker-ce-cli-1:19.03.5-3.el7.x86_64                                         7/7

Installed:
  docker-ce-3:18.09.1-3.el7.x86_64
  container-selinux-2:2.94-1.git1e99f1d.module_el8.0.0+58+91b614e7.noarch
  libcgroup-0.41-19.el8.x86_64
  policycoreutils-python-utils-2.8-16.1.el8.noarch
  tar-2:1.30-4.el8.x86_64
  containerd.io-1.2.0-3.el7.x86_64
  docker-ce-cli-1:19.03.5-3.el7.x86_64

Skipped:
  docker-ce-3:19.03.5-3.el7.x86_64

Complete!


$ sudo systemctl enable --now docker
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /usr/lib/systemd/system/docker.service.


$ sudo systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
   Active: active (running) since Sat 2020-01-04 21:33:21 CET; 9s ago
     Docs: https://docs.docker.com
 Main PID: 3881 (dockerd)
    Tasks: 10
   Memory: 89.7M
   CGroup: /system.slice/docker.service
           └─3881 /usr/bin/dockerd -H fd://

$ docker pull alpine
Using default tag: latest
latest: Pulling from library/alpine
e6b0cf9c0882: Pull complete
Digest: sha256:2171658620155679240babee0a7714f6509fae66898db422ad803b951257db78
Status: Downloaded newer image for alpine:latest
docker.io/library/alpine:latest

```

## Disable SELinux

```
$ setenforce 0
$ sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
```

## Install Kubernetes CLI

```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
```

## Install Kubernetes

```
$ systemctl enable --now kubelet


$ cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF


$ swapoff -a
// Remove swap entry from /etc/fstab if you have


$ kubeadm init --pod-network-cidr=10.244.0.0/16
W0104 21:56:04.141158    2615 validation.go:28] Cannot validate kube-proxy config - no validator is available
W0104 21:56:04.141549    2615 validation.go:28] Cannot validate kubelet config - no validator is available
[init] Using Kubernetes version: v1.17.0
[preflight] Running pre-flight checks
	[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
	[WARNING FileExisting-tc]: tc not found in system path
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [centos-8gb-fsn1-k8s-1 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 X.X.X.X]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [centos-8gb-fsn1-k8s-1 localhost] and IPs [X.X.X.X 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [centos-8gb-fsn1-k8s-1 localhost] and IPs [X.X.X.X 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
W0104 21:56:09.208229    2615 manifests.go:214] the default kube-apiserver authorization-mode is "Node,RBAC"; using "Node,RBAC"
[control-plane] Creating static Pod manifest for "kube-scheduler"
W0104 21:56:09.209236    2615 manifests.go:214] the default kube-apiserver authorization-mode is "Node,RBAC"; using "Node,RBAC"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 14.004851 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.17" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node centos-8gb-fsn1-k8s-1 as control-plane by adding the label "node-role.kubernetes.io/master=''"
[mark-control-plane] Marking the node centos-8gb-fsn1-k8s-1 as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: uonssz.djo6m41c1vaaz72n
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join X.X.X.X:6443 --token uonssz.djo6m41c1vaaz72n \
    --discovery-token-ca-cert-hash sha256:199bc8f264f3227b17565ab2363bd749b548cf9f2d18aa7bc53bebc81b9dtu1c

// !!! Do it if you have one-node kubernetes installation
$ kubectl taint nodes --all node-role.kubernetes.io/master-


$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config

$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
podsecuritypolicy.policy/psp.flannel.unprivileged created
clusterrole.rbac.authorization.k8s.io/flannel created
clusterrolebinding.rbac.authorization.k8s.io/flannel created
serviceaccount/flannel created
configmap/kube-flannel-cfg created
daemonset.apps/kube-flannel-ds-amd64 created
daemonset.apps/kube-flannel-ds-arm64 created
daemonset.apps/kube-flannel-ds-arm created
daemonset.apps/kube-flannel-ds-ppc64le created
daemonset.apps/kube-flannel-ds-s390x created


$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended.yaml

```

## Install cluster config on remote (admin) computer

```
local$ scp root@X.X.X.X:/etc/kubernetes/admin.conf ${HOME}/.kube/config


local$ brew install kubectx


local$ kubectx
kubernetes-admin@kubernetes

local$ kubectl get pods --all-namespaces
NAMESPACE     NAME                                            READY   STATUS    RESTARTS   AGE
kube-system   coredns-6955765f44-6thl8                        1/1     Running   0          56m
kube-system   coredns-6955765f44-p8lw6                        1/1     Running   0          56m
kube-system   etcd-centos-8gb-fsn1-k8s-1                      1/1     Running   0          56m
kube-system   kube-apiserver-centos-8gb-fsn1-k8s-1            1/1     Running   0          56m
kube-system   kube-controller-manager-centos-8gb-fsn1-k8s-1   1/1     Running   0          56m
kube-system   kube-flannel-ds-amd64-7tzm5                     1/1     Running   0          48m
kube-system   kube-proxy-sxhvt                                1/1     Running   0          56m
kube-system   kube-scheduler-centos-8gb-fsn1-k8s-1            1/1     Running   0          56m

```

## Open kubernetes dashboard

```
local$ kubectl proxy
```


Open http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/


## Add dashboard user

```
$ cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
EOF


$ cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
EOF


$ kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')



```

## Install Helm 3

```
server$ curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash

local$ brew install helm

```


## Install Ingress controller
```
$ helm repo add stable https://kubernetes-charts.storage.googleapis.com/

$ kubectl create namespace nginx-ingress

$ helm install nginx-ingress stable/nginx-ingress --namespace nginx-ingress \
    --set controller.service.externalIPs[0]=X.X.X.X

NAME: nginx-ingress
LAST DEPLOYED: Sun Jan  5 19:38:29 2020
NAMESPACE: nginx-ingress
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace nginx-ingress get services -o wide -w nginx-ingress-controller'

An example Ingress that makes use of the controller:

  apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    annotations:
      kubernetes.io/ingress.class: nginx
    name: example
    namespace: foo
  spec:
    rules:
      - host: www.example.com
        http:
          paths:
            - backend:
                serviceName: exampleService
                servicePort: 80
              path: /
    # This section is only required if TLS is to be enabled for the Ingress
    tls:
        - hosts:
            - www.example.com
          secretName: example-tls

If TLS is enabled for the Ingress, a Secret containing the certificate and key must also be provided:

  apiVersion: v1
  kind: Secret
  metadata:
    name: example-tls
    namespace: foo
  data:
    tls.crt: <base64 encoded cert>
    tls.key: <base64 encoded key>
  type: kubernetes.io/tls

```


## Install cert-manager

```
$ kubectl apply --validate=false\
    -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml


$ helm repo add jetstack https://charts.jetstack.io


$ kubectl create namespace cert-manager

// https://github.com/jetstack/cert-manager/issues/2423
$ helm install cert-manager jetstack/cert-manager --namespace cert-manager --version v0.12.0

$ cat <<EOF | kubectl apply -f -
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    # You must replace this email address with your own.
    # Let's Encrypt will use this to contact you about expiring
    # certificates, and issues related to your account.
    email: your@email.com
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      # Secret resource used to store the account's private key.
      name: letsencrypt-prod
    # Add a single challenge solver, HTTP01 using nginx
    solvers:
    - http01:
        ingress:
          class: nginx
EOF


```



## Deploy NFS dynamic provisioner

```
// install nfs utils on all nodes
server$ yum install nfs-utils

$ cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: PersistentVolume
metadata:
  name: data-nfs-server-provisioner-0
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /nfs-volumes/data-nfs-server-provisioner-0
  claimRef:
    namespace: default
    name: data-nfs-server-provisioner-0
EOF


$ helm install nfs-server-provisioner stable/nfs-server-provisioner \
  --set persistence.enabled=true \
  --set persistence.size=5Gi

```

## Deploy multitool

```
$ kubectl run --generator=run-pod/v1 nwtool --image praqma/network-multitool
deployment.apps/nwtool created

```

## Deploy example app

```

$ kubectl create namespace wordpress

$ helm install wordpress stable/wordpress --namespace wordpress \
  --set persistence.accessMode=ReadWriteMany \
  --set persistence.storageClass=nfs \
  --set persistence.size=1Gi \
  --set mariadb.master.persistence.storageClass=nfs \
  --set mariadb.master.persistence.size=1Gi \
  --set ingress.enabled=true \
  --set ingress.annotations."kubernetes\.io\/ingress\.class"="nginx" \
  --set ingress.annotations."cert-manager\.io\/cluster-issuer"="letsencrypt-prod" \
  --set ingress.tls[0].secretName=wordpress2.local-tls \
  --set ingress.tls[0].hosts[0]=three.staging.smetan.in \
  --set ingress.hosts[0].name=three.staging.smetan.in

open https://one.staging.smetan.in

```


## Interesting articles

[Kubernetes clusters for the hobbyist](https://github.com/hobby-kube/guide)

[Install a Kubernetes cluster on Hetzner cloud servers](https://community.hetzner.com/tutorials/install-kubernetes-cluster)

## TODO

Explore Helm 3 values piping
[Helm 3 values](https://v2.helm.sh/docs/chart_best_practices/#consider-how-users-will-use-your-values)
[Helm should support reading the values file from STDIN](https://github.com/helm/helm/issues/2709)
[Support reading values from STDIN for `upgrade` command](https://github.com/helm/helm/issues/7002)

```

cat <<EOF | helm install nginx-ingress stable/nginx-ingress --namespace nginx-ingress --wait --debug -f -
controller:
  service
    externalIPs: [X.X.X.X]
EOF
```
