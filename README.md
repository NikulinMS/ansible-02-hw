# Домашнее задание к занятию "`Работа с Playbook`" - `Никулин Михаил Сергеевич`



---

## Основная часть

#### 1. Подготовьте свой inventory-файл `prod.yml`.

Подготовим [prod.yml](src%2Fplaybook%2Finventory%2Fprod.yml)

#### 2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает [vector](https://vector.dev). Конфигурация vector должна деплоиться через template файл jinja2. От вас не требуется использовать все возможности шаблонизатора, просто вставьте стандартный конфиг в template файл. Информация по шаблонам по [ссылке](https://www.dmosk.ru/instruktions.php?object=ansible-nginx-install).

В [site.yml](src%2Fplaybook%2Fsite.yml) добавлен блок task с ```tags: vector```, который устанавливает и настраивает Vector

#### 3. При создании tasks рекомендую использовать модули: `get_url`, `template`, `unarchive`, `file`.

Использованы модули:
1. ```ansible.builtin.get_url```
2. ```ansible.builtin.yum```
3. ```ansible.builtin.meta```
4. ```ansible.builtin.pause```
5. ```ansible.builtin.command```
6. ```ansible.builtin.file```
7. ```ansible.builtin.unarchive```
8. ```ansible.builtin.copy```
9. ```ansible.builtin.replace```
10. ```ansible.builtin.user```
11. ```ansible.builtin.service```
12. ```ansible.builtin.systemd```


#### 4. Tasks должны: скачать дистрибутив нужной версии, выполнить распаковку в выбранную директорию, установить vector.

Нужные task представлены в [site.yml](src%2Fplaybook%2Fsite.yml)

#### 5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.

```
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-02-playbook/playbook# ansible-lint site.yml 
WARNING  Listing 11 violation(s) that are fatal
key-order[task]: You can improve the task key order to: name, tags, block
site.yml:22 Task/Handler: Install, configure, and start Clickhouse

name[missing]: All tasks should be named.
site.yml:24 Task/Handler: block/always/rescue 

yaml[octal-values]: Forbidden implicit octal value "0644"
site.yml:29

yaml[octal-values]: Forbidden implicit octal value "0644"
site.yml:36

key-order[task]: You can improve the task key order to: name, tags, block
site.yml:63 Task/Handler: Install, configure, and start Vector

yaml[octal-values]: Forbidden implicit octal value "0755"
site.yml:69

yaml[octal-values]: Forbidden implicit octal value "0644"
site.yml:75

yaml[octal-values]: Forbidden implicit octal value "0755"
site.yml:89

yaml[octal-values]: Forbidden implicit octal value "0644"
site.yml:105

yaml[octal-values]: Forbidden implicit octal value "0644"
site.yml:115

yaml[octal-values]: Forbidden implicit octal value "0755"
site.yml:140

Read documentation for instructions on how to ignore specific rule violations.

                Rule Violation Summary                 
 count tag                profile rule associated tags 
     2 key-order[task]    basic   formatting           
     1 name[missing]      basic   idiom                
     8 yaml[octal-values] basic   formatting, yaml     

Failed: 11 failure(s), 0 warning(s) on 1 files. Last profile that met the validation criteria was 'min'.
```
Ошибки заключаются в отсутствии имени блока, неверной последовательности task/block и неверном указании параметров ```mode```. Добавил имя, а остальные ошибки решил автоматическим исправлением ``` ansible-lint site.yml --fix```
Отформатированный файл [site.yml](src%2Fplaybook%2Fsite.yml)
#### 6. Попробуйте запустить playbook на этом окружении с флагом `--check`.

![img_1.png](img%2Fimg_1.png)
Проверка останавливается на этапе установки пакетов Clickhouse, т.к. они не скачаны в систему

#### 7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.

