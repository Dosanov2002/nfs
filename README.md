# 📦 Ansible NFS Setup

Автоматическая установка и настройка **NFS-сервера и клиента** с помощью Ansible.  
Проект настраивает:
- NFS-сервер с отдельным XFS-диском
- Монтирование экспортированной папки на клиенте

---

## 🗂 Структура

```bash
ansible_nfs_project/
├── inventories/
│   └── prod/
│       └── hosts.ini        # Инвентори с IP серверов
├── roles/
│   ├── nfs_server/          # Роль сервера
│   │   └── tasks/main.yml
│   └── nfs_client/          # Роль клиента
│       └── tasks/main.yml
└── site.yml                 # Главный плейбук
```

---

## 🛠️ Требования

- Ubuntu сервер (и клиент)
- Доступ по SSH (рекомендуется VPN или офисная сеть)
- Второй диск на NFS-сервере (например, `/dev/sdb`)
- Ansible установлен на управляющей машине

---

## 📋 Inventory файл

`inventories/prod/hosts.ini`

```ini
[nfs_server]
10.215.19.10

[nfs_client]
10.215.19.20
```

---

## 🚀 Запуск

1. Установи зависимости (если нужно):

```bash
sudo apt update && sudo apt install ansible -y
```

2. Запусти плейбук:

```bash
ansible-playbook -i inventories/prod/hosts.ini site.yml
```

---

## ⚙️ Что делает playbook

### На сервере:
- Устанавливает `nfs-kernel-server`
- Форматирует `/dev/sdb` в XFS (если диск доступен)
- Монтирует диск в `/data/nfs_share`
- Экспортирует папку через NFS

### На клиенте:
- Устанавливает `nfs-common`
- Монтирует экспорт с сервера в `/mnt/nfs_share`

---

## 📌 Примечания

- Убедись, что диск `/dev/sdb` существует и не размечен.
- По умолчанию используется подсеть `10.215.19.0/24`. Измени её в `roles/nfs_server/tasks/main.yml`, если нужно.
- Файрвол и VPN должны позволять подключение по NFS (порт `2049`).

