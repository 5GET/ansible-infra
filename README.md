# Ansible Infrastructure as Code

Автоматизированное развертывание инфраструктуры мониторинга и логирования на CentOS 10 с помощью Ansible.

## Стек технологий

- **Ansible** + **Ansible Vault** — Infrastructure as Code, управление секретами
- **Docker** — контейнеризация всех сервисов
- **Nginx** — Reverse Proxy с HTTPS и Basic Auth
- **Prometheus** — сбор метрик
- **Grafana** — визуализация метрик и логов
- **Loki + Promtail** — централизованный сбор логов
- **Alertmanager** — маршрутизация алертов
- **Uptime Kuma** — мониторинг доступности сервисов
- **Node Exporter + cAdvisor** — экспортеры метрик (хост + контейнеры)
- **SELinux** — безопасность на уровне ядра
- **GitHub Actions** — CI/CD с ansible-lint

##  Архитектура

```
┌─────────────────────────────────────────────────────────────┐
│                    Nginx Reverse Proxy                       │
│  (HTTPS + Basic Auth на отдельных портах)                   │
├──────────┬──────────┬──────────┬──────────┬─────────────────┤
│ :443     │ :3443    │ :4443    │ :5443    │ :6443           │
│ Main App │ Grafana  │Prometheus│Alertmgr  │ Uptime Kuma     │
└──────────┴──────────┴──────────┴──────────┴─────────────────┘
     ↓           ↓          ↓          ↓           ↓
  my-nginx   Grafana   Prometheus  Alertmanager  Uptime Kuma
              (UI)      (TSDB)      (routing)    (health checks)
                           ↓
                    ┌──────┴──────┐
                    │  Loki       │ ← Promtail (сбор логов)
                    └─────────────┘
```

##  Что развернуто

### Мониторинг метрик
- **Prometheus** — собирает метрики с Node Exporter и cAdvisor
- **Grafana** — дашборды для хоста (#1860) и контейнеров (#193)
- **Node Exporter** — метрики ОС (CPU, RAM, Disk, Network)
- **cAdvisor** — метрики Docker-контейнеров

### Централизованные логи
- **Loki** — хранилище логов (retention 7 дней)
- **Promtail** — сборщик логов с хоста и Docker-контейнеров
- Интеграция с Grafana для просмотра логов вместе с метриками

### Алертинг
- **Alertmanager** — маршрутизация алертов в ntfy.sh
- Правила: InstanceDown, HighCPUUsage, HighMemoryUsage

### Доступность
- **Uptime Kuma** — health checks всех сервисов каждые 60 секунд
- Публичная статус-страница

##  Как запустить

```bash
# Клонируем репозиторий
git clone <your-repo-url>
cd ansible-infra

# Устанавливаем зависимости
ansible-galaxy collection install -r requirements.yml

# Создаем файл с паролем Vault
echo "your-vault-password" > .vault_pass
chmod 600 .vault_pass

# Запускаем развертывание
ansible-playbook playbooks/deploy-docker.yml
ansible-playbook playbooks/deploy-monitoring.yml
```

##  Доступы

| Сервис | URL | Логин | Пароль |
|--------|-----|-------|--------|
| Grafana | https://192.168.0.140:3443 | admin | admin123 |
| Prometheus | https://192.168.0.140:4443 | admin | securepassword123 |
| Alertmanager | https://192.168.0.140:5443 | admin | securepassword123 |
| Uptime Kuma | https://192.168.0.140:6443 | admin | uptime123 |

##  Структура проекта

```
ansible-infra/
├── inventory/
│   ├── hosts.yml              # Инвентарь хостов
│   └── group_vars/
│       └── webservers_secret.yml  # Зашифрованные секреты (Vault)
├── playbooks/
│   ├── deploy-docker.yml      # Установка Docker
│   └── deploy-monitoring.yml  # Развертывание стека мониторинга
├── roles/
│   ├── docker_setup/          # Установка Docker
│   ├── nginx_deploy/          # Основное приложение
│   ├── exporters/             # Node Exporter + cAdvisor
│   ├── prometheus/            # Prometheus
│   ├── grafana/               # Grafana
│   ├── alertmanager/          # Alertmanager
│   ├── nginx_proxy/           # Nginx Reverse Proxy
│   ├── uptime_kuma/           # Uptime Kuma
│   ├── loki/                  # Loki
│   └── promtail/              # Promtail
└── .github/workflows/
    └── lint.yml               # CI/CD с ansible-lint
```

##  Безопасность

- Все пароли зашифрованы через **Ansible Vault**
- Nginx настроен с **HTTPS** (self-signed сертификаты)
- Prometheus и Alertmanager защищены **Basic Auth**
- SELinux в режиме **enforcing** с правильными политиками для нестандартных портов
- `.vault_pass` добавлен в `.gitignore`


