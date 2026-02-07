# VProfile Local Multi-Tier DevOps Stack (Vagrant)

## Overview

This project demonstrates how to provision and automate a real-world multi-tier web application stack locally using Vagrant, VirtualBox, and shell provisioning.

The goal of this project is Infrastructure as Code (IaC), not application development:

- Safely reproducing complex production-like environments
- Practicing service integration and dependency management
- Enabling local R&D without touching real servers

A complete application stack is brought up with a single command:

    vagrant up

---

## Architecture

### Application Stack (Runtime)

| Layer | Service | Purpose |
|-----|--------|--------|
| Web | Nginx | Entry point / reverse proxy |
| Application | Apache Tomcat | Java application server |
| Messaging | RabbitMQ | Message broker (learning complexity) |
| Cache | Memcached | Database caching |
| Database | MySQL (MariaDB) | Persistent data store |

### Request Flow

    Browser
      ↓
    Nginx (web01)
      ↓
    Tomcat (app01)
      ↓
    MySQL (db01)
      ↘
       Memcached (mc01)
      ↘
       RabbitMQ (rmq01)

---

### Infrastructure Stack

| Component | Role |
|---------|-----|
| Vagrant | Infrastructure as Code |
| VirtualBox | Local hypervisor |
| Shell scripts | Automated provisioning |
| vagrant-hostmanager | Hostname resolution |

All services communicate using hostnames instead of hardcoded IPs.

---

## Project Structure

    vprofile-project/
    └── vagrant/
        ├── Manual_provisioning_WinMacIntel/
        │   └── Vagrantfile
        │
        └── Automated_provisioning_WinMacIntel/
            ├── Vagrantfile
            ├── mysql.sh
            ├── memcache.sh
            ├── rabbitmq.sh
            ├── tomcat.sh
            ├── nginx.sh
            └── extras/
                └── tomcat_ubuntu.sh

### Design Notes

- Manual provisioning was used for learning and validation
- Automated provisioning is the final Infrastructure as Code deliverable
- Experimental or alternate scripts are clearly separated

---

## Why Manual Provisioning First?

Before automation, the stack was set up manually to:

- Understand service dependencies
- Validate correct startup order
- Observe real runtime behavior
- Confirm application functionality
- Identify and fix integration issues

Once validated, all steps were converted into repeatable automation scripts.

This mirrors real DevOps workflows:

    Understand → Validate → Automate

---

## Automated Provisioning (IaC)

Each virtual machine provisions its service automatically using shell scripts.

| VM | Script | Responsibility |
|---|------|----------------|
| db01 | mysql.sh | Database install, hardening, schema |
| mc01 | memcache.sh | Cache setup |
| rmq01 | rabbitmq.sh | Message broker |
| app01 | tomcat.sh | App server, build, deploy |
| web01 | nginx.sh | Reverse proxy |

### One-Command Setup

    cd vagrant/Automated_provisioning_WinMacIntel
    vagrant up

No manual SSH  
No post-setup commands

---

## Validation

### Infrastructure Validation

- All virtual machines are created automatically
- All provisioning scripts execute during vagrant up

### Runtime Validation

- Application is accessed via Nginx using VM IP
- Login succeeds using database-backed credentials
- Repeated page loads demonstrate caching behavior (Memcached)

This confirms end-to-end service integration.

---

## How to Reproduce

### Prerequisites

- Oracle VM VirtualBox
- Vagrant
- Git
- Vagrant plugin:

    vagrant plugin install vagrant-hostmanager

### Steps

    git clone <your-repo-url>
    cd vagrant/Automated_provisioning_WinMacIntel
    vagrant up

Access the application in a browser:

    http://192.168.56.11

---

## Learning Outcomes

This project demonstrates:

- Multi-VM infrastructure design
- Service dependency management
- Infrastructure as Code principles
- Automated provisioning with shell scripts
- Local environment parity with production systems
- Safe experimentation using destroy and recreate workflows

---

## Attribution

### Application Source

The Java web application used in this project is an open-source sample application:

    https://github.com/hkhcoder/vprofile-project

- Used strictly for learning and infrastructure automation
- No ownership is claimed over the application code
- All credit belongs to the original author

The focus of this repository is DevOps automation, not application development.

---

## Future Enhancements

- Configuration management using Ansible
- CI/CD integration
- AWS migration (EC2, RDS, ALB)
- Environment-specific configuration
- Secrets management
- Monitoring and logging

---

## Author

Built as a DevOps learning and automation project to practice real-world infrastructure patterns and workflows.

