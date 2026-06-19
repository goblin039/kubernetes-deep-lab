# Lab Journal

Lab journal - рабочий журнал участника Kubernetes Deep Lab.

Он нужен, чтобы фиксировать прогресс, команды, выводы, ошибки, вопросы и выводы по каждой лабораторной.

## Участник

| Поле | Значение |
|---|---|
| Имя | Вячеслав  |
| GitHub | goblin039 |

## Общий прогресс

| Lab | Status | PR | Notes |
|---|---|---|---|
| Lab 00 - Environment Validation | Done |  |  |
| Lab 01 - Node Baseline | Done |  |  |
| Lab 02 - Container Runtime: containerd | Done | | |
| Lab 03 - kubeadm prerequisites | Done | | |
| Lab 04 - kubeadm cluster bootstrap | Done | | |
| Lab 05 - CNI bootstrap with Calico | Done | | |
| Lab 06 - Разбор kubeadm-кластера после установки | Done | | |

## Lab 07 - Kubernetes API objects and kubectl workflow

### Дата

2026-06-DD

### Цель

Изучать базовые объекты Kubernetes не как "набор YAML-файлов", а как систему связанных сущностей внутри Kubernetes API.

### Что было сделано

- Lab 07.01 - Namespace
- Lab 07.02 - Pod
- Lab 07.03 - Labels and selectors
- Lab 07.04 - ReplicaSet

### Команды

команды разных блоков буду разделять горизонтальными линиями

-----
**Lab 07.01 - Namespace**   
```
root@deep-cp-01:/home/goblin# kubectl apply  -f manifests/namespace.yaml
namespace/kdl-lab07 created  
root@deep-cp-01:/home/goblin# kubectl get ns kdl-lab07 --show-labels
NAME        STATUS   AGE    LABELS
kdl-lab07   Active   107s   app.kubernetes.io/name=kdl-lab07,app.kubernetes.io/part-of=kubernetes-deep-lab,kdl/lab=07,kubernetes.io/metadata.name=kdl-lab07

root@deep-cp-01:/home/goblin# kubectl get ns kdl-lab07 -oyaml
...
metadata:
  annotations:
    kdl/description: Kubernetes Deep Lab 07 namespace
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"Namespace","metadata":{"annotations":{"kdl/description":"Kubernetes Deep Lab 07 namespace"},"labels":{"app.kubernetes.io/name":"kdl-lab07","app.kubernetes.io/part-of":"kubernetes-deep-lab","kdl/lab":"07"},"name":"kdl-lab07"}}
  creationTimestamp: "2026-06-18T12:27:14Z"
  labels:
    app.kubernetes.io/name: kdl-lab07
    app.kubernetes.io/part-of: kubernetes-deep-lab
    kdl/lab: "07"
    kubernetes.io/metadata.name: kdl-lab07
  name: kdl-lab07
  resourceVersion: "90289"
  uid: d09c7185-44fe-4ae1-8db7-7dda0f53fdf8
...  
spec:
  finalizers:
  - kubernetes
...
status:
  phase: Active

root@deep-cp-01:/home/goblin# kubectl describe ns kdl-lab07
Name:         kdl-lab07
Labels:       app.kubernetes.io/name=kdl-lab07
              app.kubernetes.io/part-of=kubernetes-deep-lab
              kdl/lab=07
              kubernetes.io/metadata.name=kdl-lab07
Annotations:  kdl/description: Kubernetes Deep Lab 07 namespace
Status:       Active

No resource quota.

No LimitRange resource.

root@deep-cp-01:/home/goblin# kubectl label namespace kdl-lab07 kdl/phase=api-basics
root@deep-cp-01:/home/goblin# kubectl get ns kdl-lab07 --show-labels
NAME        STATUS   AGE     LABELS
kdl-lab07   Active   7m49s   app.kubernetes.io/name=kdl-lab07,app.kubernetes.io/part-of=kubernetes-deep-lab,kdl/lab=07,kdl/phase=api-basics,kubernetes.io/metadata.name=kdl-lab07

root@deep-cp-01:/home/goblin# kubectl diff -f  manifests/namespace.yaml
root@deep-cp-01:/home/goblin# 

root@deep-cp-01:/home/goblin# kubectl diff -f  manifests/namespace.yaml
root@deep-cp-01:/home/goblin# kubectl diff -f manifests/namespace.yaml
root@deep-cp-01:/home/goblin# kubectl apply -f manifests/namespace.yaml
namespace/kdl-lab07 configured

root@deep-cp-01:/home/goblin# kubectl get all -n kdl-lab07
No resources found in kdl-lab07 namespace.
root@deep-cp-01:/home/goblin# kubectl get events -n kdl-lab07
No resources found in kdl-lab07 namespace.

root@deep-cp-01:/home/goblin# kubectl get namespace kdl-lab07 -n default
NAME        STATUS   AGE
kdl-lab07   Active   39m
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07
No resources found in kdl-lab07 namespace.
```