```
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-02-playbook/playbook# ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Clickhouse & Vector] *************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] ******************************************************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "centos", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "centos", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] ******************************************************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
ok: [clickhouse-01] => (item=clickhouse-common-static)

TASK [Install clickhouse packages] *************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Flush handlers] **************************************************************************************************************************************************************************************************************

TASK [Wait for clickhouse-server to become available] ******************************************************************************************************************************************************************************
Pausing for 15 seconds (output is hidden)
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [clickhouse-01]

TASK [Create database] *************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create vector work directory] ************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get Vector distrib] **********************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Unzip Vector archive] ********************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Install Vector binary] *******************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Check Vector installation] ***************************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Create Vector config vector.toml] ********************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create vector.service daemon] ************************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Modify vector.service file] **************************************************************************************************************************************************************************************************
--- before: /lib/systemd/system/vector.service
+++ after: /lib/systemd/system/vector.service
@@ -8,7 +8,7 @@
 User=vector
 Group=vector
 ExecStartPre=/usr/bin/vector validate
-ExecStart=/usr/bin/vector
+ExecStart=/usr/bin/vector --config /etc/vector/vector.toml
 ExecReload=/usr/bin/vector validate
 ExecReload=/bin/kill -HUP $MAINPID
 Restart=no

changed: [clickhouse-01]

TASK [Create user vector] **********************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create Vector data_dir] ******************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

RUNNING HANDLER [Start Vector service] *********************************************************************************************************************************************************************************************
ok: [clickhouse-01]

PLAY RECAP *************************************************************************************************************************************************************************************************************************
clickhouse-01              : ok=16   changed=3    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0 
```
Проверяем изменения на хосте clickhouse-01 (158.160.120.5)
```
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-02-playbook/playbook# ssh centos@158.160.120.5
[centos@node01 ~]$ ip -br a
lo               UNKNOWN        127.0.0.1/8 ::1/128 
eth0             UP             192.168.101.31/24 fe80::d20d:9aff:fecb:79c8/64 
[centos@node01 ~]$ 
[centos@node01 ~]$ id vector
uid=1001(vector) gid=1001(vector) groups=1001(vector)
[centos@node01 ~]$ 
[centos@node01 ~]$ vector --version
vector 0.21.1 (x86_64-unknown-linux-gnu 18787c0 2022-04-22)
[centos@node01 ~]$ 
[centos@node01 ~]$ systemctl status clickhouse-server
● clickhouse-server.service - ClickHouse Server (analytic DBMS for big data)
   Loaded: loaded (/usr/lib/systemd/system/clickhouse-server.service; disabled; vendor preset: disabled)
   Active: active (running) since Fri 2023-11-03 22:31:39 UTC; 1 day 12h ago
 Main PID: 31139 (clckhouse-watch)
   CGroup: /system.slice/clickhouse-server.service
           ├─31139 clickhouse-watchdog --config=/etc/clickhouse-server/config.xml --pid-file=/run/clickhouse-server/clickhouse-server.pid
           └─31142 /usr/bin/clickhouse-server --config=/etc/clickhouse-server/config.xml --pid-file=/run/clickhouse-server/clickhouse-server.pid

Nov 03 22:31:39 node01.netology.cloud systemd[1]: Started ClickHouse Server (analytic DBMS for big data).
Nov 03 22:31:39 node01.netology.cloud clickhouse-server[31139]: Processing configuration file '/etc/clickhouse-server/config.xml'.
Nov 03 22:31:39 node01.netology.cloud clickhouse-server[31139]: Logging trace to /var/log/clickhouse-server/clickhouse-server.log
Nov 03 22:31:39 node01.netology.cloud clickhouse-server[31139]: Logging errors to /var/log/clickhouse-server/clickhouse-server.err.log
Nov 03 22:31:50 node01.netology.cloud clickhouse-server[31139]: Processing configuration file '/etc/clickhouse-server/config.xml'.
Nov 03 22:31:50 node01.netology.cloud clickhouse-server[31139]: Saved preprocessed configuration to '/var/lib/clickhouse/preprocessed_configs/config.xml'.
Nov 03 22:31:50 node01.netology.cloud clickhouse-server[31139]: Processing configuration file '/etc/clickhouse-server/users.xml'.
Nov 03 22:31:50 node01.netology.cloud clickhouse-server[31139]: Saved preprocessed configuration to '/var/lib/clickhouse/preprocessed_configs/users.xml'.
[centos@node01 ~]$ 
[centos@node01 ~]$ clickhouse-client 
ClickHouse client version 22.3.3.44 (official build).
Connecting to localhost:9000 as user default.
Connected to ClickHouse server version 22.3.3 revision 54455.

node01.netology.cloud :) SHOW DATABASES;

SHOW DATABASES

Query id: b7963e74-69a5-4a93-a679-c64c0b3b89a5

┌─name───────────────┐
│ INFORMATION_SCHEMA │
│ default            │
│ information_schema │
│ logs               │
│ system             │
└────────────────────┘

5 rows in set. Elapsed: 0.001 sec. 

node01.netology.cloud :) q
Bye.
```

#### 8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.

```
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-02-playbook/playbook# ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Clickhouse & Vector] *************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] ******************************************************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "centos", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "centos", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] ******************************************************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
ok: [clickhouse-01] => (item=clickhouse-common-static)

TASK [Install clickhouse packages] *************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Flush handlers] **************************************************************************************************************************************************************************************************************

TASK [Wait for clickhouse-server to become available] ******************************************************************************************************************************************************************************
Pausing for 15 seconds (output is hidden)
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [clickhouse-01]

TASK [Create database] *************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create vector work directory] ************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get Vector distrib] **********************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Unzip Vector archive] ********************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Install Vector binary] *******************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Check Vector installation] ***************************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Create Vector config vector.toml] ********************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create vector.service daemon] ************************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Modify vector.service file] **************************************************************************************************************************************************************************************************
--- before: /lib/systemd/system/vector.service
+++ after: /lib/systemd/system/vector.service
@@ -8,7 +8,7 @@
 User=vector
 Group=vector
 ExecStartPre=/usr/bin/vector validate
-ExecStart=/usr/bin/vector
+ExecStart=/usr/bin/vector --config /etc/vector/vector.toml
 ExecReload=/usr/bin/vector validate
 ExecReload=/bin/kill -HUP $MAINPID
 Restart=no

changed: [clickhouse-01]

TASK [Create user vector] **********************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create Vector data_dir] ******************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

RUNNING HANDLER [Start Vector service] *********************************************************************************************************************************************************************************************
ok: [clickhouse-01]

PLAY RECAP *************************************************************************************************************************************************************************************************************************
clickhouse-01              : ok=16   changed=3    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
```

