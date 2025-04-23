# Working with deployment-with-probe

Example: check in depth on describing pod section cmd

```

➜  probes git:(main) ✗ vim namespace.yml
➜  probes git:(main) ✗ kubectl apply -f namespace.yml 
namespace/nginx-ns created
➜  probes git:(main) ✗ kubectl apply -f deployment-with-probe.yml 
deployment.apps/nginx-web created
➜  probes git:(main) ✗ kubectl get pods -n nginx-ns
NAME                         READY   STATUS    RESTARTS   AGE
nginx-web-56b4fd6bd6-6pc8n   0/1     Running   0          10s
➜  probes git:(main) ✗ kubectl get pods -n nginx-ns
NAME                         READY   STATUS    RESTARTS   AGE
nginx-web-56b4fd6bd6-6pc8n   0/1     Running   0          13s
➜  probes git:(main) ✗ kubectl describe pod nginx-web-56b4fd6bd6-6pc8n -n nginx-ns
Name:             nginx-web-56b4fd6bd6-6pc8n
Namespace:        nginx-ns
Priority:         0
Service Account:  default
Node:             ignius-k8s-cluster-worker3/172.18.0.5
Start Time:       Wed, 23 Apr 2025 23:44:17 +0545
Labels:           app=nginx-web
                  pod-template-hash=56b4fd6bd6
Annotations:      <none>
Status:           Running
IP:               10.244.3.10
IPs:
  IP:           10.244.3.10
Controlled By:  ReplicaSet/nginx-web-56b4fd6bd6
Containers:
  nginx-web:
    Container ID:   containerd://57458d60fd858b8edd4c07c8a8d40e9e8c66cc0df1a7ce940cf3199a6b62c8a7
    Image:          nginx:latest
    Image ID:       docker.io/library/nginx@sha256:5ed8fcc66f4ed123c1b2560ed708dc148755b6e4cbd8b943fab094f2c6bfa91e
    Port:           3000/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Wed, 23 Apr 2025 23:44:49 +0545
    Last State:     Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Wed, 23 Apr 2025 23:44:20 +0545
      Finished:     Wed, 23 Apr 2025 23:44:47 +0545
    Ready:          False
    Restart Count:  1
    Liveness:       http-get http://:8000/ delay=0s timeout=1s period=10s #success=1 #failure=3
    Readiness:      http-get http://:8000/ delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-c9k8p (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  kube-api-access-c9k8p:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  40s                default-scheduler  Successfully assigned nginx-ns/nginx-web-56b4fd6bd6-6pc8n to ignius-k8s-cluster-worker3
  Normal   Pulled     38s                kubelet            Successfully pulled image "nginx:latest" in 2.096s (2.096s including waiting). Image size: 68844367 bytes.
  Normal   Pulling    10s (x2 over 40s)  kubelet            Pulling image "nginx:latest"
  Warning  Unhealthy  10s (x3 over 30s)  kubelet            Liveness probe failed: Get "http://10.244.3.10:8000/": dial tcp 10.244.3.10:8000: connect: connection refused
  Normal   Killing    10s                kubelet            Container nginx-web failed liveness probe, will be restarted
  Normal   Created    8s (x2 over 38s)   kubelet            Created container: nginx-web
  Normal   Started    8s (x2 over 37s)   kubelet            Started container nginx-web
  Normal   Pulled     8s                 kubelet            Successfully pulled image "nginx:latest" in 1.988s (1.988s including waiting). Image size: 68844367 bytes.
  Warning  Unhealthy  5s (x9 over 37s)   kubelet            Readiness probe failed: Get "http://10.244.3.10:8000/": dial tcp 10.244.3.10:8000: connect: connection refused
➜  probes git:(main) ✗ 


```
