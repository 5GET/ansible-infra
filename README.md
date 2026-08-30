# Ansible Infrastructure Automation

## Описание
Проект автоматизации развертывания и настройки серверов с использованием Ansible и Docker.

## Структура проекта
- inventory/ - инвентарь хостов (список серверов)
- playbooks/ - плейбуки Ansible (сценарии автоматизации)
- roles/ - роли Ansible (переиспользуемые блоки)
- files/ - файлы для копирования на хосты
- templates/ - шаблоны конфигураций

## Возможности
- Автоматическая установка Docker на все серверы
- Развертывание Nginx в контейнере
- Централизованное управление конфигурациями
- Версионирование инфраструктуры через Git

## Использование

### Проверка связи с хостами
ansible all -i inventory/hosts.yml -m ping

### Запуск плейбука для установки Docker
ansible-playbook -i inventory/hosts.yml playbooks/deploy-docker.yml

## Технологии
- Ansible 2.16+
- Python 3.10+
- Docker
- Git