#### 9. Подготовьте README.md-файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги. Пример качественной документации ansible playbook по [ссылке](https://github.com/opensearch-project/ansible-playbook).

[site.yml](src%2Fplaybook%2Fsite.yml) содержит 2 блока:
#### 1. Первый блок объединяет последовательность задач по инсталяции Clickhouse. Блоку соответствует ```tags: clickhouse```. В блоке используются параметры:
- ```clickhouse_version: "22.3.3.44"``` - версия Clickhouse
- ```clickhouse_packages: ["clickhouse-client", "clickhouse-server", "clickhouse-common-static"]``` - список пакетов для установки

Task'и:
- ```TASK [Clickhouse. Get clickhouse distrib]``` - скачивает rpm-пакеты с дистрибутивами с помощью модуля ```ansible.builtin.get_url```
- ```TASK [Clickhouse. Install clickhouse packages]``` - устанавливает набор пакетов с помощью модуля ```ansible.builtin.yum```
- ```TASK [Clickhouse. Flush handlers]``` - инициирует внеочередной запуск хандлера ```Start clickhouse service```
- ```RUNNING HANDLER [Start clickhouse service]``` - для старта сервера ```clickhouse``` в хандлере используется модуль ```ansible.builtin.service```
- ```TASK [Clickhouse. Wait for clickhouse-server to become available]``` - устанавливает паузу в 15 секунд с помощью модуля ```ansible.builtin.pause```, чтобы сервер Clickhouse успел запуститься. Иначе следующая задача по созданию БД может завершиться ошибкой, т.к. сервер еще не успел подняться
- ```TASK [Clickhouse. Create database]``` - создает инстанс базы данных Clickhouse

#### 2. Второй блок объединяет последовательность задач по инсталяции Vector. Блоку соответствует ```tags: vector```. В блоке используются параметры:
- ```vector_version: "0.21.1"``` - версия Vector
- ```vector_os_arh: "x86_64"``` - архитектура ОС
- ```vector_workdir: "/home/centos/vector"``` - рабочий каталог, в котором будут сохранены скачанные rpm-пакеты
- ```vector_os_user: "vector"``` - имя пользователя-владельца Vector в ОС
- ```vector_os_group: "vector"``` - имя группы пользователя-владельца Vector в ОС

Task'и:
- ```TASK [Vector. Create work directory]``` - создает рабочий каталог, в котором будут сохранены скачанные rpm-пакеты, с помощью модуля ```ansible.builtin.file```
- ```TASK [Vector. Get Vector distributive]``` - скачивает архив с дистрибутивом с помощью модуля ```ansible.builtin.get_url```
- ```TASK [Vector. Unzip archive]``` - распаковывает скачанный архив с помощью модуля ```ansible.builtin.unarchive```
- ```TASK [Vector. Install vector binary file]``` - копирует исполняемый файл Vector в ```/usr/bin``` с помощью модуля ```ansible.builtin.copy```
- ```TASK [Vector. Check Vector installation]``` - проверяет, что бинарный файл Vector работает корректно, с помощью модуля ```ansible.builtin.command```
- ```TASK [Vector. Create Vector config vector.toml]``` - создает файл ```/etc/vector/vector.toml``` с конфигом Vector с помощью модуля ```ansible.builtin.copy```
- ```TASK [Vector. Create vector.service daemon]``` - создает файл юнита systemd ```/lib/systemd/system/vector.service``` с помощью модуля ```ansible.builtin.copy```
- ```TASK [Vector. Modify vector.service file]``` - редактирует файл юнита systemd ```/lib/systemd/system/vector.service``` с помощью модуля ```ansible.builtin.replace```
- ```TASK [Vector. Create user vector]``` - создает пользователя ОС с помощью модуля ```ansible.builtin.user```
- ```TASK [Vector. Create data_dir]``` - создает каталог для данных Vector с помощью модуля ```ansible.builtin.file```
- ```RUNNING HANDLER [Start Vector service]``` - инициируется запуск хандлера ```Start Vector service```, обновляющего конфигурацию systemd и стартующего сервис ```vector.service``` с помощью модуля ```ansible.builtin.systemd```


#### 10. Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-02-playbook` на фиксирующий коммит, в ответ предоставьте ссылку на него.