-----
**Lab 07.02 - Pod**  
```
root@deep-cp-01:/home/goblin# kubectl get ns kdl-lab07
NAME        STATUS   AGE
kdl-lab07   Active   119m

root@deep-cp-01:/home/goblin# kubectl explain pod
KIND:       Pod
VERSION:    v1

DESCRIPTION:
    Pod is a collection of containers that can run on a host. This resource is
    created by clients and scheduled onto hosts.
    
FIELDS:
  apiVersion    <string>
    APIVersion defines the versioned schema of this representation of an object.
    Servers should convert recognized schemas to the latest internal value, and
    may reject unrecognized values. More info:
    https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources

  kind  <string>
    Kind is a string value representing the REST resource this object
    represents. Servers may infer this from the endpoint the client submits
    requests to. Cannot be updated. In CamelCase. More info:
    https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds

  metadata      <ObjectMeta>
    Standard object's metadata. More info:
    https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata

  spec  <PodSpec>
    Specification of the desired behavior of the pod. More info:
    https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status

  status        <PodStatus>
    Most recently observed status of the pod. This data may not be up to date.
    Populated by the system. Read-only. More info:
    https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status

root@deep-cp-01:/home/goblin#  kubectl explain pod.spec
root@deep-cp-01:/home/goblin#  kubectl explain pod.spec.containers
root@deep-cp-01:/home/goblin#  kubectl explain pod.spec.containers.image


root@deep-cp-01:/home/goblin# kubectl apply --dry-run=client -f manifests/pod-nginx.yaml
pod/nginx-pod created (dry run)
root@deep-cp-01:/home/goblin# kubectl apply --dry-run=server -f manifests/pod-nginx.yaml
pod/nginx-pod created (server dry run)

root@deep-cp-01:/home/goblin# kubectl apply -f manifests/pod-nginx.yaml
pod/nginx-pod created

root@deep-cp-01:/home/goblin# kubectl -n kdl-lab07 get pods -o wide
NAME        READY   STATUS    RESTARTS   AGE   IP               NODE          NOMINATED NODE   READINESS GATES
nginx-pod   1/1     Running   0          26m   192.168.19.136   deep-wrk-02   <none>           <none>

root@deep-cp-01:/home/goblin# kubectl wait --for=condition=Ready pod/nginx-pod -n kdl-lab07 --timeout=120s
pod/nginx-pod condition met

kubectl get pod nginx-pod -n kdl-lab07 -o yaml
...
metadata:
  annotations:
    cni.projectcalico.org/containerID: 63abb1a35203a7fe8ce77bb9165a5c0087688e404cfb8e025d486a81c71b4955
    cni.projectcalico.org/podIP: 192.168.19.136/32
    cni.projectcalico.org/podIPs: 192.168.19.136/32
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"labels":{"app.kubernetes.io/name":"nginx","app.kubernetes.io/part-of":"kubernetes-deep-lab","kdl/component":"pod","kdl/lab":"07"},"name":"nginx-pod","namespace":"kdl-lab07"},"spec":{"containers":[{"image":"nginx:1.27.5","name":"nginx","ports":[{"containerPort":80}]}]}}
  creationTimestamp: "2026-06-18T14:33:11Z"
  generation: 1
  labels:
    app.kubernetes.io/name: nginx
    app.kubernetes.io/part-of: kubernetes-deep-lab
    kdl/component: pod
    kdl/lab: "07"
  name: nginx-pod
  namespace: kdl-lab07
  resourceVersion: "108880"
  uid: f53c4018-9bc7-482b-9a03-21edf6dde026
...
spec:
  containers:
  - image: nginx:1.27.5
    imagePullPolicy: IfNotPresent
    name: nginx
    ports:
    - containerPort: 80
      protocol: TCP
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-5hb5j
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: deep-wrk-02
...
status:
  conditions:
  containerStatuses:
  - containerID: containerd://0568dddbe31b54ac6237ec7bc2acb58af92d9dcd0e9ece10b94aa58b2724a5f2
    image: docker.io/library/nginx:1.27.5
    imageID: docker.io/library/nginx@sha256:6784fb0834aa7dbbe12e3d7471e69c290df3e6ba810dc38b34ae33d3c1c05f7d
    lastState: {}
    name: nginx
    ready: true    
    resources: {}
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2026-06-18T14:53:25Z"
    user:
      linux:
        gid: 0
        supplementalGroups:
        - 0
        uid: 0


root@deep-cp-01:/home/goblin# kubectl describe pod nginx-pod -n kdl-lab07
Name:             nginx-pod
Namespace:        kdl-lab07
Priority:         0
Service Account:  default
Node:             deep-wrk-02/192.168.100.52
Start Time:       Thu, 18 Jun 2026 14:33:11 +0000
Labels:           app.kubernetes.io/name=nginx
                  app.kubernetes.io/part-of=kubernetes-deep-lab
                  kdl/component=pod
                  kdl/lab=07
Annotations:      cni.projectcalico.org/containerID: 63abb1a35203a7fe8ce77bb9165a5c0087688e404cfb8e025d486a81c71b4955
                  cni.projectcalico.org/podIP: 192.168.19.136/32
                  cni.projectcalico.org/podIPs: 192.168.19.136/32
Status:           Running
IP:               192.168.19.136
IPs:
  IP:  192.168.19.136
Containers:
  nginx:
    Container ID:   containerd://0568dddbe31b54ac6237ec7bc2acb58af92d9dcd0e9ece10b94aa58b2724a5f2
    Image:          nginx:1.27.5
    Image ID:       docker.io/library/nginx@sha256:6784fb0834aa7dbbe12e3d7471e69c290df3e6ba810dc38b34ae33d3c1c05f7d
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Thu, 18 Jun 2026 14:53:25 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-5hb5j (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-5hb5j:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  41m                default-scheduler  Successfully assigned kdl-lab07/nginx-pod to deep-wrk-02
  Warning  Failed     40m                kubelet            spec.containers{nginx}: Failed to pull image "nginx:1.27.5": failed to pull and unpack image "docker.io/library/nginx:1.27.5": failed to copy: httpReadSeeker: failed open: failed to do request: Get "https://production.cloudfront.docker.com/registry-v2/docker/registry/v2/blobs/sha256/1e/1e5f3c5b981a9f91ca91cf13ce87c2eedfc7a083f4f279552084dd08fc477512/data?Expires=1781796202&Signature=rlFgd7cYFSdpgJTC2fuy-L2lrelDsqTzI3Y7D0PYG1o9-rBxJnjMsv-SexpKd6LCuDlqYVmmX6PIaAouANx14QzhC91-TxwV5DVMSQLKAfL44VGi6voug85giRJeYy7b0ytKOP5WCf1n2Q7Fu6CMt~2qKUkQgpmIZWTZ9t5ynzrD2QZ7LA3v2CGJuiecAwhcEs8aSft9OjoRnx3kCP3C92dXDtS8boALkO7mbwDK12Z6VahQ2WpfGnj-w7JTkjS9h6EbZiIqj~o5BykdCvDSMfoiryjZN-EVWMc01lhEoMjGQB3glH3o68cCvkDItiFKTMmmapJ2LxkOB81d-3r7DA__&Key-Pair-Id=K2C9XPB6FLAKUF": dial tcp [2600:9000:26de:4600:9:4855:aac0:93a1]:443: connect: network is unreachable
  Warning  Failed     40m                kubelet            spec.containers{nginx}: Error: ErrImagePull
  Normal   BackOff    40m                kubelet            spec.containers{nginx}: Back-off pulling image "nginx:1.27.5"
  Warning  Failed     40m                kubelet            spec.containers{nginx}: Error: ImagePullBackOff
  Normal   Pulling    40m (x2 over 41m)  kubelet            spec.containers{nginx}: Pulling image "nginx:1.27.5"
  Normal   Pulled     20m                kubelet            spec.containers{nginx}: Successfully pulled image "nginx:1.27.5" in 19m40.063s (19m40.063s including waiting). Image size: 72406859 bytes.
  Normal   Created    20m                kubelet            spec.containers{nginx}: Container created
  Normal   Started    20m                kubelet            spec.containers{nginx}: Container started

root@deep-cp-01:/home/goblin# kubectl logs pod/nginx-pod -n kdl-lab07
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/06/18 14:53:25 [notice] 1#1: using the "epoll" event method
2026/06/18 14:53:25 [notice] 1#1: nginx/1.27.5
2026/06/18 14:53:25 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2026/06/18 14:53:25 [notice] 1#1: OS: Linux 6.8.0-124-generic
2026/06/18 14:53:25 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1024:524288
2026/06/18 14:53:25 [notice] 1#1: start worker processes
2026/06/18 14:53:25 [notice] 1#1: start worker process 29
2026/06/18 14:53:25 [notice] 1#1: start worker process 30

root@deep-cp-01:/home/goblin# kubectl exec -n kdl-lab07 pod/nginx-pod -- nginx -v
nginx version: nginx/1.27.5
root@deep-cp-01:/home/goblin# kubectl exec -n kdl-lab07 pod/nginx-pod -- curl -I http://127.0.0.1:80
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0   615    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Server: nginx/1.27.5
Date: Fri, 19 Jun 2026 10:14:37 GMT
Content-Type: text/html
Content-Length: 615
Last-Modified: Wed, 16 Apr 2025 12:01:11 GMT
Connection: keep-alive
ETag: "67ff9c07-267"
Accept-Ranges: bytes

root@deep-cp-01:/home/goblin# kubectl logs pod/nginx-pod -n kdl-lab07
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/06/19 09:31:32 [notice] 1#1: using the "epoll" event method
2026/06/19 09:31:32 [notice] 1#1: nginx/1.27.5
2026/06/19 09:31:32 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2026/06/19 09:31:32 [notice] 1#1: OS: Linux 6.8.0-124-generic
2026/06/19 09:31:32 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1024:524288
2026/06/19 09:31:32 [notice] 1#1: start worker processes
2026/06/19 09:31:32 [notice] 1#1: start worker process 29
2026/06/19 09:31:32 [notice] 1#1: start worker process 30
127.0.0.1 - - [19/Jun/2026:10:14:37 +0000] "HEAD / HTTP/1.1" 200 0 "-" "curl/7.88.1" "-"

root@deep-cp-01:/home/goblin# kubectl get events -n kdl-lab07 --sort-by=.metadata.creationTimestamp
LAST SEEN   TYPE      REASON           OBJECT          MESSAGE
19h         Normal    Scheduled        pod/nginx-pod   Successfully assigned kdl-lab07/nginx-pod to deep-wrk-02
19h         Normal    Pulling          pod/nginx-pod   Pulling image "nginx:1.27.5"
19h         Warning   Failed           pod/nginx-pod   Failed to pull image "nginx:1.27.5": failed to pull and unpack image "docker.io/library/nginx:1.27.5": failed to copy: httpReadSeeker: failed open: failed to do request: Get "https://production.cloudfront.docker.com/registry-v2/docker/registry/v2/blobs/sha256/1e/1e5f3c5b981a9f91ca91cf13ce87c2eedfc7a083f4f279552084dd08fc477512/data?Expires=1781796202&Signature=rlFgd7cYFSdpgJTC2fuy-L2lrelDsqTzI3Y7D0PYG1o9-rBxJnjMsv-SexpKd6LCuDlqYVmmX6PIaAouANx14QzhC91-TxwV5DVMSQLKAfL44VGi6voug85giRJeYy7b0ytKOP5WCf1n2Q7Fu6CMt~2qKUkQgpmIZWTZ9t5ynzrD2QZ7LA3v2CGJuiecAwhcEs8aSft9OjoRnx3kCP3C92dXDtS8boALkO7mbwDK12Z6VahQ2WpfGnj-w7JTkjS9h6EbZiIqj~o5BykdCvDSMfoiryjZN-EVWMc01lhEoMjGQB3glH3o68cCvkDItiFKTMmmapJ2LxkOB81d-3r7DA__&Key-Pair-Id=K2C9XPB6FLAKUF": dial tcp [2600:9000:26de:4600:9:4855:aac0:93a1]:443: connect: network is unreachable
19h         Warning   Failed           pod/nginx-pod   Error: ErrImagePull
19h         Normal    BackOff          pod/nginx-pod   Back-off pulling image "nginx:1.27.5"
19h         Warning   Failed           pod/nginx-pod   Error: ImagePullBackOff
19h         Normal    Pulled           pod/nginx-pod   Successfully pulled image "nginx:1.27.5" in 19m40.063s (19m40.063s including waiting). Image size: 72406859 bytes.
19h         Normal    Created          pod/nginx-pod   Container created
19h         Normal    Started          pod/nginx-pod   Container started
48m         Normal    SandboxChanged   pod/nginx-pod   Pod sandbox changed, it will be killed and re-created.
48m         Normal    Pulled           pod/nginx-pod   Container image "nginx:1.27.5" already present on machine and can be accessed by the pod
48m         Normal    Created          pod/nginx-pod   Container created
48m         Normal    Started          pod/nginx-pod   Container started

root@deep-cp-01:/home/goblin# kubectl get pod nginx-pod -n kdl-lab07 -o wide
NAME        READY   STATUS    RESTARTS      AGE   IP               NODE          NOMINATED NODE   READINESS GATES
nginx-pod   1/1     Running   1 (51m ago)   19h   192.168.19.137   deep-wrk-02   <none>           <none>


root@deep-cp-01:/home/goblin# kubectl diff -f manifests/pod-nginx-v2.yaml
diff -u -N /tmp/LIVE-2577371688/v1.Pod.kdl-lab07.nginx-pod /tmp/MERGED-2753362076/v1.Pod.kdl-lab07.nginx-pod
--- /tmp/LIVE-2577371688/v1.Pod.kdl-lab07.nginx-pod     2026-06-19 10:24:54.676724962 +0000
+++ /tmp/MERGED-2753362076/v1.Pod.kdl-lab07.nginx-pod   2026-06-19 10:24:54.677724966 +0000
@@ -14,6 +14,7 @@
     app.kubernetes.io/part-of: kubernetes-deep-lab
     kdl/component: pod
     kdl/lab: "07"
+    kdl/version: v2
   name: nginx-pod
   namespace: kdl-lab07
   resourceVersion: "112607"

root@deep-cp-01:/home/goblin# kubectl apply -f manifests/pod-nginx-v2.yaml
pod/nginx-pod configured
root@deep-cp-01:/home/goblin# kubectl get pod nginx-pod -n kdl-lab07 --show-labels
NAME        READY   STATUS    RESTARTS      AGE   LABELS
nginx-pod   1/1     Running   1 (54m ago)   19h   app.kubernetes.io/name=nginx,app.kubernetes.io/part-of=kubernetes-deep-lab,kdl/component=pod,kdl/lab=07,kdl/version=v2

root@deep-cp-01:/home/goblin# kubectl get pod nginx-pod -n kdl-lab07
NAME        READY   STATUS    RESTARTS      AGE
nginx-pod   1/1     Running   1 (56m ago)   19h
root@deep-cp-01:/home/goblin# kubectl delete pod nginx-pod -n kdl-lab07
pod "nginx-pod" deleted from kdl-lab07 namespace
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07
No resources found in kdl-lab07 namespace.

root@deep-cp-01:/home/goblin# kubectl apply -f manifests/pod-nginx-v2.yaml
pod/nginx-pod created
root@deep-cp-01:/home/goblin# kubectl get pod nginx-pod -n kdl-lab07 -o wide
NAME        READY   STATUS    RESTARTS   AGE   IP               NODE          NOMINATED NODE   READINESS GATES
nginx-pod   1/1     Running   0          5s    192.168.19.139   deep-wrk-02   <none>           <none>

root@deep-cp-01:/home/goblin# kubectl apply --dry-run=server -f manifests/pod-broken-image.yaml
pod/broken-image-pod created (server dry run)
root@deep-cp-01:/home/goblin# kubectl apply -f manifests/pod-broken-image.yaml
pod/broken-image-pod created
root@deep-cp-01:/home/goblin# kubectl get pod broken-image-pod -n kdl-lab07
NAME               READY   STATUS         RESTARTS   AGE
broken-image-pod   0/1     ErrImagePull   0          9s
root@deep-cp-01:/home/goblin# kubectl describe pod broken-image-pod -n kdl-lab07
Name:             broken-image-pod
Namespace:        kdl-lab07
Priority:         0
Service Account:  default
Node:             deep-wrk-01/192.168.100.51
Start Time:       Fri, 19 Jun 2026 10:29:49 +0000
Labels:           app.kubernetes.io/name=broken-image
                  app.kubernetes.io/part-of=kubernetes-deep-lab
                  kdl/component=pod
                  kdl/lab=07
Annotations:      cni.projectcalico.org/containerID: 87e2e8bcb20bc611526de82d5d0d93afe6de7356daad2f844b8922464cfa2d16
                  cni.projectcalico.org/podIP: 192.168.158.198/32
                  cni.projectcalico.org/podIPs: 192.168.158.198/32
Status:           Pending
IP:               192.168.158.198
IPs:
  IP:  192.168.158.198
Containers:
  nginx:
    Container ID:   
    Image:          nginx:this-tag-does-not-exist
    Image ID:       
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-tp2lv (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  kube-api-access-tp2lv:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  23s               default-scheduler  Successfully assigned kdl-lab07/broken-image-pod to deep-wrk-01
  Warning  Failed     15s               kubelet            spec.containers{nginx}: Failed to pull image "nginx:this-tag-does-not-exist": rpc error: code = NotFound desc = failed to pull and unpack image "docker.io/library/nginx:this-tag-does-not-exist": failed to resolve image: docker.io/library/nginx:this-tag-does-not-exist: not found
  Warning  Failed     15s               kubelet            spec.containers{nginx}: Error: ErrImagePull
  Normal   BackOff    14s               kubelet            spec.containers{nginx}: Back-off pulling image "nginx:this-tag-does-not-exist"
  Warning  Failed     14s               kubelet            spec.containers{nginx}: Error: ImagePullBackOff
  Normal   Pulling    2s (x2 over 22s)  kubelet            spec.containers{nginx}: Pulling image "nginx:this-tag-does-not-exist"

root@deep-cp-01:/home/goblin# kubectl get events -n kdl-lab07 --sort-by=.metadata.creationTimestamp
LAST SEEN   TYPE      REASON           OBJECT                 MESSAGE
19h         Normal    Scheduled        pod/nginx-pod          Successfully assigned kdl-lab07/nginx-pod to deep-wrk-02
19h         Normal    Pulling          pod/nginx-pod          Pulling image "nginx:1.27.5"
19h         Warning   Failed           pod/nginx-pod          Error: ErrImagePull
19h         Warning   Failed           pod/nginx-pod          Failed to pull image "nginx:1.27.5": failed to pull and unpack image "docker.io/library/nginx:1.27.5": failed to copy: httpReadSeeker: failed open: failed to do request: Get "https://production.cloudfront.docker.com/registry-v2/docker/registry/v2/blobs/sha256/1e/1e5f3c5b981a9f91ca91cf13ce87c2eedfc7a083f4f279552084dd08fc477512/data?Expires=1781796202&Signature=rlFgd7cYFSdpgJTC2fuy-L2lrelDsqTzI3Y7D0PYG1o9-rBxJnjMsv-SexpKd6LCuDlqYVmmX6PIaAouANx14QzhC91-TxwV5DVMSQLKAfL44VGi6voug85giRJeYy7b0ytKOP5WCf1n2Q7Fu6CMt~2qKUkQgpmIZWTZ9t5ynzrD2QZ7LA3v2CGJuiecAwhcEs8aSft9OjoRnx3kCP3C92dXDtS8boALkO7mbwDK12Z6VahQ2WpfGnj-w7JTkjS9h6EbZiIqj~o5BykdCvDSMfoiryjZN-EVWMc01lhEoMjGQB3glH3o68cCvkDItiFKTMmmapJ2LxkOB81d-3r7DA__&Key-Pair-Id=K2C9XPB6FLAKUF": dial tcp [2600:9000:26de:4600:9:4855:aac0:93a1]:443: connect: network is unreachable
19h         Warning   Failed           pod/nginx-pod          Error: ImagePullBackOff
19h         Normal    BackOff          pod/nginx-pod          Back-off pulling image "nginx:1.27.5"
19h         Normal    Pulled           pod/nginx-pod          Successfully pulled image "nginx:1.27.5" in 19m40.063s (19m40.063s including waiting). Image size: 72406859 bytes.
19h         Normal    Created          pod/nginx-pod          Container created
19h         Normal    Started          pod/nginx-pod          Container started
59m         Normal    SandboxChanged   pod/nginx-pod          Pod sandbox changed, it will be killed and re-created.
59m         Normal    Pulled           pod/nginx-pod          Container image "nginx:1.27.5" already present on machine and can be accessed by the pod
59m         Normal    Started          pod/nginx-pod          Container started
59m         Normal    Created          pod/nginx-pod          Container created
3m30s       Normal    Killing          pod/nginx-pod          Stopping container nginx
2m51s       Normal    Scheduled        pod/nginx-pod          Successfully assigned kdl-lab07/nginx-pod to deep-wrk-02
2m51s       Normal    Pulled           pod/nginx-pod          Container image "nginx:1.27.5" already present on machine and can be accessed by the pod
2m51s       Normal    Created          pod/nginx-pod          Container created
2m50s       Normal    Started          pod/nginx-pod          Container started
52s         Normal    Scheduled        pod/broken-image-pod   Successfully assigned kdl-lab07/broken-image-pod to deep-wrk-01
31s         Normal    Pulling          pod/broken-image-pod   Pulling image "nginx:this-tag-does-not-exist"
25s         Warning   Failed           pod/broken-image-pod   Failed to pull image "nginx:this-tag-does-not-exist": rpc error: code = NotFound desc = failed to pull and unpack image "docker.io/library/nginx:this-tag-does-not-exist": failed to resolve image: docker.io/library/nginx:this-tag-does-not-exist: not found
25s         Warning   Failed           pod/broken-image-pod   Error: ErrImagePull
12s         Normal    BackOff          pod/broken-image-pod   Back-off pulling image "nginx:this-tag-does-not-exist"
12s         Warning   Failed           pod/broken-image-pod   Error: ImagePullBackOff  

root@deep-cp-01:/home/goblin# kubectl delete pod broken-image-pod -n kdl-lab07
pod "broken-image-pod" deleted from kdl-lab07 namespace
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          3m44s
root@deep-cp-01:/home/goblin# kubectl delete pod nginx-pod -n kdl-lab07
pod "nginx-pod" deleted from kdl-lab07 namespace
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07
No resources found in kdl-lab07 namespace.
```

