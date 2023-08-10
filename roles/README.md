### Roles
Описание ролей которые лежат в моем каталоге
#### Формат такой
```
#Описание 
FileName
#Описание 
FileName
#Описание 
FileName
```
### Список playbooks
```
# Установка keepalived and haproxy с конфигами заготовленными для настройки HA Kubernetes
# Обязательно открыть файл с переменными и заполнить его: 
# install_keepalived_and_haproxy_k8s/vars/main.yml
install_keepalived_and_haproxy_k8s
# Установка заббикс агента и замена конфига 
install_zabbix_agent
# Подготовака линукса для использования
# - установка часто используемых программ
# - копирование конфигов для удобства работы в консоли (.bashrc,.inputrc)
prepare_linux
# отрабатывал работу с template
test

```
