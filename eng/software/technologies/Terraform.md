# Terraform
- Infrastructure as Code
- Programmatically provision and manage the underlying infrastructure resources of your app
	- enables you to track infrastructure state
	- works with many cloud providers
- Infrastructure includes your servers, networks, DBs, etc...
- Define the desired final state of your infrastructure and Terraform figures out how to reach it
- Immutable infrastructure
	- components are never modified after they've been deployed
	- to make any changes, you create entirely new components and replace old ones
	- this and code versioning prevents configuration drift (manual changes that are undocumented)
## Resource
- resource represents a single infrastructure component managed by Terraform
- e.g.: AWS EC2 instance
- Modules are containers for multiple resources used together
## State file
- tracks the current state of your infrastructure
- Terraform compares this state file to your desired state and determines the necessary actions for updates
## Terraform Syntax (HCL)
- HashiCorp Configuration Language
- **Blocks:** Terraform configuration is organized into blocks. Common blocks include:
    - `terraform`: Configuration for Terraform itself (optional).
    - `provider`: Defines how Terraform interacts with a specific cloud platform or service (required)
    - `resource`: Defines an infrastructure element you want to manage (e.g., virtual machine, database)
    - `data`: Fetches data from external sources to use within your configuration
    - `output`: Outputs values from your configuration for use elsewhere.
- **Comments:** Start with `#` for single-line or enclose multi-line comments in `/* ... */`
- **Variables:** Use variables to hold reusable values within your configuration. Defined using `variable "name" { type = string }`
- **Arguments:** Used to provide specific data to resources or providers. Defined within the resource block (e.g., `name = "webserver"`)
## Workspace
- Workspaces allow you to create multiple isolated instances of your infrastructure within a single configuration, each with its own state
## Basic workflow
- **init:** Initializes a working directory containing your Terraform configuration files and downloads relevant plugins.
- **plan:** Terraform creates an execution plan showing the changes required to reach your desired infrastructure state.
- **apply:** Terraform executes the plan, either creating new resources, modifying existing ones, or destroying them as needed.
## Docker integration
- can use Terraform to provision the infrastructure your docker containers will run