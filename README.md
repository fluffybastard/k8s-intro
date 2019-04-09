# Introduction to Kubernetes

### Requirements
* Docker
* Virtual box ( or similar hypervisor )
* Minikube

### Start the local cluster
`$ minikube start` : this will take some time.

### Check that the service is actually running
`$ minikube version` : you should see something similar to this : `minikube version: v0.35.0`.

### Run a single pod
Run a single `pod` using `nginx-single-pod.yml`.
`$ kubectl create -f resources/nginx-single-pod.yml`

List running pods.
`$ kubectl get pods -o wide`
```
NAME         READY     STATUS    RESTARTS   AGE       IP           NODE
web-server   1/1       Running   0          46s       172.17.0.2   minikube
```

Additional information can be displayed with `describe` command.
`$ kubectl describe pods web-server`

Sample output
```
Name:               web-server
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               minikube/10.0.2.15
Start Time:         Tue, 09 Apr 2019 14:45:04 +0300
Labels:             name=web-server
Annotations:        <none>
Status:             Running
IP:                 172.17.0.2
Containers:
  nginx:
    Container ID:   docker://be103db517b33228f543983728d642ccb9c5c671ce5fca66dcced6d6f2114a31
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:c8a861b8a1eeef6d48955a6c6d5dff8e2580f13ff4d0f549e082e7c82a8617a2
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 09 Apr 2019 14:45:09 +0300
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     500m
      memory:  128Mi
    Requests:
      cpu:        500m
      memory:     128Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-jw72v (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-jw72v:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-jw72v
    Optional:    false
QoS Class:       Guaranteed
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  2m    default-scheduler  Successfully assigned default/web-server to minikube
  Normal  Pulling    1m    kubelet, minikube  pulling image "nginx"
  Normal  Pulled     1m    kubelet, minikube  Successfully pulled image "nginx"
  Normal  Created    1m    kubelet, minikube  Created container
  Normal  Started    1m    kubelet, minikube  Started container
```

To delete a pod run.
`$ kubectl delete pods web-server` : you should see something similar to this : `pod "web-server" deleted`

Check the status by listing all pods.
`$ kubectl get pods -o wide`

If none are running you will see : `No resources found.`

### Run a multiple pods setup
Create the resource.
`$ kubectl create -f resources/nginx-deployment.yml`

List all running pods.
`$ kubectl get pods -o wide`
```
NAME                          READY     STATUS    RESTARTS   AGE       IP           NODE
web-server-7cb4d54797-5z5kn   1/1       Running   0          7s        172.17.0.2   minikube
web-server-7cb4d54797-f8wzr   1/1       Running   0          7s        172.17.0.3   minikube
```

List deployments ( what we just created ).
`$ kubectl get deploy`
```
NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
web-server   2         2         2            2           2m
```

The output shows the current and desired state of our deployment. Based on the `resources/nginx-deployment.yml`
we requested 2 replicas of the same pod, which k8s created.

To delete the current deployment, print the resource and choose the correct one.
`$ kubectl get deploy`
```
NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
web-server   2         2         2            2           12m
```
Once you identified the deployment, delete it.

`$ kubectl delete deploy web-server`
```
deployment.extensions "web-server" deleted
```

### OBS
If you don't create a `service` type resource or explicitly expose a pod, all resources are only available
inside the cluster.
To manually expose a pod ( for testing purposes ), you can run this command.
`$ kubectl port-forward -n default POD_NAME HOST_PORT:CONTAINER_PORT`

### Example
* Create the single pod resource from above

`$ kubectl create -f resources/nginx-single-pod.yml`

* Forward the host port to container.

`$ kubectl port-forward -n default web-server 8081:80`

* Test it from your browser.

`http://localhost:8081`