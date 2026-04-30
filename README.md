# Что делает
```
Актуально для Ubuntu 24.04
```
- Устанавливает fail2ban и настраивает на ssh
- Устанавливает [telemet](https://github.com/An0nX/telemt-docker)
- Устанавилвает [3xui](https://github.com/MHSanaei/3x-ui) (опционально, `xui_enabled: true`)
- Устанавливает [Angie](https://angie.software/) (опционально, `xui_enabled: true`) и выпускает сертификат для морды 3ui

# Подготовка

## Ставим Ansible

`brew install ansible`

## Настраиваем inventory/inventory.yaml

Берем за основу `inventory.example.yaml`, копируем в `inventory.yaml`

## Добавляем ssh-ключ на серверы из inventory

`ssh-copy-id -p 22 root@127.0.0.1`

## Меняем дефолтный ssh порт на другой

- `sudo nano /etc/ssh/sshd_config`
- `Выставляем в Port нужное значение`
- `sudo nano /lib/systemd/system/ssh.socket`
- `Выставляем для ListenStream нужное значение`
- `sudo systemctl restart ssh`
- `sudo systemctl daemon-reload`
- `sudo systemctl restart ssh.socket`

## Ставим зависимости

`ansible-galaxy install -r requirements.yml`

## Проверяем доступность хостов

`ansible all -i inventory/inventory.yaml -m ping`

# Установка

## dry run

`ansible-playbook -i inventory/inventory.yaml main.yaml --check`

## run

`ansible-playbook -i inventory/inventory.yaml main.yaml`

# После установки

## Донастройка 3xui

- По адресу домену, определенному в конфиге
- Логинимся в ui: admin / admin
  - Настроки -> Учетная запись - меняем логин и пароль
  - Панель -> IP-адрес управления панелью выставляем 127.0.0.1 (перестраховка, т.к. локальный порт понели отсекается через ufw)
  - Домен панели -> `значение_domain_name_из_inventory`
  - Жмем "Перезапуск панели"
  - Настройки -> Подписка
    - Прослушивание IP : 127.0.0.1
    - Домен прослушивания: `значение_domain_name_из_inventory`
    - Корневой путь URL-адреса подписки: `/значение_xui_subscription_path_из_inventory/`
    - URI обратного прокси: `https://значение_domain_name_из_inventory/значение_xui_subscription_path_из_inventory/`


## Получение url для подключения к telemet

- Подключаемся на хост по ssh
- `docker logs telemt | grep "EE-TLS"`