-----
**Lab 07.03 - Labels and selectors**  
```
root@deep-cp-01:/home/goblin# kubectl get namespace kdl-lab07
NAME        STATUS   AGE
kdl-lab07   Active   23h

root@deep-cp-01:/home/goblin# kubectl apply -f manifests/pods-labels.yaml
pod/labels-nginx-a created
pod/labels-nginx-b created
pod/labels-busybox-a created

root@deep-cp-01:/home/goblin# kubectl -n kdl-lab07 get po
NAME               READY   STATUS    RESTARTS   AGE
labels-busybox-a   1/1     Running   0          76s
labels-nginx-a     1/1     Running   0          76s
labels-nginx-b     1/1     Running   0          76s

root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 --show-labels
NAME               READY   STATUS    RESTARTS   AGE     LABELS
labels-busybox-a   1/1     Running   0          3m18s   app=busybox,environment=lab,kdl/component=labels,kdl/lab=07,tier=tools,track=stable
labels-nginx-a     1/1     Running   0          3m18s   app=nginx,environment=lab,kdl/component=labels,kdl/lab=07,tier=frontend,track=stable
labels-nginx-b     1/1     Running   0          3m18s   app=nginx,environment=lab,kdl/component=labels,kdl/lab=07,tier=frontend,track=canary

root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -L app,tier,track
NAME               READY   STATUS    RESTARTS   AGE     APP       TIER       TRACK
labels-busybox-a   1/1     Running   0          2m52s   busybox   tools      stable
labels-nginx-a     1/1     Running   0          2m52s   nginx     frontend   stable
labels-nginx-b     1/1     Running   0          2m52s   nginx     frontend   canary

root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l app=nginx
NAME             READY   STATUS    RESTARTS   AGE
labels-nginx-a   1/1     Running   0          50m
labels-nginx-b   1/1     Running   0          50m
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l app=busybox
NAME               READY   STATUS    RESTARTS   AGE
labels-busybox-a   1/1     Running   0          51m
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l track!=canary
NAME               READY   STATUS    RESTARTS   AGE
labels-busybox-a   1/1     Running   0          51m
labels-nginx-a     1/1     Running   0          51m
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l app=nginx,track=stable
NAME             READY   STATUS    RESTARTS   AGE
labels-nginx-a   1/1     Running   0          51m
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l 'track in (stable,canary)'
NAME               READY   STATUS    RESTARTS   AGE
labels-busybox-a   1/1     Running   0          51m
labels-nginx-a     1/1     Running   0          51m
labels-nginx-b     1/1     Running   0          51m
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l 'tier notin (tools)'
NAME             READY   STATUS    RESTARTS   AGE
labels-nginx-a   1/1     Running   0          52m
labels-nginx-b   1/1     Running   0          52m
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l track
NAME               READY   STATUS    RESTARTS   AGE
labels-busybox-a   1/1     Running   0          53m
labels-nginx-a     1/1     Running   0          53m
labels-nginx-b     1/1     Running   0          53m
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l '!debug'
NAME               READY   STATUS    RESTARTS   AGE
labels-busybox-a   1/1     Running   0          53m
labels-nginx-a     1/1     Running   0          53m
labels-nginx-b     1/1     Running   0          53m

root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l app=nginx -o name
pod/labels-nginx-a
pod/labels-nginx-b

root@deep-cp-01:/home/goblin# kubectl get pod labels-nginx-a -n kdl-lab07 -o jsonpath='{.metadata.labels}'; echo
{"app":"nginx","environment":"lab","kdl/component":"labels","kdl/lab":"07","tier":"frontend","track":"stable"}
root@deep-cp-01:/home/goblin# kubectl get pod labels-nginx-a -n kdl-lab07 -o jsonpath='{.metadata.labels.track}'; echo
stable

root@deep-cp-01:/home/goblin# kubectl label pod labels-nginx-a -n kdl-lab07 debug=true
pod/labels-nginx-a labeled
root@deep-cp-01:/home/goblin# kubectl get pod labels-nginx-a -n kdl-lab07 --show-labels
NAME             READY   STATUS    RESTARTS   AGE   LABELS
labels-nginx-a   1/1     Running   0          57m   app=nginx,debug=true,environment=lab,kdl/component=labels,kdl/lab=07,tier=frontend,track=stable
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l debug=true
NAME             READY   STATUS    RESTARTS   AGE
labels-nginx-a   1/1     Running   0          57m

root@deep-cp-01:/home/goblin# nano manifests/pods-labels.yaml
root@deep-cp-01:/home/goblin# kubectl diff -f manifests/pods-labels.yaml
root@deep-cp-01:/home/goblin# kubectl apply -f manifests/pods-labels.yaml
pod/labels-nginx-a configured
pod/labels-nginx-b unchanged
pod/labels-busybox-a unchanged

root@deep-cp-01:/home/goblin# kubectl label pod labels-nginx-b -n kdl-lab07 track=stable --overwrite
pod/labels-nginx-b labeled
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -L app,tier,track
NAME               READY   STATUS    RESTARTS      AGE   APP       TIER       TRACK
labels-busybox-a   1/1     Running   1 (13m ago)   74m   busybox   tools      stable
labels-nginx-a     1/1     Running   0             74m   nginx     frontend   stable
labels-nginx-b     1/1     Running   0             74m   nginx     frontend   stable
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l app=nginx,track=stable
NAME             READY   STATUS    RESTARTS   AGE
labels-nginx-a   1/1     Running   0          74m
labels-nginx-b   1/1     Running   0          74m
root@deep-cp-01:/home/goblin# kubectl label pod labels-nginx-b -n kdl-lab07 track=canary
error: 'track' already has a value (stable), and --overwrite is false

root@deep-cp-01:/home/goblin# nano manifests/pods-labels.yaml
root@deep-cp-01:/home/goblin# kubectl diff -f manifests/pods-labels.yaml
root@deep-cp-01:/home/goblin# kubectl apply -f manifests/pods-labels.yaml
pod/labels-nginx-a unchanged
pod/labels-nginx-b configured
pod/labels-busybox-a unchanged

root@deep-cp-01:/home/goblin# kubectl label pod labels-nginx-a -n kdl-lab07 debug-
pod/labels-nginx-a unlabeled
root@deep-cp-01:/home/goblin# kubectl get pod labels-nginx-a -n kdl-lab07 --show-labels
NAME             READY   STATUS    RESTARTS   AGE   LABELS
labels-nginx-a   1/1     Running   0          77m   app=nginx,environment=lab,kdl/component=labels,kdl/lab=07,tier=frontend,track=stable
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l debug=true
No resources found in kdl-lab07 namespace.
root@deep-cp-01:/home/goblin# nano manifests/pods-labels.yaml
root@deep-cp-01:/home/goblin# kubectl diff -f manifests/pods-labels.yaml
root@deep-cp-01:/home/goblin# kubectl apply -f manifests/pods-labels.yaml
pod/labels-nginx-a configured
pod/labels-nginx-b unchanged
pod/labels-busybox-a unchanged

root@deep-cp-01:/home/goblin# kubectl get pod labels-nginx-a -n kdl-lab07 -o jsonpath='{.metadata.annotations}' | jq ; echo
{
  "cni.projectcalico.org/containerID": "247d333f2a63108ff457dcc4c57a64b87850310761cfd5f9e8ab6b8699c99791",
  "cni.projectcalico.org/podIP": "192.168.19.140/32",
  "cni.projectcalico.org/podIPs": "192.168.19.140/32",
  "kdl/description": "First nginx Pod for labels and selectors practice",
  "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"Pod\",\"metadata\":{\"annotations\":{\"kdl/description\":\"First nginx Pod for labels and selectors practice\"},\"labels\":{\"app\":\"nginx\",\"environment\":\"lab\",\"kdl/component\":\"labels\",\"kdl/lab\":\"07\",\"tier\":\"frontend\",\"track\":\"stable\"},\"name\":\"labels-nginx-a\",\"namespace\":\"kdl-lab07\"},\"spec\":{\"containers\":[{\"image\":\"nginx:1.27.5\",\"name\":\"nginx\",\"ports\":[{\"containerPort\":80}]}]}}\n"
}


root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l 'kdl/description=First nginx Pod for labels and selectors practice'
Error from server (BadRequest): Unable to find "/v1, Resource=pods" that match label selector "kdl/description=First nginx Pod for labels and selectors practice", field selector "": found 'nginx', expected: ',' or 'end of string'

root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l app=redis
No resources found in kdl-lab07 namespace.

root@deep-cp-01:/home/goblin# kubectl get events -n kdl-lab07 --sort-by=.metadata.creationTimestamp
LAST SEEN   TYPE     REASON    OBJECT                 MESSAGE
32m         Normal   Created   pod/labels-busybox-a   Container created
32m         Normal   Started   pod/labels-busybox-a   Container started
32m         Normal   Pulled    pod/labels-busybox-a   Container image "busybox:1.37.0" already present on machine and can be accessed by the pod

root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=labels
NAME               READY   STATUS    RESTARTS      AGE
labels-busybox-a   1/1     Running   1 (33m ago)   93m
labels-nginx-a     1/1     Running   0             93m
labels-nginx-b     1/1     Running   0             93m
root@deep-cp-01:/home/goblin# kubectl delete pods -n kdl-lab07 -l kdl/component=labels
pod "labels-busybox-a" deleted from kdl-lab07 namespace
pod "labels-nginx-a" deleted from kdl-lab07 namespace
pod "labels-nginx-b" deleted from kdl-lab07 namespace
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07
No resources found in kdl-lab07 namespace.
```

