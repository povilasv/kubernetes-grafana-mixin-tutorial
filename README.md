# Kubernetes Grafana Mixin Tutorial

# Setup


```
git clone https://github.com/google/jsonnet.git jsonnet_git
cd jsonnet_git
make
sudo mv jsonnet /usr/local/bin/
```

```
go get -u github.com/jsonnet-bundler/jsonnet-bundler/cmd/jb
```

# Using Kubernetes Grafana Mixin

## Create a new folder, cd into it.

## Initialise json bundler & get mixin

```
jb init
jb install https://github.com/povilasv/kubernetes-grafana-mixin
```

## Create a config

Now create a new file called `config.jsonnet`.

Edit the `config.jsonnet` file:

```
local kubedashboards = import 'kubernetes-grafana-mixin/mixin.libsonnet';

kubedashboards {
  _config+:: {
    kubeletSelector: 'job="kubernetes-nodes2"',
    kubeSchedulerSelector: 'job="kube-scheduler2"',
    kubeControllerManagerSelector: 'job="kube-controller-manager2"',
    kubeApiserverSelector: 'job="kube-apiserver2"',
    kubeProxySelector: 'job="kube-proxy2"',
  },
}
```

This will import the dashboard and override job selectors. 

<aside class="notice">
Adjust the Prometheus label selectors to your actual environment.
</aside>

## Now it's time to build the dashboard.

Create a `dashboards` directory.

```
mkdir dashboards
```

Call `jsonnet` to compile `config.libsonnet`:

```
jsonnet -J vendor -m dashboards -e '(import "config.libsonnet").grafanaDashboards'
```

```
dashboards/kube-apiserver.json
dashboards/kube-controller-manager.json
dashboards/kube-proxy.json
dashboards/kube-scheduler.json
dashboards/kubelet.json
```


## Result

List your dashboard directory. 

```
ls -l dashboards
-rw-r--r-- 1 povilasv povilasv 35746 Apr 26 08:29 kube-apiserver.json
-rw-r--r-- 1 povilasv povilasv 34790 Apr 26 08:29 kube-controller-manager.json
-rw-r--r-- 1 povilasv povilasv 62845 Apr 26 08:29 kubelet.json
-rw-r--r-- 1 povilasv povilasv 27673 Apr 26 08:29 kube-proxy.json
-rw-r--r-- 1 povilasv povilasv 25650 Apr 26 08:29 kube-scheduler.json
```

# Provisioning into Grafana

I highly recommend you provision these dashboards via config files.

You can read more about it in [grafana docs](https://grafana.com/docs/administration/provisioning/#dashboards).

# Updating your dashboards.

After some time passes, there will be changes to the dashboards. 

In order to update you need to execute:
```
jb update 
```

```
Cloning into 'vendor/.tmp/jsonnetpkg-kubernetes-grafana-mixin-master510511451'...

remote: Enumerating objects: 97, done.
remote: Counting objects: 100% (97/97), done.
remote: Compressing objects: 100% (67/67), done.
remote: Total 97 (delta 43), reused 78 (delta 27), pack-reused 0
Receiving objects: 100% (97/97), 44.14 KiB | 422.00 KiB/s, done.
Resolving deltas: 100% (43/43), done.
Already on 'master'
Your branch is up to date with 'origin/master'.
>>> Installed kubernetes-grafana-mixin version master
```

Then rebuild the dashboards.
