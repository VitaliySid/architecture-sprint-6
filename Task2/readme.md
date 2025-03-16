
### Включение дашборда:  
```sh
C:\Windows\system32>minikube start --driver=docker
* minikube v1.35.0 на Microsoft Windows 10 Pro 10.0.19045.5608 Build 19045.5608
* Используется драйвер docker на основе конфига пользователя
* Using Docker Desktop driver with root privileges
* Starting "minikube" primary control-plane node in "minikube" cluster
* Pulling base image v0.0.46 ...
    > gcr.io/k8s-minikube/kicbase...:  500.31 MiB / 500.31 MiB  100.00% 10.48 M
* Creating docker container (CPUs=2, Memory=10100MB) ...
! Failing to connect to https://registry.k8s.io/ from inside the minikube container
* To pull new external images, you may need to configure a proxy: https://minikube.sigs.k8s.io/docs/reference/networking/proxy/
* Подготавливается Kubernetes v1.32.0 на Docker 27.4.1 ...
  - Generating certificates and keys ...
  - Booting up control plane ...
  - Configuring RBAC rules ...
* Configuring bridge CNI (Container Networking Interface) ...
* Компоненты Kubernetes проверяются ...
  - Используется образ gcr.io/k8s-minikube/storage-provisioner:v5
* Включенные дополнения: storage-provisioner, default-storageclass

! C:\Program Files\Docker\Docker\resources\bin\kubectl.exe is version 1.25.4, which may have incompatibilities with Kubernetes 1.32.0.
  - Want kubectl v1.32.0? Try 'minikube kubectl -- get pods -A'
* Готово! kubectl настроен для использования кластера "minikube" и "default" пространства имён по умолчанию

C:\Windows\system32>minikube addons enable metrics-server
* metrics-server is an addon maintained by Kubernetes. For any concerns contact minikube on GitHub.
You can view the list of minikube maintainers at: https://github.com/kubernetes/minikube/blob/master/OWNERS
  - Используется образ registry.k8s.io/metrics-server/metrics-server:v0.7.2
* The 'metrics-server' addon is enabled

C:\Windows\system32>minikube config set driver docker
! These changes will take effect upon a minikube delete and then a minikube start

C:\Windows\system32>minikube service scaletestapp-srv --url
http://127.0.0.1:5379
! Because you are using a Docker driver on windows, the terminal needs to be open to run it.
```

### Разворачивание приложения:

``` sh
PS D:\projects\architecture-sprint-6\Task2\memory-scale> minikube kubectl create namespace task2
namespace/task2 created
PS D:\projects\architecture-sprint-6\Task2\memory-scale> minikube kubectl -- apply -f .\deployment.yaml -n task2
deployment.apps/scaletestapp created
PS D:\projects\architecture-sprint-6\Task2\memory-scale> minikube kubectl -- apply -f .\service.yaml -n task2   
service/scaletestapp-srv created
PS D:\projects\architecture-sprint-6\Task2\memory-scale> minikube kubectl -- apply -f .\hpa.yaml -n task2    
horizontalpodautoscaler.autoscaling/scaletestapp-hpa created
PS D:\projects\architecture-sprint-6\Task2\memory-scale> minikube kubectl -- get pods -n task2           
NAME                            READY   STATUS    RESTARTS   AGE
scaletestapp-58958494c8-vcpnm   1/1     Running   0          11m
PS D:\projects\architecture-sprint-6\Task2\memory-scale> minikube kubectl -- get all -n task2      
NAME                                READY   STATUS    RESTARTS   AGE
pod/scaletestapp-58958494c8-vcpnm   1/1     Running   0          13m

NAME                       TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE  
service/scaletestapp-srv   NodePort   10.104.171.154   <none>        80:30149/TCP   3m37s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/scaletestapp   1/1     1            1           13m

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/scaletestapp-58958494c8   1         1         1       13m

NAME                                                   REFERENCE                 TARGETS          MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/scaletestapp-hpa   Deployment/scaletestapp   memory: 6%/80%   1         10        1          3m2s

scaletestapp-hpa   Deployment/scaletestapp   memory: 83%/80%   1         10        1          5m19s
PS D:\projects\architecture-sprint-6\Task2\memory-scale> minikube kubectl -- get hpa 
NAME               REFERENCE                 TARGETS           MINPODS   MAXPODS   REPLICAS   AGE
scaletestapp-hpa   Deployment/scaletestapp   memory: 94%/80%   1         10        2          6m51s
```

### Запуск Locust

![](/pics/locust_requests.png)

