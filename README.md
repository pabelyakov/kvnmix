# Что делает

- Устанавливает fail2ban и настраивает на ssh
- Устанавливает [telemet](https://github.com/An0nX/telemt-docker)
- Устанавилвает [3xui](https://github.com/MHSanaei/3x-ui)
- Устанавливает [Angie](https://angie.software/) и выпускает сертификат для морды 3ui

# Подготовка

## Ставим Ansible

`brew install ansible`

## Настраиваем inventory/inventory.yaml

```
all:
  hosts:
    host-1:
      ansible_host: 127.0.0.1
      ansible_user: root
      ansible_port: 22
      ansible_ssh_private_key_file: ~/.ssh/id_rsa
      domain_name: domain.example.com
      xui_upstream_host: 127.0.0.1
      xui_upstream_port: 2053
      telemt_enabled: true
      telemt_port: 8443
      telemt_config:
        secret: "Сгенерировать с помощью openssl rand -hex 16"
      acme_email: email_for_letsencrypt@mail.example
```

## Добавляем ssh-ключ на серверы из inventory

`ssh-copy-id -p 22 root@127.0.0.1`

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
  - Домен панели -> Прописываем домен
  - Жмем "Перезапуск панели"

## Получение url для подключения к telemet

- Подключаемся на хост по ssh
- `docker logs telemt | grep "EE-TLS"`
