# Installation & Configuration


Questions:
- Updgrade cluster to v1.20.0

<details><summary>Solution</summary>
<p>

```bash

(1) ControlPlane:
   kc drain controlplane
   kubeadm upgrade plan
   apt update
   apt install kubeadm=1.20.0-00
   kubeadm upgrade apply v1.20.0
   ppt install kubelet=1.20.0-00
   systemctl restart kubelet
   kubectl uncordon controlplane

   (2) Worker node:
   Kc drain node01
   Ssh node01
   apt update
   apt install kubeadm=1.20.0-00
   kubeadm upgrade node
   apt install kubelet=1.20.0-00
   systemctl restart kubelet
   exit
  
   Kc uncordon node01

```
</p>
</details>

Questions:
- Create a backup of etcd

<details><summary>Solution</summary>
<p>

```bash
export ETCDCTL_API=3 etcdctl snapshot save /opt/snapshot-pre-boot.db --endpoints=https://127.0.0.1:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key
```
</p>
</details>

Questions:
- Restore etcd backup.

<details><summary>Solution</summary>
<p>

```bash
etcdctl snapshot restore /opt/snapshot-pre-boot.db --data-dir=/var/lib/etcd-backup-restore

change the following path to updated data-dir
/etc/kubernetes/manifests/etcd.yaml
 volumes:
  - hostPath:
      path: /var/lib/etcd-backup-restore
      type: DirectoryOrCreate
    name: etcd-data

```
</p>
</details>
