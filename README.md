## Описание скриптов Ansible в этом репозитории
Итак, тут хранятся разные кусочки и нарезки которые я применяю в работе.

### Ansible inventory
Список хостов хранится вот в ниже указанном виде:
```
├── ansible.cfg
├── group_vars
│   ├── linux
│   └── together
│       ├── vars
│       └── vault
├── hosts.txt    - список компов
├── pass.txt     - пароль для ansible-vault, нужен для расшифровки group_vars/together/vault
├── playbooks    - список playbooks
│   ├── 
├── README.md
└── roles        - список ролей
    ├── 
```
Содержимое файла `ansible.cfg`
``` ini
[defaults]
host_key_checking = false
#файл инвентаризации
inventory         = ./hosts.txt
#путь к каталогу с ролями
roles_path        = ./roles
#пароль для ansible-vault, чтобы не использовать КАЖДЫЙ РАЗ параметр --ask-vault-pass
vault_password_file = ./pass.txt
```
#### Описание каталога group_vars
В папке `group_vars` лежат переменные, используется метод хранения переменных в мульти каталогах [link](https://docs.ansible.com/ansible/latest/tips_tricks/sample_setup.html#sample-setup)
В файле hosts.txt есть 2 секции для которых я объявил файл-переменные в каталоге `group_vars`:
  * `[linux]`
  * `[together:children]` - это группа **together** содержащая в себе другие группы

Использовал 2 способа хранения переменных:  
**в незашифрованном виде**
  ```
  ├── group_vars
      ├── linux
  ```
**в зашифрованном виде** - статья как это [сделать](https://docs.ansible.com/ansible/latest/tips_tricks/ansible_tips_tricks.html#tip-for-variables-and-vaults)
```
├── group_vars
│   └── together
│       ├── vars
│       └── vault
```
### Запуск playbook и roles
Для запуска playbook использовать вот такую команду:
```ansible-playbook playbook/<name> -e hn=<host> -b```
Где, hn - это переменная hostname в моих плэйбуках
