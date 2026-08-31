# Ansible Infrastructure Automation

Automated deployment of Docker infrastructure and containerized web services using Ansible, Docker, and GitHub Actions.

## Tech Stack

- Ansible 2.15+ (ansible-core)
- Docker CE
- Python 3.10+ / 3.12+
- CentOS 10 / Ubuntu 22.04
- GitHub Actions (ansible-lint)
- Jinja2

## Project Structure

```text
ansible-infra/
  ansible.cfg                  -- Ansible configuration
  requirements.yml             -- Collection dependencies
  inventory/
    hosts.yml                  -- Host inventory
    group_vars/
      webservers.yml           -- Group variables
      webservers_secret.yml    -- Secrets (gitignored)
      webservers.yml.example   -- Variable template
  playbooks/
    deploy-docker.yml          -- Docker and Nginx deployment
    deploy-monitoring.yml      -- Monitoring stack deployment
  roles/
    docker_setup/              -- Docker installation
    nginx_deploy/              -- Nginx deployment
    exporters/                 -- Node Exporter and cAdvisor
    prometheus/                -- Prometheus and alert rules
    grafana/                   -- Grafana provisioning
    alertmanager/              -- Alertmanager and webhooks
  .github/
    workflows/
      lint.yml                 -- CI/CD pipeline
Prerequisites
Control node: Ubuntu 22.04+, Ansible 2.15+, Python 3.10+
Managed nodes: CentOS 10, Python 3.12+, SSH key access
Internet access on managed nodes (for pulling images)
