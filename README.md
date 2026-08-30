# Ansible Infrastructure Automation

Автоматизация развертывания Docker-инфраструктуры и контейнеризированных веб-сервисов на базе Ansible, Docker и GitHub Actions.

## Описание

Проект обеспечивает идемпотентное развертывание Docker и запуск контейнеризированных Nginx-серверов на группе хостов под управлением CentOS 10. Конфигурации генерируются динамически через Jinja2-шаблоны. Код проходит автоматическую проверку через CI/CD pipeline при каждом коммите.

## Стек

- Ansible 2.15+ (ansible-core)
- Docker CE
- Python 3.10+ / 3.12+
- CentOS 10 / Ubuntu 22.04
- GitHub Actions (ansible-lint)
- Jinja2

## Структура проекта

ansible-infra/
  ansible.cfg                  -- конфигурация Ansible
  requirements.yml             -- зависимости коллекций
  inventory/
    hosts.yml                  -- инвентарь хостов
    group_vars/
      webservers.yml           -- переменные группы
  playbooks/
    deploy-docker.yml          -- основной плейбук
  roles/
    docker_setup/
      tasks/main.yml           -- установка Docker
    nginx_deploy/
      tasks/main.yml           -- развертывание Nginx
      templates/index.html.j2  -- шаблон страницы
  .github/
    workflows/
      lint.yml                 -- CI/CD pipeline

## Требования

- Управляющая нода: Ubuntu 22.04+, Ansible 2.15+, Python 3.10+
- Целевые хосты: CentOS 10, Python 3.12+, SSH-доступ по ключам
- Сетевой доступ целевых хостов к интернету (для скачивания образов)

## Использование

Установка коллекций:

  ansible-galaxy collection install -r requirements.yml

Проверка связи:

  ansible all -m ping

Полный деплой:

  ansible-playbook playbooks/deploy-docker.yml

Деплой только Docker:

  ansible-playbook playbooks/deploy-docker.yml --tags docker

Деплой только Nginx:

  ansible-playbook playbooks/deploy-docker.yml --tags nginx

Сухой прогон (без изменений):

  ansible-playbook playbooks/deploy-docker.yml --check

## Конфигурация

Параметры задаются в inventory/group_vars/webservers.yml:

  app_name        -- название приложения
  nginx_port      -- порт на хосте (по умолчанию 8080)
  nginx_image     -- Docker-образ (по умолчанию nginx:latest)
  host_data_dir   -- директория для данных на хосте
  custom_message  -- текст на странице (поддерживает Jinja2)

## Технические решения

Блокировка Docker Hub:
  Настроены registry-mirrors в /etc/docker/daemon.json для обхода сетевых ограничений провайдера.

Отсутствие python3-docker в репозиториях CentOS 10:
  Библиотека устанавливается через pip вместо dnf, что гарантирует совместимость с модулями Ansible.

Оптимизация повторных запусков:
  Убран параметр update_cache из модулей dnf. Ansible опирается на локальную базу RPM, что сокращает время выполнения с минуты до нескольких секунд.

Стандарты кода:
  Все модули используют FQCN (ansible.builtin.dnf, community.docker.docker_container). Булевы значения в формате true/false согласно YAML 1.2.

## CI/CD

GitHub Actions автоматически запускает ansible-lint при каждом push в ветки main и feature/*. Проверяются синтаксис, FQCN, форматирование и best practices.