-----
***Lab 07.04 - ReplicaSet***
```
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 --show-labels
No resources found in kdl-lab07 namespace.
root@deep-cp-01:/home/goblin# nano manifests/replicaset-nginx.yaml

root@deep-cp-01:/home/goblin# kubectl apply --dry-run=client -f manifests/replicaset-nginx.yaml
replicaset.apps/nginx-rs created (dry run)
root@deep-cp-01:/home/goblin# kubectl apply --dry-run=server -f manifests/replicaset-nginx.yaml
replicaset.apps/nginx-rs created (server dry run)
root@deep-cp-01:/home/goblin# kubectl apply -f manifests/replicaset-nginx.yaml
replicaset.apps/nginx-rs created
root@deep-cp-01:/home/goblin# kubectl get rs -n kdl-lab07
NAME       DESIRED   CURRENT   READY   AGE
nginx-rs   2         2         2       84s

root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=replicaset -o wide
NAME             READY   STATUS    RESTARTS   AGE     IP                NODE          NOMINATED NODE   READINESS GATES
nginx-rs-827hh   1/1     Running   0          2m53s   192.168.158.200   deep-wrk-01   <none>           <none>
nginx-rs-gnw6j   1/1     Running   0          2m53s   192.168.19.142    deep-wrk-02   <none>           <none>

root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=replicaset -o name
pod/nginx-rs-827hh
pod/nginx-rs-gnw6j

root@deep-cp-01:/home/goblin# kubectl get pod nginx-rs-gnw6j -n kdl-lab07 -o yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
...
  generation: 1
  labels:
    app: nginx
    app.kubernetes.io/name: nginx
    app.kubernetes.io/part-of: kubernetes-deep-lab
    kdl/component: replicaset
    kdl/lab: "07"
  name: nginx-rs-gnw6j
  namespace: kdl-lab07
  ownerReferences:
  - apiVersion: apps/v1
    blockOwnerDeletion: true
    controller: true
    kind: ReplicaSet
    name: nginx-rs
...
spec:
  containers:
  - image: nginx:1.27.5
...
status:
...
    lastState: {}
    name: nginx
    ready: true
    resources: {}
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2026-06-19T17:15:48Z"
    user:
      linux:
        gid: 0
        supplementalGroups:
        - 0
        uid: 0
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-dmwvk
      readOnly: true
      recursiveReadOnly: Disabled
  hostIP: 192.168.100.52
  hostIPs:
  - ip: 192.168.100.52
  observedGeneration: 1
  phase: Running
  podIP: 192.168.19.142
  podIPs:
  - ip: 192.168.19.142
  qosClass: BestEffort
  resources: {}
  startTime: "2026-06-19T17:15:47Z"

root@deep-cp-01:/home/goblin# kubectl describe rs nginx-rs -n kdl-lab07
Name:         nginx-rs
Namespace:    kdl-lab07
Selector:     app=nginx,kdl/component=replicaset
Labels:       app.kubernetes.io/name=nginx
              app.kubernetes.io/part-of=kubernetes-deep-lab
              kdl/component=replicaset
              kdl/lab=07
Annotations:  <none>
Replicas:     2 current / 2 desired
Pods Status:  2 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=nginx
           app.kubernetes.io/name=nginx
           app.kubernetes.io/part-of=kubernetes-deep-lab
           kdl/component=replicaset
           kdl/lab=07
  Containers:
   nginx:
    Image:         nginx:1.27.5
    Port:          80/TCP
    Host Port:     0/TCP
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Events:
  Type    Reason            Age   From                   Message
  ----    ------            ----  ----                   -------
  Normal  SuccessfulCreate  30m   replicaset-controller  Created pod: nginx-rs-gnw6j
  Normal  SuccessfulCreate  30m   replicaset-controller  Created pod: nginx-rs-827hh

root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=replicaset
NAME             READY   STATUS    RESTARTS   AGE
nginx-rs-827hh   1/1     Running   0          31m
nginx-rs-gnw6j   1/1     Running   0          31m
root@deep-cp-01:/home/goblin# kubectl delete pod nginx-rs-gnw6j -n kdl-lab07
pod "nginx-rs-gnw6j" deleted from kdl-lab07 namespace
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=replicaset
NAME             READY   STATUS    RESTARTS   AGE
nginx-rs-827hh   1/1     Running   0          32m
nginx-rs-qvdq6   1/1     Running   0          5s

root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=replicaset -o wide
NAME             READY   STATUS    RESTARTS   AGE   IP                NODE          NOMINATED NODE   READINESS GATES
nginx-rs-827hh   1/1     Running   0          32m   192.168.158.200   deep-wrk-01   <none>           <none>
nginx-rs-qvdq6   1/1     Running   0          53s   192.168.19.143    deep-wrk-02   <none>           <none>

root@deep-cp-01:/home/goblin# kubectl get rs nginx-rs -n kdl-lab07
NAME       DESIRED   CURRENT   READY   AGE
nginx-rs   2         2         2       33m

root@deep-cp-01:/home/goblin# kubectl scale rs nginx-rs -n kdl-lab07 --replicas=3
replicaset.apps/nginx-rs scaled
root@deep-cp-01:/home/goblin# kubectl get rs nginx-rs -n kdl-lab07
NAME       DESIRED   CURRENT   READY   AGE
nginx-rs   3         3         3       34m
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=replicaset
NAME             READY   STATUS    RESTARTS   AGE
nginx-rs-827hh   1/1     Running   0          35m
nginx-rs-qvdq6   1/1     Running   0          3m1s
nginx-rs-sx26m   1/1     Running   0          11s

root@deep-cp-01:/home/goblin# kubectl diff -f manifests/replicaset-nginx.yaml
diff -u -N /tmp/LIVE-1256121246/apps.v1.ReplicaSet.kdl-lab07.nginx-rs /tmp/MERGED-3674400640/apps.v1.ReplicaSet.kdl-lab07.nginx-rs
--- /tmp/LIVE-1256121246/apps.v1.ReplicaSet.kdl-lab07.nginx-rs  2026-06-19 17:51:17.884629302 +0000
+++ /tmp/MERGED-3674400640/apps.v1.ReplicaSet.kdl-lab07.nginx-rs        2026-06-19 17:51:17.884629302 +0000
@@ -5,7 +5,7 @@
     kubectl.kubernetes.io/last-applied-configuration: |
       {"apiVersion":"apps/v1","kind":"ReplicaSet","metadata":{"annotations":{},"labels":{"app.kubernetes.io/name":"nginx","app.kubernetes.io/part-of":"kubernetes-deep-lab","kdl/component":"replicaset","kdl/lab":"07"},"name":"nginx-rs","namespace":"kdl-lab07"},"spec":{"replicas":2,"selector":{"matchLabels":{"app":"nginx","kdl/component":"replicaset"}},"template":{"metadata":{"labels":{"app":"nginx","app.kubernetes.io/name":"nginx","app.kubernetes.io/part-of":"kubernetes-deep-lab","kdl/component":"replicaset","kdl/lab":"07"}},"spec":{"containers":[{"image":"nginx:1.27.5","name":"nginx","ports":[{"containerPort":80}]}]}}}}
   creationTimestamp: "2026-06-19T17:15:47Z"
-  generation: 2
+  generation: 3
   labels:
     app.kubernetes.io/name: nginx
     app.kubernetes.io/part-of: kubernetes-deep-lab
@@ -16,7 +16,7 @@
   resourceVersion: "176491"
   uid: 18d68665-245b-4d50-a37f-9ee609d809e2
 spec:
-  replicas: 3
+  replicas: 2
   selector:
     matchLabels:
       app: nginx

root@deep-cp-01:/home/goblin# nano manifests/replicaset-nginx.yaml
root@deep-cp-01:/home/goblin# kubectl diff -f manifests/replicaset-nginx.yaml
root@deep-cp-01:/home/goblin# kubectl apply -f manifests/replicaset-nginx.yaml
replicaset.apps/nginx-rs configured

root@deep-cp-01:/home/goblin# kubectl get rs nginx-rs -n kdl-lab07
NAME       DESIRED   CURRENT   READY   AGE
nginx-rs   3         3         3       37m
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=replicaset
NAME             READY   STATUS    RESTARTS   AGE
nginx-rs-827hh   1/1     Running   0          37m
nginx-rs-qvdq6   1/1     Running   0          5m31s
nginx-rs-sx26m   1/1     Running   0          2m41s

root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=replicaset --show-labels
NAME             READY   STATUS    RESTARTS   AGE     LABELS
nginx-rs-827hh   1/1     Running   0          38m     app.kubernetes.io/name=nginx,app.kubernetes.io/part-of=kubernetes-deep-lab,app=nginx,kdl/component=replicaset,kdl/lab=07
nginx-rs-qvdq6   1/1     Running   0          6m17s   app.kubernetes.io/name=nginx,app.kubernetes.io/part-of=kubernetes-deep-lab,app=nginx,kdl/component=replicaset,kdl/lab=07
nginx-rs-sx26m   1/1     Running   0          3m27s   app.kubernetes.io/name=nginx,app.kubernetes.io/part-of=kubernetes-deep-lab,app=nginx,kdl/component=replicaset,kdl/lab=07
root@deep-cp-01:/home/goblin# kubectl label pod nginx-rs-sx26m -n kdl-lab07 kdl/component=orphan-test --overwrite
pod/nginx-rs-sx26m labeled
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 --show-labels
NAME             READY   STATUS    RESTARTS   AGE     LABELS
nginx-rs-827hh   1/1     Running   0          38m     app.kubernetes.io/name=nginx,app.kubernetes.io/part-of=kubernetes-deep-lab,app=nginx,kdl/component=replicaset,kdl/lab=07
nginx-rs-h6dww   1/1     Running   0          11s     app.kubernetes.io/name=nginx,app.kubernetes.io/part-of=kubernetes-deep-lab,app=nginx,kdl/component=replicaset,kdl/lab=07
nginx-rs-qvdq6   1/1     Running   0          6m50s   app.kubernetes.io/name=nginx,app.kubernetes.io/part-of=kubernetes-deep-lab,app=nginx,kdl/component=replicaset,kdl/lab=07
nginx-rs-sx26m   1/1     Running   0          4m      app.kubernetes.io/name=nginx,app.kubernetes.io/part-of=kubernetes-deep-lab,app=nginx,kdl/component=orphan-test,kdl/lab=07

root@deep-cp-01:/home/goblin# kubectl get rs nginx-rs -n kdl-lab07
NAME       DESIRED   CURRENT   READY   AGE
nginx-rs   3         3         3       39m
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=replicaset
NAME             READY   STATUS    RESTARTS   AGE
nginx-rs-827hh   1/1     Running   0          39m
nginx-rs-h6dww   1/1     Running   0          71s
nginx-rs-qvdq6   1/1     Running   0          7m50s
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=orphan-test
NAME             READY   STATUS    RESTARTS   AGE
nginx-rs-sx26m   1/1     Running   0          5m8s
root@deep-cp-01:/home/goblin# kubectl delete pod -n kdl-lab07 -l kdl/component=orphan-test
pod "nginx-rs-sx26m" deleted from kdl-lab07 namespace
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 --show-labels
NAME             READY   STATUS    RESTARTS   AGE     LABELS
nginx-rs-827hh   1/1     Running   0          40m     app.kubernetes.io/name=nginx,app.kubernetes.io/part-of=kubernetes-deep-lab,app=nginx,kdl/component=replicaset,kdl/lab=07
nginx-rs-h6dww   1/1     Running   0          113s    app.kubernetes.io/name=nginx,app.kubernetes.io/part-of=kubernetes-deep-lab,app=nginx,kdl/component=replicaset,kdl/lab=07
nginx-rs-qvdq6   1/1     Running   0          8m32s   app.kubernetes.io/name=nginx,app.kubernetes.io/part-of=kubernetes-deep-lab,app=nginx,kdl/component=replicaset,kdl/lab=07

root@deep-cp-01:/home/goblin# kubectl get rs nginx-rs -n kdl-lab07
NAME       DESIRED   CURRENT   READY   AGE
nginx-rs   3         3         3       41m
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=replicaset
NAME             READY   STATUS    RESTARTS   AGE
nginx-rs-827hh   1/1     Running   0          41m
nginx-rs-h6dww   1/1     Running   0          3m15s
nginx-rs-qvdq6   1/1     Running   0          9m54s
root@deep-cp-01:/home/goblin# kubectl delete rs nginx-rs -n kdl-lab07
replicaset.apps "nginx-rs" deleted from kdl-lab07 namespace
root@deep-cp-01:/home/goblin# kubectl get rs -n kdl-lab07
No resources found in kdl-lab07 namespace.
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=replicaset
No resources found in kdl-lab07 namespace.
```

