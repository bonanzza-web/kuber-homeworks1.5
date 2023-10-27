# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 2»

### Цель задания

В тестовой среде Kubernetes необходимо обеспечить доступ к двум приложениям снаружи кластера по разным путям.

------

### Чеклист готовности к домашнему заданию

1. Установленное k8s-решение (например, MicroK8S).
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключённым Git-репозиторием.

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Инструкция](https://microk8s.io/docs/getting-started) по установке MicroK8S.
2. [Описание](https://kubernetes.io/docs/concepts/services-networking/service/) Service.
3. [Описание](https://kubernetes.io/docs/concepts/services-networking/ingress/) Ingress.
4. [Описание](https://github.com/wbitt/Network-MultiTool) Multitool.

------

### Задание 1. Создать Deployment приложений backend и frontend

1. Создать Deployment приложения _frontend_ из образа nginx с количеством реплик 3 шт.
2. Создать Deployment приложения _backend_ из образа multitool. 
3. Добавить Service, которые обеспечат доступ к обоим приложениям внутри кластера. 
4. Продемонстрировать, что приложения видят друг друга с помощью Service.
5. Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.

------

### Задание 2. Создать Ingress и обеспечить доступ к приложениям снаружи кластера

1. Включить Ingress-controller в MicroK8S.
2. Создать Ingress, обеспечивающий доступ снаружи по IP-адресу кластера MicroK8S так, чтобы при запросе только по адресу открывался _frontend_ а при добавлении /api - _backend_.
3. Продемонстрировать доступ с помощью браузера или `curl` с локального компьютера.
4. Предоставить манифесты и скриншоты или вывод команды п.2.

------

### Ответы    

### Данное задание я выполнил, используя terraform и ansible. Создание машины, установка microk8s и kubectl, а также создание подов, сервисов и ингресса выполнены автоматически после запуска terraform apply. В качестве ответа привожу вывод describe ingress и скриншоты. Все файлы в репозитории

```
ubuntu@server-kube:~$ micro describe ingress
Name:             nginx-ingress
Labels:           <none>
Namespace:        default
Address:
Ingress Class:    nginx
Default backend:  <default>
Rules:
  Host             Path  Backends
  ----             ----  --------
  nginx-ubunta.ru
                   /      nginx-svc:80 (<none>)
                   /api   mult-svc:80 (<none>)
Annotations:       nginx.ingress.kubernetes.io/rewrite-target: /
Events:            <none>
ubuntu@server-kube:~$ micro get po -o wide
NAME                            READY   STATUS              RESTARTS   AGE   IP       NODE          NOMINATED NODE   READINESS GATES
nginx-deploy-7c79c4bf97-rznhs   0/1     ContainerCreating   0          72s   <none>   server-kube   <none>           <none>
nginx-deploy-7c79c4bf97-7xftm   0/1     ContainerCreating   0          72s   <none>   server-kube   <none>           <none>
nginx-deploy-7c79c4bf97-x52r6   0/1     ContainerCreating   0          72s   <none>   server-kube   <none>           <none>
mult-deploy-8545445f7b-q9dmd    0/1     ContainerCreating   0          71s   <none>   server-kube   <none>           <none>
ubuntu@server-kube:~$ htop
ubuntu@server-kube:~$ micro get po -o wide
NAME                            READY   STATUS    RESTARTS   AGE   IP            NODE          NOMINATED NODE   READINESS GATES
nginx-deploy-7c79c4bf97-x52r6   1/1     Running   0          94s   10.1.55.196   server-kube   <none>           <none>
nginx-deploy-7c79c4bf97-rznhs   1/1     Running   0          94s   10.1.55.194   server-kube   <none>           <none>
nginx-deploy-7c79c4bf97-7xftm   1/1     Running   0          94s   10.1.55.195   server-kube   <none>           <none>
mult-deploy-8545445f7b-q9dmd    1/1     Running   0          93s   10.1.55.199   server-kube   <none>           <none>
ubuntu@server-kube:~$ micro describe ingress
Name:             nginx-ingress
Labels:           <none>
Namespace:        default
Address:          127.0.0.1
Ingress Class:    nginx
Default backend:  <default>
Rules:
  Host             Path  Backends
  ----             ----  --------
  nginx-ubunta.ru
                   /      nginx-svc:80 (10.1.55.194:80,10.1.55.195:80,10.1.55.196:80)
                   /api   mult-svc:80 (10.1.55.199:80)
Annotations:       nginx.ingress.kubernetes.io/rewrite-target: /
Events:
  Type    Reason  Age              From                      Message
  ----    ------  ----             ----                      -------
  Normal  Sync    8s (x2 over 9s)  nginx-ingress-controller  Scheduled for sync

```

Screenshots     
    
![alt text](https://github.com/bonanzza-web/kuber-homeworks1.5/blob/main/img/1.png)

![alt text](https://github.com/bonanzza-web/kuber-homeworks1.5/blob/main/img/2.png)

![alt text](https://github.com/bonanzza-web/kuber-homeworks1.5/blob/main/img/hosts.png)

------

### Правила приема работы

1. Домашняя работа оформляется в своем Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд `kubectl` и скриншоты результатов.
3. Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.

------