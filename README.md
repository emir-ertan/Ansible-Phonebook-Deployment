# Ansible Phonebook Deployment

This project provides Ansible playbooks and roles, along with Terraform configurations, to automate the deployment of a simple Flask-based phonebook web application and its MySQL database on AWS EC2 instances.

## Project Structure

- `project/`: Contains the core Ansible playbooks and roles.
    - [`playbook.yml`](project/playbook.yml): The main playbook orchestrating the deployment of the database and web servers using defined roles. Includes commented-out sections for blue/green deployment and ALB switching.
    - `group_vars/`: Contains variable files specific to host groups.
        - [`servers.yml`](project/group_vars/servers.yml): Variables for server configurations.
    - `host_vars/`: Contains variable files specific to individual hosts.
        - [`blue_server_phonebook.yml`](project/host_vars/blue_server_phonebook.yml): Host-specific variables for the blue web server.
        - [`db_server_phonebook.yml`](project/host_vars/db_server_phonebook.yml): Host-specific variables for the database server.
        - [`green_server_phonebook.yml`](project/host_vars/green_server_phonebook.yml): Host-specific variables for the green web server.
    - `phonebook/`: Contains the phonebook application code and related files.
        - [`init.sql`](project/phonebook/init.sql): SQL script to initialize the phonebook database table.
        - [`phonebook-app.j2`](project/phonebook/phonebook-app.j2): Jinja2 template for the Flask application (likely used by Ansible).
        - [`phonebook-app.py`](project/phonebook/phonebook-app.py): The Flask web application code for the phonebook.
        - [`requirements.txt`](project/phonebook/requirements.txt): Python dependencies for the Flask application.
        - `templates/`: HTML templates used by the Flask application.
            - [`add-update.html`](project/phonebook/templates/add-update.html): Template for adding or updating phonebook entries.
            - [`delete.html`](project/phonebook/templates/delete.html): Template for deleting phonebook entries.
            - [`index.html`](project/phonebook/templates/index.html): Main template for displaying phonebook entries.
    - `roles/`: Contains reusable Ansible roles.
        - `alb-switch/`: Role for switching the Application Load Balancer (currently commented out in the main playbook).
            - [`tasks/main.yml`](project/roles/alb-switch/tasks/main.yml): Tasks for the ALB switching role.
            - (Other standard role directories: `defaults/`, `handlers/`, `meta/`, `tests/`, `vars/`)
        - `common/`: Common tasks applicable to multiple server types.
            - [`tasks/main.yml`](project/roles/common/tasks/main.yml): Common tasks.
        - `mysql/`: Role for deploying and configuring the MySQL database server.
            - [`files/init.sql`](project/roles/mysql/files/init.sql): Copy of the database initialization script.
            - [`handlers/main.yml`](project/roles/mysql/handlers/main.yml): Handlers for the MySQL role (e.g., restarting MySQL).
            - [`tasks/main.yml`](project/roles/mysql/tasks/main.yml): Tasks for installing and configuring MySQL.
            - [`vars/mysql_packages.yml`](project/roles/mysql/vars/mysql_packages.yml): Variables defining MySQL packages to install.
        - `web/`: Role for deploying and configuring the web application server.
            - [`tasks/main.yml`](project/roles/web/tasks/main.yml): Tasks for setting up the web server, deploying the Flask app, and installing dependencies.
            - (Other standard role directories: `defaults/`, `files/`, `handlers/`, `meta/`, `templates/`, `tests/`, `vars/`)
- `terraform-files/`: Contains Terraform configuration files for provisioning AWS infrastructure.
    - [`ansible.cfg`](terraform-files/ansible.cfg): Ansible configuration file.
    - [`lines-to-edit.txt .txt`](terraform-files/lines-to-edit.txt .txt): Notes file.
    - [`main.tf`](terraform-files/main.tf): Main Terraform file defining AWS resources (EC2 instances, Security Group, IAM roles). Includes commented-out sections for ALB and Route53.
    - [`myvars.auto.tfvars`](terraform-files/myvars.auto.tfvars): Example variable definitions for Terraform.
    - [`variable.tf`](terraform-files/variable.tf): Terraform variable definitions.

## Getting Started

1.  **Prerequisites:**
    *   Install Ansible and Terraform.
    *   Configure AWS credentials.
2.  **Provision Infrastructure:**
    *   Navigate to the `terraform-files/` directory.
    *   Update `myvars.auto.tfvars` with your AWS region, key pair name, and desired instance counts/types.
    *   Run `terraform init`.
    *   Run `terraform apply`.
3.  **Deploy Application:**
    *   Once Terraform has provisioned the EC2 instances, it will output the public IP of the control node.
    *   SSH into the control node.
    *   Navigate to the directory where the project files were copied (usually `/home/ubuntu/project`).
    *   Update the Ansible inventory file (`inventory.ini` created by Terraform) if necessary.
    *   Run the Ansible playbook: `ansible-playbook playbook.yml`

## Usage

*   Run `terraform apply` in `terraform-files/` to provision infrastructure.
*   Run `ansible-playbook playbook.yml` on the Ansible control node to deploy the application.

## Blue/Green Deployment (Commented Out)

The project structure and playbook include components for a blue/green deployment strategy using an AWS Application Load Balancer. These sections are currently commented out in the main playbook and Terraform files but can be enabled for zero-downtime deployments.