```sh

C:\Windows\system32>locust -f D:\projects\architecture-sprint-6\Task2\locustfile.py
[2025-03-16 12:14:29,890] MSI/INFO/locust.main: Starting Locust 2.33.2
[2025-03-16 12:14:29,891] MSI/INFO/locust.main: Starting web interface at http://localhost:8089, press enter to open your default browser.
[2025-03-16 12:14:37,192] MSI/INFO/locust.runners: Ramping to 1000 users at a rate of 100.00 per second
[2025-03-16 12:14:46,270] MSI/INFO/locust.runners: All users spawned: {"WebsiteUser": 1000} (1000 total users)
[2025-03-16 12:15:55,149] MSI/INFO/locust.runners: Ramping to 2000 users at a rate of 100.00 per second
[2025-03-16 12:16:04,208] MSI/INFO/locust.runners: All users spawned: {"WebsiteUser": 2000} (2000 total users)
[2025-03-16 12:18:11,950] MSI/WARNING/locust.runners: Your selected spawn rate is very high (>100), and this is known to sometimes cause issues. Do you really need to ramp up that fast?
[2025-03-16 12:18:11,951] MSI/INFO/locust.runners: Ramping to 2000 users at a rate of 400.00 per second
[2025-03-16 12:18:11,953] MSI/INFO/locust.runners: All users spawned: {"WebsiteUser": 2000} (2000 total users)
[2025-03-16 12:19:16,353] MSI/WARNING/locust.runners: Your selected spawn rate is very high (>100), and this is known to sometimes cause issues. Do you really need to ramp up that fast?
[2025-03-16 12:19:16,354] MSI/INFO/locust.runners: Ramping to 2000 users at a rate of 500.00 per second
[2025-03-16 12:19:16,356] MSI/INFO/locust.runners: All users spawned: {"WebsiteUser": 2000} (2000 total users)
[2025-03-16 12:28:26,405] MSI/WARNING/locust.runners: Your selected spawn rate is very high (>100), and this is known to sometimes cause issues. Do you really need to ramp up that fast?
[2025-03-16 12:28:26,406] MSI/INFO/locust.runners: Ramping to 1 users at a rate of 500.00 per second
[2025-03-16 12:28:26,407] MSI/INFO/locust.runners: All users spawned: {"WebsiteUser": 1} (1 total users)
[2025-03-16 12:39:26,759] MSI/WARNING/locust.runners: Your selected spawn rate is very high (>100), and this is known to sometimes cause issues. Do you really need to ramp up that fast?
```

### Демонстрация Дашборда

![](/pics/task2_default.png)

![](/pics/task2_scaling.png)


<details>
<summary>Вторая часть (частично)</summary>

Установка Prometeus
```sh

C:\Windows\system32>choco install kubernetes-helm
...
Chocolatey installed 1/1 packages.
 See the log for details (C:\ProgramData\chocolatey\logs\chocolatey.log).

C:\Windows\system32>helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
"prometheus-community" has been added to your repositories

C:\Windows\system32>helm install prometheus prometheus-community/kube-prometheus-stack
NAME: prometheus
LAST DEPLOYED: Sun Mar 16 20:03:14 2025
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
kube-prometheus-stack has been installed. Check its status by running:
  kubectl --namespace default get pods -l "release=prometheus"

Get Grafana 'admin' user password by running:

  kubectl --namespace default get secrets prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo

Access Grafana local instance:

  export POD_NAME=$(kubectl --namespace default get pod -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=prometheus" -oname)
  kubectl --namespace default port-forward $POD_NAME 3000

C:\Windows\system32>kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-np
service/prometheus-server-np exposed

C:\Windows\system32>kubectl get pods -l app.kubernetes.io/instance=prometheus
NAME                                                 READY   STATUS    RESTARTS   AGE
prometheus-alertmanager-0                            1/1     Running   0          78s
prometheus-kube-state-metrics-5bd466f7f6-2zpvr       1/1     Running   0          78s
prometheus-prometheus-node-exporter-vx9ch            1/1     Running   0          78s
prometheus-prometheus-pushgateway-544579d549-8vb8d   1/1     Running   0          78s
prometheus-server-596945876b-sd8zd                   1/2     Running   0          78s

C:\Windows\system32>helm upgrade -f D:\projects\architecture-sprint-6\Task2\rps-scale\values.yaml prometheus prometheus-community/kube-prometheus-stack
Release "prometheus" has been upgraded. Happy Helming!
NAME: prometheus
LAST DEPLOYED: Sun Mar 16 20:18:13 2025
NAMESPACE: default
STATUS: deployed
REVISION: 2
NOTES:
kube-prometheus-stack has been installed. Check its status by running:
  kubectl --namespace default get pods -l "release=prometheus"

Get Grafana 'admin' user password by running:

  kubectl --namespace default get secrets prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo

Access Grafana local instance:

  export POD_NAME=$(kubectl --namespace default get pod -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=prometheus" -oname)
  kubectl --namespace default port-forward $POD_NAME 3000


C:\Windows\system32>minikube service prometheus-server-np
|-----------|----------------------|-------------|---------------------------|
| NAMESPACE |         NAME         | TARGET PORT |            URL            |
|-----------|----------------------|-------------|---------------------------|
| default   | prometheus-server-np |          80 | http://192.168.49.2:30664 |
|-----------|----------------------|-------------|---------------------------|
* Starting tunnel for service prometheus-server-np.
|-----------|----------------------|-------------|------------------------|
| NAMESPACE |         NAME         | TARGET PORT |          URL           |
|-----------|----------------------|-------------|------------------------|
| default   | prometheus-server-np |             | http://127.0.0.1:53590 |
|-----------|----------------------|-------------|------------------------|

```

Разворачивание приложения:

```sh

PS D:\projects\architecture-sprint-6\Task2\rps-scale> kubectl apply -f .\deployment.yaml
deployment.apps/scaletest-deploy created
PS D:\projects\architecture-sprint-6\Task2\rps-scale> kubectl apply -f .\service.yaml   
service/scaletestapp-srv created
PS D:\projects\architecture-sprint-6\Task2\rps-scale> kubectl apply -f .\hpa.yaml    
horizontalpodautoscaler.autoscaling/scaletestapp-hpa created

```

</details>
