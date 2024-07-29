# Automated Deployment and Configuration with Ansible for Boilerplates

This project involves setting up an automated deployment and configuration system for a Golang boilerplate application using Ansible. The following steps outline the process, ensuring a comprehensive setup that includes PostgreSQL installation, application deployment, and Nginx configuration.

## Table of Contents

1. [Overview](#overview)
2. [Configuration Details](#configuration-details)
3. [Ansible Playbook Tasks](#ansible-playbook-tasks)
4. [Running the Playbook](#running-the-playbook)
5. [Test Cases](#test-cases)

## Overview

This project aims to automate the deployment of a Golang boilerplate web application using Ansible. The setup includes:
- Cloning and deploying the application
- Installing necessary dependencies
- Configuring PostgreSQL
- Setting up Nginx as a reverse proxy
- Logging configuration


## Configuration Details

### Variables

- `repo_dir`: Directory to clone the repository (default: `/opt/stage_5b`)
- `repo_url`: URL of the repository to clone (default: `'https://github.com/hngprojects/hng_boilerplate_golang_web.git'`)
- `expected_owner`: Expected owner of the cloned directory (default: `hng`)
- `expected_group`: Expected group of the cloned directory (default: `hng`)

### Directories and Files

- `/var/secrets/pg_pw.txt`: File to store PostgreSQL credentials
- `/var/log/stage_5b`: Directory for application logs

## Ansible Playbook Tasks

### System Package Installation
- Install required system packages: `git`, `curl`, `wget`
- Install PostgreSQL and ensure the service is started and enabled

### PostgreSQL Setup
- Create a PostgreSQL database and user
- Save PostgreSQL credentials in `/var/secrets/pg_pw.txt`

### User and Directory Setup
- Create the `hng` user with sudo privileges
- Ensure the `/opt/stage_5b` directory exists and is owned by the `hng` user
- Clone the devops branch of the boilerplate repository into `/opt/stage_5b`
- Set the proper ownership of the cloned directory

### Go Installation
- Install Go and set up Go environment variables for the `hng` user

### Application Build and Run
- Build the application using Go
- Run the application and ensure it is listening on `127.0.0.1:3000`

### Nginx Configuration
- Install Nginx
- Configure Nginx to reverse proxy requests to the application
- Enable the Nginx configuration and restart the service

### Logging Configuration
- Ensure log files are owned by the `hng` user

### Application Status Check
- Verify if the application is running and print the status

## Running the Playbook

Run the Ansible playbook using the following command:

```sh
ansible-playbook main.yaml -b
```

Ensure the inventory.cfg file is correctly configured with the host `hng` and your host IP address.

Ensure the main.yaml, inventory.cfg and ansible.cfg files are in the same directory to run the command.

Example:
git clone https://github.com/Dayvid0063/hng_boilerplate_golang_web.git

cd ansible-book

Run the Ansible playbook command

Note: .gitignore my inventory.cfg file.

## Test Cases

1. **System Package Installation**
   - Verify `git`, `curl`, `wget`, and `postgresql` are installed:
     ```sh
     dpkg -l | grep -E 'git|curl|wget'
     psql --version
     ```
   - Ensure PostgreSQL service is started and enabled:
     ```sh
     sudo systemctl status postgresql
     ```

2. **PostgreSQL Setup**
   - Check if the `hngdevops` database and `dayvid` user are created:
     ```sh
     sudo -u postgres psql -c "\l" | grep hngdevops
     sudo -u postgres psql -c "\du" | grep dayvid
     ```
   - Verify `/var/secrets/pg_pw.txt` contains correct PostgreSQL credentials:
     ```sh
     sudo cat /var/secrets/pg_pw.txt
     ```

3. **User and Directory Setup**
   - Confirm the `hng` user is created with sudo privileges:
     ```sh
     id hng
     ```
   - Ensure `/opt/stage_5b` directory exists and is owned by the `hng` user:
     ```sh
     ls -ld /opt/stage_5b
     ```
   - Validate the repository is cloned into `/opt/stage_5b`:
     ```sh
     ls /opt/stage_5b
     ls -ld /opt/stage_5b
     ```

4. **Go Installation**
   - Check if Go is installed and the Go binary path is added to the `hng` user's profile:
     ```sh
     go version
     ```

5. **Application Build and Run**
   - Confirm the application is built successfully:
     ```sh
     ls /opt/stage_5b | grep app
     ```
   - Ensure the application is running on `127.0.0.1:3000`:
     ```sh
     curl -I http://127.0.0.1:3000
     ```

6. **Nginx Configuration**
   - Verify Nginx is installed and configured to reverse proxy to the application:
     ```sh
     nginx -v
     cat /etc/nginx/sites-available/stage_5b
     ```
   - Ensure the Nginx configuration is enabled and the service is restarted:
     ```sh
     sudo systemctl status nginx
     ls /etc/nginx/sites-enabled | grep default
     ```

7. **Logging Configuration**
   - Check if log files `/var/log/stage_5b/error.log` and `/var/log/stage_5b/out.log` exist and are owned by the `hng` user:
     ```sh
     ls -l /var/log/stage_5b/error.log
     ls -l /var/log/stage_5b/out.log
     ```