### Ключевые выводы команд

Ключевые выводы команд разных блоков буду разделять горизонтальными линиями

-----
**Lab 07.01 - Namespace**   
```
root@deep-cp-01:/home/goblin# kubectl describe ns kdl-lab07
Name:         kdl-lab07
Labels:       app.kubernetes.io/name=kdl-lab07
              app.kubernetes.io/part-of=kubernetes-deep-lab
              kdl/lab=07
              kubernetes.io/metadata.name=kdl-lab07
Annotations:  kdl/description: Kubernetes Deep Lab 07 namespace
Status:       Active

No resource quota.

No LimitRange resource.
```

-----
**Lab 07.02 - Pod** 
```
root@deep-cp-01:/home/goblin# kubectl -n kdl-lab07 get pods -o wide
NAME        READY   STATUS    RESTARTS   AGE   IP               NODE          NOMINATED NODE   READINESS GATES
nginx-pod   1/1     Running   0          26m   192.168.19.136   deep-wrk-02   <none>           <none>

root@deep-cp-01:/home/goblin# kubectl get pod nginx-pod -n kdl-lab07
NAME        READY   STATUS    RESTARTS      AGE
nginx-pod   1/1     Running   1 (56m ago)   19h
root@deep-cp-01:/home/goblin# kubectl delete pod nginx-pod -n kdl-lab07
pod "nginx-pod" deleted from kdl-lab07 namespace
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07
No resources found in kdl-lab07 namespace.
```

