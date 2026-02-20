# Hatch Docker Builds

A collection of custom Docker images for various development and operations tools, built on Amazon Linux 2023.

## Overview

This repository contains Dockerfiles and configurations for building custom container images with specific versions of commonly used development tools. All images are designed to work together and are based on Amazon Linux 2023 for consistency with AWS environments.

## Available Images

### 📦 Ansible
**Location:** `ansible/`

A custom Python container for Ansible with MySQL support and AWS CLI integration.

**Features:**
- Amazon Linux 2023.10 base
- Ansible 13.3.0
- Python 3.13 + Python 3.9 (system)
- AWS CLI with boto/boto3/botocore
- MariaDB client utilities
- PyMySQL for database tasks

**Quick Start:**
```bash
# On Windows
docker run -v ${PWD}:/work ansible:2.7.1 test.yml

# On Linux/Mac
docker run -t -v $(pwd):/work:ro -v ~/.ssh:/root/.ssh --rm ansible:ansible-12.1.0
```

See [ansible/README.md](ansible/README.md) for more details.

---

### ☕ Java
**Location:** `java/`

A container with Azul Zulu JDK for Java development.

**Features:**
- Amazon Linux 2023.10 base
- Azul Zulu Java 25.0.2 (JDK 25.32.17-ca)
- Essential development tools (wget, tar, vim, openssh, etc.)

**Environment Variables:**
- `JAVA_HOME`: `/usr/local/java`
- Java binaries are in PATH

See [java/Readme.md](java/Readme.md) for more details.

---

### 🔧 Jenkins
**Location:** `jenkins/`

A comprehensive CI/CD container with Jenkins, Maven, and Ansible.

**Features:**
- Based on custom Java image (Java 25.0.2)
- Jenkins 2.541.1
- Maven 3.9.12
- Ansible 13.3.0
- Python 3.13 with uv 0.9.28
- Timezone: America/Los_Angeles

**Volumes:**
- `/var/jenkins_home` - Jenkins data directory
- `/usr/local/maven/conf` - Maven configuration (for custom settings.xml)

**Ports:**
- `8080` - Jenkins web interface

See [jenkins/README.md](jenkins/README.md) for more details.

---

### 📊 Nagios
**Location:** `nagios/`

A custom Nagios monitoring container with AWS integration.

**Features:**
- Based on jasonrivers/nagios:4.5.7
- Boto/boto3/botocore for AWS integration
- Vim editor included
- Timezone: America/Los_Angeles
- Custom UID (3002) for nagios user

See [nagios/README.md](nagios/README.md) for more details.

---

## Building Images

To build any of the images:

```bash
cd <image-directory>
docker build -t <your-tag> .
```

Example:
```bash
cd ansible
docker build -t my-ansible:latest .
```

## Repository Structure

```
.
├── README.md                 # This file
├── ansible/
│   ├── Dockerfile
│   ├── README.md
│   ├── test.yml
│   └── missingPackages.txt
├── java/
│   ├── Dockerfile
│   └── Readme.md
├── jenkins/
│   ├── Dockerfile
│   └── README.md
└── nagios/
    ├── Dockerfile
    └── README.md
```

## Contributing

Each image directory contains its own README with specific usage instructions and configuration details.

## Maintainer

Ken DeLong <kenwdelong@gmail.com>

## License

See individual image directories for licensing information.