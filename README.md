# rdcm_platform

# 1) Установлен kubectl:

`https://kubernetes.io/docs/tasks/tools/install-kubectl/`

`curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.0/bin/linux/amd64/kubectl`

`chmod +x ./kubectl`

`sudo mv ./kubectl /usr/local/bin/kubectl`

`kubectl version`

# 2) Установлен minikube:

`https://kubernetes.io/docs/tasks/tools/install-minikube/`

`curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube`

`sudo install minikube /usr/local/bin`

`minikube start`

`minikube delete`

# 3) Проверены конфиги kubectl:

`kubectl config view`

`kubectl cluster-info`

```
Kubernetes master is running at https://192.168.99.100:8443`
KubeDNS is running at https://192.168.99.100:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

# 4) Установлен kubernetes dashboard:

`https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/`

`kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta1/aio/deploy/recommended.yaml`

`kubectl proxy`

`http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/`

# 4.1) Добавлена авторизация в kubernetes dashboard:
`https://github.com/kubernetes/dashboard/wiki/Creating-sample-user`

`kubectl apply -f dashboard-adminuser.yaml`

`kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')`

```
Data
====
ca.crt:     1066 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLW5xNzk4Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIxNWE3MzMxYy04MDc1LTRlMTUtYTA5ZC1jNDAyZmRhYWIxOTUiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06YWRtaW4tdXNlciJ9.OHTQmphm3KLCJgs7B5a__U2wFzzpIHuKssoV_XakWVycFuwfBZiy1GF0z-tZb4LQvo7D9qxp9mIYjnONluhLLIJpTMV47a2NsOEPeDJH4SNM_wYA3H_HpKYaR7h6S0ZQOZ6oEJ2f9Xb8tFXmWBp5W2nzQZxa6YT5M94XaTOn-14Tz2Oi8bi3FXehyUsmb93fMll2EXjVxxsPUVipgKEH1KWnjEvONgIey9RYhK-jkdQ8-lLnkX-9REHjREnQ1mN_Lt9cpmnPwBOdyCM_EfLjZD3F0BM7N4xPU0bFrePSF61QuhDIlidqNydQ9PeN10DIMyr_CztlPcx8yr-AXkgkiA
```

# 5) Установлен клиент k9s:

`k9s - https://k9ss.io/`

`snap install k9s`

# 6) Проведены failover тесты:

`connect to minikube over ssh `

`minikube ssh`

`docker ps` - список контейнеров кластера запущенных на виртуалке

`kubectl get pods -n kube-system` - тоже самое, только через kubectl

`kubectl delete pod --all -n kube-system` - удалить все поды

`docker rm -f $(docker ps -a -q)` - удалить все контейнеры

`kubectl get componentstatuses` - healthcheck

# 7) Собран докер образ с веб-сервером nginx:

`docker build -t web ./`

`docker run --name web -d -p 8000:8000 web`

`docker login --username=login --password='password'`

`docker tag 26fb323aedf7 rdcm/web:latest`

`docker push rdcm/web`

# 8) Собранный образ задеплоен в kubernetes:

`kubectl apply -f web-pod.yaml`

`kubectl get pods`

`kubectl get pod web -o yaml` - получение манифеста после запуска пода

`kubectl describe pod web` - описание запущенного пода

`kubectl apply -f web-pod.yaml && kubectl get pods -w` - отслеживание изменений

`kubectl delete pod web` - удаление пода

`kubectl port-forward --address 0.0.0.0 pod/web 8000:8000` - открываем доступ к поду из вне

# 9) Альтернативный способ установки дашборда:

`minikube dashboard`

`minikube addons enable dashboard`