-----
**Lab 07.03 - Labels and selectors**  
```
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=labels
NAME               READY   STATUS    RESTARTS      AGE
labels-busybox-a   1/1     Running   1 (33m ago)   93m
labels-nginx-a     1/1     Running   0             93m
labels-nginx-b     1/1     Running   0             93m
root@deep-cp-01:/home/goblin# kubectl delete pods -n kdl-lab07 -l kdl/component=labels
pod "labels-busybox-a" deleted from kdl-lab07 namespace
pod "labels-nginx-a" deleted from kdl-lab07 namespace
pod "labels-nginx-b" deleted from kdl-lab07 namespace
```

-----
***Lab 07.04 - ReplicaSet***
```
root@deep-cp-01:/home/goblin# kubectl apply -f manifests/replicaset-nginx.yaml
replicaset.apps/nginx-rs created
root@deep-cp-01:/home/goblin# kubectl get rs -n kdl-lab07
NAME       DESIRED   CURRENT   READY   AGE
nginx-rs   2         2         2       84s

root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=replicaset
NAME             READY   STATUS    RESTARTS   AGE
nginx-rs-827hh   1/1     Running   0          31m
nginx-rs-gnw6j   1/1     Running   0          31m
root@deep-cp-01:/home/goblin# kubectl delete pod nginx-rs-gnw6j -n kdl-lab07
pod "nginx-rs-gnw6j" deleted from kdl-lab07 namespace
root@deep-cp-01:/home/goblin# kubectl get pods -n kdl-lab07 -l kdl/component=replicaset
NAME             READY   STATUS    RESTARTS   AGE
nginx-rs-827hh   1/1     Running   0          32m
nginx-rs-qvdq6   1/1     Running   0          5s



```

