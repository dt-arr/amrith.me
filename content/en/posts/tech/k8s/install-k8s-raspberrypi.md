---
title: "Kubernetes: Installing Kubernetes on a Raspberry Pi 5 in simple steps"
description: "This guide will walk you through the process of installing Kubernetes(k8s) on a Raspberry Pi, allowing you to set up a local Kubernetes cluster for learning, development, testing, or training purposes within your own network."
date: 2024-11-21T13:29:58+10:00
draft: false
image: images/icons/rpi-k8s.svg
enableToc: true
enableTocContent: true
tocPosition: inner
author: amrith
---



## Install Debian on Raspberry Pi


First of all, we need Debian installed on Raspberry Pi. Installing on Rasbian or other operating systems would be a waste of resources. Follow [this article](/posts/tech/install-debian-12-on-raspberrypi5) and install Vanilla Debian 12 on Raspberry Pi 5. 

### System Configuration (Raspberry Pi 5 - 8GB)
* CPU : 4
* RAM: 8 GB
* Disk: 128 GB (could be much smaller)
* Architecture: aarch64
* Model: ARM Cortex - A76

## Preparing the Linux System for Kubernetes

Run all the following commands as root or sudo or as required

### Ensure the host has a static IP

Follow the most convenient method to set up a static UP address.

Decide if you want to connect the Raspberry Pi via a Ethernet(eth0) or via Wireless lan(wlan0). Having connectivity to both does not matter but note that kubeadm will choose the primary IP address of the host. 

If you are connected via an Ethernet or Wireless LAN, create a file named `eth0` or `wlan0` in folder `/etc/network/interfaces.d/` with the contents similar to the following:

