# Ansible DevOps Lab
![Ansible](https://img.shields.io/badge/ansible-automation-red)
![Docker](https://img.shields.io/badge/docker-containerized-blue)
![GitHub Actions](https://img.shields.io/badge/github_actions-CI-black)
![Nginx](https://img.shields.io/badge/nginx-web--server-green)
![License](https://img.shields.io/badge/license-MIT-green)

A local and CI-ready Ansible lab that provisions and deploys a containerized nginx web application.  
This project demonstrates a DevOps workflow including Ansible roles, Jinja2 templating, Docker deployment, and automated CI with GitHub Actions.

Repository:  
https://github.com/sassenagh/ansible-devops-lab

---

# Architecture

Ansible runs inside a Docker container (locally) or directly on the GitHub Actions runner (CI). It renders an HTML template and deploys it via an nginx container.

```
   Push to GitHub
         │
         ▼
  GitHub Actions Runner
         │
         ▼
   Ansible Playbook
  ┌──────────────────┐
  │  Role: app       │  → Creates directory + renders HTML template
  │  Role: deploy    │  → Runs nginx Docker container
  └──────────────────┘
         │
         ▼
   nginx container
   http://localhost:8081
```

Deployment workflow:

```
GitHub → CI/CD → Ansible Roles → Docker Container → nginx web app
```

---

# Features

- Ansible roles for **app provisioning** and **Docker deployment**
- HTML rendering with **Jinja2 templates** and environment variables
- Containerized nginx serving static content
- Local execution via **Docker Compose**
- CI pipeline with **syntax check** and **ansible-lint** at `production` profile
- Fully runs inside **GitHub Actions** on every push

---

# Tech Stack

## Automation
- Ansible
- Jinja2 templating

## Infrastructure
- Docker
- nginx:alpine

## CI/CD
- GitHub Actions

---

# Project Structure

```
ansible-devops-lab/
│
├── ansible/
│   ├── inventory.ini
│   ├── playbook.yml
│   ├── group_vars/
│   │   └── dev.yml
│   └── roles/
│       ├── docker/
│       │   ├── tasks/main.yml
│       │   └── handlers/main.yml
│       ├── app/
│       │   ├── tasks/main.yml
│       │   └── templates/index.html.j2
│       └── deploy/
│           └── tasks/main.yml
│
├── .github/workflows/
│   └── ci.yml
├── docker-compose.yml
└── README.md
```

---

# How It Works

1. The **app role** creates the app directory and renders `index.html` from a Jinja2 template.
2. The **deploy role** stops any existing webapp container and starts a new nginx container mounting the generated HTML.
3. The **docker role** handles Docker installation for remote server deployments.
4. Locally, Docker Compose runs the ansible-runner container with the Docker socket mounted.
5. In CI, GitHub Actions runs the playbook directly on the `ubuntu-latest` runner.

---

# Running Locally

Clone the repository:

```bash
git clone https://github.com/sassenagh/ansible-devops-lab.git
cd ansible-devops-lab
```

Run with Docker Compose:

```bash
HOST_APP_PATH=$(pwd)/app docker compose up
```

The application will be available at [http://localhost:8081](http://localhost:8081).

### Why `HOST_APP_PATH`

Docker Desktop requires host paths for volume mounts. Since Ansible runs inside a container, `HOST_APP_PATH` passes the real host path so nginx can mount it correctly.

---

# GitHub Actions Pipeline

Triggered on every push. Steps run in order:

1. Install Ansible and `community.docker` collection
2. Install Python Docker SDK
3. Syntax check the playbook
4. Run `ansible-lint` at `production` profile
5. Execute the full playbook

Example workflow trigger:

```yaml
on: [push]
```

---

# Future Improvements

- Add SSH-based deployment to a remote server (VPS/EC2)
- Parameterize the app with more `group_vars`
- Add a staging vs production environment split
- Integrate Molecule for Ansible role testing
- Add Slack or webhook notification on deploy

---

# License

MIT License