### Ошибки и диагностика

| Симптом | Слой | Что проверил | Решение |
|---|---|---|---|
|  |  |  |  |

### Что стало понятнее

- Использование `kubectl diff`
- Использование `kubectl explain` 
- Использование `kubectl get events`

### Вопросы

- Файлик 02-pod.md В первой инженерной заметке стоило бы убрать вариант с одним контейнером в поде т.к. помимо контейнера с рабочей нагрузкой там есть ещё sandbox, который держит ресурсы ip, ns
- 
- 

### Выводы  

-----
**Lab 07.01 - Namespace**   
1. Что такое Namespace в Kubernetes?
  * Namespace - очередной слой абстракции, позволяющий логически разделять нагрузку и контролировать доступ
2. Namespace создает отдельный cluster или логическую область внутри cluster?
  * Логическую область
3. Почему Namespace сам по себе не является полноценной границей безопасности?
  * Это просто логическое разделение, для разграничения прав нужно использовать rbac и networkPolicy
4. Какие объекты являются namespaced?
  * Pod
  * Service
  * Deployment
  * ReplicaSet
  * ConfigMap
  * Secret
  * ServiceAccount
  * endpoints
  * events
  * ingress
5. Какие объекты являются cluster-wide?
  * Node
  * Namespace
  * PersistentVolume
  * StorageClass
  * ClusterRole
  * ClusterRoleBinding
  * CustomResourceDefinition
  * tiers
  * apiservices
