### Playbooks
Описание плэйбуков которые лежат в моем каталоге, а то я уже путаться в них начал.  
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
# Пример создания учетной записи
initnewuser.yml
# Установка apache
install_apache.yml
# Установка docker.io, взял готовый и обновил его (2023)
install_docker.yml
# Установка kubernetis, взял готовый и обновил его (2023) 
install_k8s.yml
# Установка linstor
install_linstor_drbd.yml
# Файл c репозиториями для Linux Oracle 7
# прописаны необхожимые репозиториия, для настройки GlusterFS, docker.io
oracle-linux-ol7.repo
# Пинг всех машин
ping_all.yml
# Удаление docker.io
remove_docker.yml
# Запуск роли, прежде чем запускать отредактировать переменные надо, 
# каталог ./roles/install_keepalived_and_haproxy_k8s/vars/main.yml
role_install_keepalived_and_haproxy_k8s.yml
# Запуск роли prepare_linux ()
# - Установка основного набора ПО
# - копирование конфигов
# - утановка заббикс
role_prepare_linux.yml
# тест
role_test2.yml
# выключить компьютеры
shutdown_all_pc.yml
# тестовый playbook, проверял как работает проверка каталога на существование
test.yml
# Пример пополнения файлика /etc/hosts с использованием ansible
update-hosts-k8s.yml
# Добавление файла репозитория в Oracle Linux
# проще оказалось создать файл 
update_oracle_linux_repos.yml
# выполнение команд apt update && apt upgrade на машинах
update_sof.yml

```