Change the `address`, `gateway`, `dns-server values` that meet your needs.
Refer [Network Configuration](https://wiki.debian.org/NetworkConfiguration#Configuring_the_interface_manually) in case you need to see additional details

Configure your router to reserve IP addresses based on MAC addresses if it supports configurations.


{{< codes eth0 wlan0 >}}
  {{< code >}}

  ```
auto eth0
iface eth0 inet static
    address 192.168.1.88
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 1.1.1.1 1.0.0.1
  ```

  {{< /code >}}


  {{< code >}}

  ```
auto wlan0
iface wlan0 inet static
    address 192.168.1.88
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 1.1.1.1 1.0.0.1
  ```

  {{< /code >}}
{{< /codes >}}




### Check if swap is ON and if so disable it

``` shell
swapoff -a
nano /etc/fstab # If applicable otherwise use the below approach
```
Some systems Swap is managed using `dphys-swapfile`. Follow the below steps to disable swap:

``` shell
sudo dphys-swapfile swapoff
sudo systemctl disable dphys-swapfile
sudo rm -f /var/swap
```
Make sure to reboot the machine and check again if swap is enabled or not:

```shell
swapon --show
````

### Preparing for Kubernetes Networking


Kubernetes requires certain kernel modules for proper networking and container support.

1. **Load Kernel Modules**: Enable `overlay` for container storage and `br_netfilter` for network bridge filtering.
2. **Configure Kernel Parameters**: Set parameters to enable IP forwarding and ensure iptables processes bridged traffic.
3. **Apply Changes**: Reload the system configuration to apply the updated parameters.

```shell
# Step 1: Load kernel modules
cat <<EOF | tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF

modprobe overlay
modprobe br_netfilter

# Step 2: Configure kernel parameters
cat <<EOF | tee /etc/sysctl.d/99-kubernetes-k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

# Step 3: Apply kernel parameter changes
sysctl --system
````


{{< expand "Output" >}}

You should see an output like the below:

```shell
overlay
br_netfilter
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
* Applying /usr/lib/sysctl.d/50-pid-max.conf ...
* Applying /etc/sysctl.d/98-rpi.conf ...
* Applying /etc/sysctl.d/99-kubernetes-k8s.conf ...
* Applying /usr/lib/sysctl.d/99-protect-links.conf ...
* Applying /etc/sysctl.d/99-sysctl.conf ...
* Applying /etc/sysctl.conf ...
kernel.pid_max = 4194304
kernel.printk = 3 4 1 3
vm.min_free_kbytes = 16384
net.ipv4.ping_group_range = 0 2147483647
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
fs.protected_fifos = 1
fs.protected_hardlinks = 1
fs.protected_regular = 2
fs.protected_symlinks = 1
root@rpi5-debian-k8s:/etc/sysctl.d#

```
You can also verify from the new files in:

````shell
cd /etc/sysctl.d/
cd /etc/modules-load.d
````

{{< /expand >}}

## Install Containerd

##### Install Containerd 
````shell
apt update
apt install -y containerd
````

##### Generate the `containerd` configuration
````shell
containerd config default > /etc/containerd/config.toml
````


##### Enable Systemd for Cgroup Management

Open the new config file in your text editor. The `-l` should help you see the line numbers
````shell
nano -l /etc/containerd/config.toml
````


Locate the section `[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]`

Within this section, find the attribute `SystemdCgroup` and set its value to `true` to enable the use of systemd for cgroup management. In my case, it was line number `114`

````shell
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
   SystemdCgroup = true
````

Setting `SystemdCgroup` to `true` ensures the container runtime uses systemd for cgroup management, aligning it with Kubernetes' cgroup driver for better compatibility, stability, and resource management. This avoids runtime conflicts and simplifies debugging.

{{< expand "Output" >}}
~
          [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
            BinaryName = ""
            CriuImagePath = ""
            CriuPath = ""
            CriuWorkPath = ""
            IoGid = 0
            IoUid = 0
            NoNewKeyring = false
            NoPivotRoot = false
            Root = ""
            ShimCgroup = ""
            SystemdCgroup = true
~
{{< /expand >}}

##### Enable containerd and then restart it

````shell
systemctl enable containerd
systemctl restart containerd
````

## Install Kubernetes

Once we have the system and containerd configured. We can head on installing Kubernetes itself.

- **Update package lists**: Run `sudo apt-get update` to refresh the package index on your system.
- **Install dependencies**: Install essential packages (`apt-transport-https`, `ca-certificates`, `curl`, and `gpg`) using `sudo apt-get install -y apt-transport-https ca-certificates curl gpg`.
- **Download and add Kubernetes GPG key**: Use `curl` to download the Kubernetes GPG key and save it to `/etc/apt/keyrings/kubernetes-apt-keyring.gpg` for secure package verification.
- **Add Kubernetes repository**: Add the Kubernetes repository to your sources list by running `echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list`.


````shell
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

````
{{< expand "Verify that the k8s.io repo is in the list the system connects to" >}}
root@rpi5-debian-k8s:/etc/apt/keyrings#
root@rpi5-debian-k8s:/etc/apt/keyrings# apt update
Hit:1 http://deb.debian.org/debian bookworm InRelease
Hit:2 http://deb.debian.org/debian-security bookworm-security InRelease
Hit:3 http://deb.debian.org/debian bookworm-updates InRelease
Get:4 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.31/deb  InRelease [1,186 B]
Get:5 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.31/deb  Packages [7,312 B]
Hit:6 http://archive.raspberrypi.com/debian bookworm InRelease
Fetched 8,498 B in 1s (11.4 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
65 packages can be upgraded. Run 'apt list --upgradable' to see them.
root@rpi5-debian-k8s:/etc/apt/keyrings#

{{< /expand >}}

Update the apt package index, install kubelet, kubeadm and kubectl:

````shell
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
````

To prevent accidental upgrade or uninstall
````shell
sudo apt-mark hold kubelet kubeadm kubectl
````
## Create the Kubernetes cluster

### Initialise the kubeadm

````shell
kubeadm init --control-plane-endpoint=$HOSTNAME
````

{{< expand "Troubleshooting errors" >}}

If you encounter errors using preflight like the below. You would need to following certain steps


{{< expand "missing required cgroups: memory and missing optional cgroups: hugetlb" >}}

```shell
root@rpi5-debian-k8s:/home/pi# kubeadm init
[init] Using Kubernetes version: v1.31.3
[preflight] Running pre-flight checks
[preflight] The system verification failed. Printing the output from the verification:
KERNEL_VERSION: 6.6.51+rpt-rpi-2712
CONFIG_NAMESPACES: enabled
CONFIG_NET_NS: enabled
CONFIG_PID_NS: enabled
CONFIG_IPC_NS: enabled
CONFIG_UTS_NS: enabled
CONFIG_CGROUPS: enabled
CONFIG_CGROUP_CPUACCT: enabled
CONFIG_CGROUP_DEVICE: enabled
CONFIG_CGROUP_FREEZER: enabled
CONFIG_CGROUP_PIDS: enabled
CONFIG_CGROUP_SCHED: enabled
CONFIG_CPUSETS: enabled
CONFIG_MEMCG: enabled
CONFIG_INET: enabled
CONFIG_EXT4_FS: enabled
CONFIG_PROC_FS: enabled
CONFIG_NETFILTER_XT_TARGET_REDIRECT: enabled (as module)
CONFIG_NETFILTER_XT_MATCH_COMMENT: enabled (as module)
CONFIG_FAIR_GROUP_SCHED: enabled
CONFIG_OVERLAY_FS: enabled (as module)
CONFIG_AUFS_FS: not set - Required for aufs.
CONFIG_BLK_DEV_DM: enabled (as module)
CONFIG_CFS_BANDWIDTH: enabled
CONFIG_CGROUP_HUGETLB: not set - Required for hugetlb cgroup.
CONFIG_SECCOMP: enabled
CONFIG_SECCOMP_FILTER: enabled
OS: Linux
CGROUPS_CPU: enabled
CGROUPS_CPUSET: enabled
CGROUPS_DEVICES: enabled
CGROUPS_FREEZER: enabled
CGROUPS_MEMORY: missing
CGROUPS_PIDS: enabled
CGROUPS_HUGETLB: missing
CGROUPS_IO: enabled
        [WARNING SystemVerification]: missing optional cgroups: hugetlb
error execution phase preflight: [preflight] Some fatal errors occurred:
        [ERROR SystemVerification]: missing required cgroups: memory
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher
root@rpi5-debian-k8s:/home/pi#

```
{{< /expand >}}

To address the `missing required cgroups: memory` issue:

add the following at the end of `/boot/cmdline.txt`

```shell
cgroup_enable=cpuset cgroup_enable=memory
````

{{< /expand >}}

```shell
~
Your Kubernetes control-plane has initialized successfully!
~
```

{{< expand "Full Output" >}}
root@rpi5-debian-k8s:/home/pi# kubeadm init --control-plane-endpoint=$HOSTNAME
[init] Using Kubernetes version: v1.31.3
[preflight] Running pre-flight checks
        [WARNING SystemVerification]: missing optional cgroups: hugetlb
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action beforehand using 'kubeadm config images pull'
W1121 14:57:34.271903    1530 checks.go:846] detected that the sandbox image "registry.k8s.io/pause:3.6" of the container runtime is inconsistent with that used by kubeadm.It is recommended to use "registry.k8s.io/pause:3.10" as the CRI sandbox image.
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local rpi5-debian-k8s] and IPs [10.96.0.1 192.168.1.88]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [localhost rpi5-debian-k8s] and IPs [192.168.1.88 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [localhost rpi5-debian-k8s] and IPs [192.168.1.88 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "super-admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests"
[kubelet-check] Waiting for a healthy kubelet at http://127.0.0.1:10248/healthz. This can take up to 4m0s
[kubelet-check] The kubelet is healthy after 1.000673248s
[api-check] Waiting for a healthy API server. This can take up to 4m0s
[api-check] The API server is healthy after 20.502096693s
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node rpi5-debian-k8s as control-plane by adding the labels: [node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
[mark-control-plane] Marking the node rpi5-debian-k8s as control-plane by adding the taints [node-role.kubernetes.io/control-plane:NoSchedule]
[bootstrap-token] Using token: h13289.n8rg6coc3icgotsy
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

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

You can now join any number of control-plane nodes by copying certificate authorities
and service account keys on each node and then running the following as root:

  kubeadm join rpi5-debian-k8s:6443 --token h13289.n8asdasdasasdotsy \
        --discovery-token-ca-cert-hash sha256:MAAAAASSSSSSSSSSKEEEEEEEEEEEEEEEEEED \
        --control-plane

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join rpi5-debian-k8s:6443 --token h13289.n8rg6coc3icgotsy \
        --discovery-token-ca-cert-hash sha256:MAAAAASSSSSSSSSSKEEEEEEEEEEEEEEEEEED
root@rpi5-debian-k8s:/home/pi#
{{< /expand >}}


#### Configuring Access to the Kubernetes Cluster

As a normal user

```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
As root user
```shell
export KUBECONFIG=/etc/kubernetes/admin.conf
```

Verify the installation

```shell
kubectl get pods
```
You should see an output like below:

```shell

pi@rpi5-debian-k8s:~ $ kubectl get nodes
NAME              STATUS     ROLES           AGE   VERSION
rpi5-debian-k8s   NotReady   control-plane   79m   v1.31.3
pi@rpi5-debian-k8s:~ $ 
```
#### Check for taints. 

```shell
$kubectl get nodes -o json | jq '.items[].spec.taints'
output:

[
  {
    "effect": "NoSchedule",
    "key": "node-role.kubernetes.io/control-plane"
  }
]

```
You may have to install `apt-get install jq` if `jq` isn't installed

#### Making the control-plane node  available for regular workloads.

The taint node-role.kubernetes.io/control-plane:NoSchedule is automatically applied to control-plane nodes during the cluster setup to prevent regular workloads (non-control-plane pods) from being scheduled on these nodes.

To fix this, we run the `kubectl taint` command:

```shell
kubectl taint node <nodename> node-role.kubernetes.io/control-plane:NoSchedule-

output:
node/<nodename> untainted

```
`kubectl get nodes` should show the node to be `ready` now

## Deploy a pod network to the cluster

In our case, lets install Calico.

#### Apply Calico Manifest

Run the following command to install Calico v3.29.0:

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.0/manifests/calico.yaml
```

### Verify that the pods are running:

```shell
$ kubectl get pods -n kube-system
NAME                                      READY   STATUS    RESTARTS   AGE
calico-kube-controllers-d4dc4cc65-zqcvl   1/1     Running   0          32m
calico-node-llccq                         1/1     Running   0          32m
coredns-7c65d6cfc9-5d457                  1/1     Running   0          7h48m
coredns-7c65d6cfc9-5lxs8                  1/1     Running   0          7h48m
etcd-rpi5-debian-k8s                      1/1     Running   0          7h48m
kube-apiserver-rpi5-debian-k8s            1/1     Running   0          7h48m
kube-controller-manager-rpi5-debian-k8s   1/1     Running   0          7h48m
kube-proxy-rpblj                          1/1     Running   0          7h48m
kube-scheduler-rpi5-debian-k8s            1/1     Running   0          7h48m
````

#### Allowing access to the kube network to other systems

#### Confirm the network range:

````shell
kubectl cluster-info dump | egrep -e '(service-cluster-ip-range|cluster-cidr)'
````
Output:
````shell
     "--service-cluster-ip-range=10.96.0.0/12",

````

#### Get the static IP that was set initally:

````shell
ip route get 1 | grep -o -P "src [0-9\.]+"
````

My case the output IP was `src 192.168.1.88`

#### Update your network routing

Update the routing in your WiFi router to route `10.96.0.0` via `192.168.1.88` and subnetmask `255.240.0.0`


## Conclusion

That's it!

You now have Kubernetes running on a tiny but powerful server in your home network. 

### Next: Install a fully functional Demo App

In the next section in the series, lets install a fully functional demo App:

Go ahead and deploy a Kubernetes app and see how it works!