6. Почему у Namespace нет поля metadata.namespace?
  * ns кластерный объект, он не может сожержать другие ns
7. Чем labels отличаются от annotations?
  * По labels производится выборка, аннотации - дополнительная информация передаваетмая для уточнения настроек, к примеру
8. Почему изменение через kubectl label нужно перенести обратно в manifest?
  * Для соблюдения идеологии gitOps - что бы манифесты описывали актуальное состояние кластера
9. Что означает status.phase: Active?
  * Текущее состояние - ns активен и жив
10. Почему удаление namespace считается HIGH-risk операцией?
  * Потому, что удаляться все объекты внутри ns



-----
**Lab 07.02 - Pod**  
Возникла странная проблема со скачиванием образа nginx, принудительное скачивание crictl прошло со второй попытки, минут через 20 под поднялся самостоятельно.

1. Что такое Pod?
  * Квант kubernetes.
2. Почему Kubernetes запускает Pod, а не container напрямую?
  * Квантом (т.е. минимальной единицей) является под, который содержит некоторое количество контейнеров.
3. Что находится в metadata Pod?
  * Метаданные, ns, labels, annotation и подобное.
4. Что находится в spec Pod?
  * спецификация пода - какой образ, какие политики, пробы, csi и подобное, то как мыхотим видеть этот под.
5. Что находится в status Pod?
  * Текущее состояние пода
6. Чем status.phase отличается от containerStatuses?
  * status.phase отражает текущую фазу состояния пода, containerStatuses массив описывающий состояние каждого контейнера в поде.
7. Что показывает kubectl get pod -o wide?
  * Расширенную информацию о поде.
8. Что показывает kubectl describe pod?
  * расширенная информация по поду.
9. Почему logs nginx могут быть пустыми после запуска?
  * нет логов, которые нужно отобразить.
10. Что делает kubectl exec?
  * выполняет команду внутри пода
11. Почему команда внутри kubectl exec может не найтись?
  * её там может просто не быть, более того там и шела может не быть.
12. Почему Pod IP нельзя считать стабильным?
  * пр пересоздании пода с большой вероятностью IP поменяется.
13. Что произойдет, если удалить одиночный Pod без controller?
  * Он умрёт и не возрадится автоматически.
14. Почему для приложений обычно используют Deployment, а не Pod напрямую?
  * Что бы поды пересоздавались после смерти.
15. Что означает ImagePullBackOff?
  * Это означает, что указанный образ не был скачан, соответственно запустить кониейнер нет возможности.

Под минимальный квант kubernetes, его рождение и смерть - нормальный жизненный цикл, для поддержания сего цикла нужно использовать контроллеры replicaSet, stateFullSet или daemonSet.  

-----
**Lab 07.03 - Labels and selectors**  
1. Что такое label?
  * метка, которой присваевается определённое значение
2. Что такое selector?
  * условие выбора по определённым меткам с их определённым значением.
3. Где в manifest находятся labels?
  * в разделе метаданных
4. Чем labels отличаются от annotations?
  * Labels  участвуют в процессе выбора, аннотации - нет.
5. Почему selector не выбирает объекты по имени?
  * потому что он выбирает их по меткам, а имя понятие эфимерное и будеть зависеть от контроллера, который стартует поды.
6. Что означает selector app=nginx,track=stable?
  * будут выбраны поды с метками app=nginx и track=stable.
7. Что означает selector track in (stable,canary)?
  * Метка track может иметь значения stable или canary.
8. Почему selector track!=canary может выбрать больше объектов, чем ожидалось?
  * выберет все поды, у которых метка track не равна canary, даже если этой метки вообще нет на поде.
9. Зачем нужен --show-labels?
  * Что бы посмотреть метки
10. Что делает команда kubectl get pods -L app,tier,track?
  * Выводит указанные метки в табличном формате.
11. Почему kubectl label может создать расхождение между live-состоянием и manifest?
  * созданная руками метка сама не появится в манифесте.
12. Зачем нужен --overwrite при изменении существующего label?
  * Что бы перезаписать существующюю метку
13. Как удалить label через kubectl label?
  * kubectl label pod <pod name> -n <ns> <имя метки>-
  но, лучше через манифест.
14. Почему annotations не подходят для selectors?
  * Выборка происходит по меткам, аннотации не участвуют в процессе выбора подов.
15. Как неправильный selector может повлиять на Service или ReplicaSet?
  * Service или ReplicaSet не будут видеть поды, даже если они существуют, в случае с ReplicaSet будут созданы другие поды, с подходящими метками, Service просто не создаст endPoints, соответственно трафик до подов не дойдёт.

-----
***Lab 07.04 - ReplicaSet***
1. Что такое ReplicaSet?
  * контроллер kubernetes.
2. Чем ReplicaSet отличается от одиночного Pod?
  * ReplicaSet поддерживает заданное количество подов.
3. Какие три ключевые части есть в spec ReplicaSet?
  * spec.replicas
  * spec.selector 
  * spec.template
4. Что означает spec.replicas?
  * Количество реплик пода.
5. Что означает spec.selector?
  * метки для опознания пода своим.
6. Что означает spec.template?
  * шаблон создания пода.
7. Почему selector должен совпадать с labels в Pod template?
  * Иначе поды не будут считаться принадлежащими rs
8. Как ReplicaSet понимает, какие Pods относятся к нему?
  * По соответствию меток у подов с описанными в .template
9. Что произойдет, если удалить Pod, которым управляет ReplicaSet?
  * Создастца ещё один под
10. Что произойдет, если изменить label у Pod так, что он перестанет подходить под selector?
  * Создастца ещё один под с нудными метками.
11. Почему изменение через kubectl scale нужно переносить обратно в manifest?
  * Для соответствия состояния в кластере и в манифесте.
12. Что показывает kubectl get rs?
  * активные rs и состояние подов запущенных ими.
13. Где посмотреть ownerReferences у Pod?
  * kubectl get pod <pod-name> -o yaml
14. Почему ReplicaSet обычно не создают вручную?
  * ReplicaSet умеет только поддерживать заданное количество подов, для нормальной эксплуатации этого не достаточно.
15. Какая связь между Deployment, ReplicaSet и Pod?
  * Deployment -> ReplicaSet -> Pod


### Статус

in-progress

