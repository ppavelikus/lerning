install_keepalived_and_haproxy_k8s
=========
Эта роль устанавливает keepalived, haproxy и копирует конфигурационные файла на указанные ноды.
[Ссылка на настройку keepalived_and_haproxy](https://github.com/kubernetes/kubeadm/blob/main/docs/ha-considerations.md)

Requirements
------------
Обязательно заполнить файл с перменными vars/main.yml

Dependencies
--------------
Эту роль использовать после установки ПО kubernetes.io на ноды

Для установки запускать playbook: 
```
../playbooks/install_k8s.yml
```

Role Variables
--------------
Пример файла с перменными:
``` yaml
---
# vars file for test
# https://github.com/kubernetes/kubeadm/blob/main/docs/ha-considerations.md#kube-vip
# MASTER OR BACKUP
#STATE: MASTER
# Interface
#INTERFACE: ens03
# Any number (Этот номер дожен совпадать на всех нодах где запущен keepalived)
ROUTER_ID: 100
# MASTER is higher number, for exsample: MASTER: 101, BACKUP: 100
# (Это приоритет, тут просто у мастера он самый высокий, всем делаем 100, мастеру исправляем на 101)
PRIORITY: 100
# some pass (пароль, одинаковый у всех)
AUTH_PASS: passw
# Virtual ip (Это виртуальный IP через который мы будм подключаться)
APISERVER_VIP: 192.168.1.1
# default port of haproxy
APISERVER_DEST_PORT: 8888
# default port API Server
APISERVER_SRC_PORT: 6443
# address list of servers with control plane 
# (Это пример заполенения, сколько у нас нод с haproxy, вот столько строк и пишем в этом файле)
APISERVER_LIST_FOR_HAPROXY:
  - name: app1
    address: 192.168.1.2
  - name: app2
    address: 192.168.1.3
  - name: app3
    address: 192.168.1.3
  - name: app4
    address: 192.168.1.4

```
После того как роль отработала
---------------
Я не стал через ansible запускать службы  `haproxy` и `keepalived` всеравно скорее всего без ручной донастройки они не запустятся.

Настройка keepalived
-------------
Идем на каждую ноду и правим файл `nano /etc/keepalived/keepalived.conf`
Вид файла будет вот такой:
```
! /etc/keepalived/keepalived.conf
! Configuration File for keepalived
global_defs {
    router_id LVS_DEVEL
}
vrrp_script check_apiserver {
  script "/etc/keepalived/check_apiserver.sh"
  interval 3
  weight -2
  fall 10
  rise 2
}

vrrp_instance VI_1 {
    # MASTER OR BACKUP
    state ${STATE}
    interface ${INTERFACE}
    virtual_router_id 100
    priority 100
    authentication {
        auth_type PASS
        auth_pass k8ds9det
    }
    virtual_ipaddress {
        192.168.103.46
    }
    track_script {
        check_apiserver
    }
}
```
В файле оставил 2 перменные в формате BASH:
  * `${STATE}` - На мастер ноде, это нужно заменить на `MASTER`, на всех остальных заменить на `BACKUP`
  * `${INTERFACE}` - Заменить на правильное название интерфейса
  * `priority 100` - исправить значение на `101` или выше, т.к. это мастер нода, на остальных оставить `100`
После этого запустить keepalived:
```
systemctl start keepalived
systemctl status keepalived
```
Настройка haproxy
------------------
  * На каждой ноде открыть файл `/etc/haproxy/haproxy.cfg`
  * Проверить что в нем нету перменных типа `{{ APISERVER_LIST_FOR_HAPROXY }}`
  * Запустить службу: 
    ```
    systemctl start haproxy.service
    systemctl status haproxy.service
    ```
  * Проверить, порт **8888** слушает haproxy или нет

Теперь создаем кластер kubernetes
--------------
После этого кластер kubernetes.io создаем вот такой командой:  
`kubeadm init --control-plane-endpoint "k8s-sem:8888" --upload-certs --pod-network-cidr 172.16.1.0/24`  
В домене создал DNS- запись `k8s-sem` для виртуального IP.  
Порт указываем 8888, этот порт будет балансировать `HAPROXY`  
Подсеть для подов будет `172.16.1.0/24`  

Эту же подсеть надо будет прописывать в сетевом плагине (CNI).

