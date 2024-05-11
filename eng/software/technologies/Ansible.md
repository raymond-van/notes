# Ansible
- Configuration as Code
- Configuration management, app deployments
	- e.g.: software installation/updates, setting config files
- Outline step-by-step instructions on how to configure existing systems
- Terraform provisions infrastructure, Ansible configures the software on the infrastructure
- Agentless
	- doesn't require any software installed on the systems / nodes you manage 
	- uses SSH for communication
## Playbooks
- YAML files that define the desired state of your systems and the tasks needed to achieve it
- Idempotent 
	- a playbook can run multiple times on a system, bringing it to the desired state without causing unintended side effects
- can create dynamic configuration files using templates
## Modules
- Ansible comes with hundreds of built-in modules that handle common tasks like:
	- Installing packages (`apt`, `yum`)
	- Managing files and directories (`file`)
	- Modifying system settings (`sysctl`)
	- Interacting with services (`service`)
	- Deploying applications
## Inventories
- list the hosts (nodes) Ansible will manage
## Basic Workflow
1. **Write Playbook:** Create a YAML playbook describing the tasks that need to be performed on your managed nodes
2. **Define Inventory:** Create a list of hosts Ansible should target
3. **Run the Playbook:** Use the `ansible-playbook` command to execute tasks. Ansible connects to target hosts, performs the tasks in order, and reports results

## Terraform + Ansible Workflow
- **Provision with Terraform:** Terraform defines the foundation of your infrastructure (VMs, networks, load balancers, etc.) and executes the changes using `terraform apply`.
- **Retrieve Information with Terraform Outputs:** Terraform can expose important details about the provisioned resources, such as IP addresses or hostnames, as outputs.
- **Invoke Ansible:** Utilize one of the following methods to trigger Ansible playbooks:
    - **Local-exec provisioner:** Execute Ansible from within Terraform itself (convenient but less scalable for large deployments).
    - **Remote-exec provisioner:** SSH into remote instances provisioned by Terraform and run Ansible playbooks for in-place configuration.
    - **CI/CD pipeline:** Have your CI/CD tool chain Terraform's execution and then trigger an Ansible job to execute playbooks.