# Introduction to Terraform

[:simple-terraform: Terraform](https://www.terraform.io/){target=_blank} is an open-source infrastructure-as-code (IaC) software tool created by [HashiCorp](https://www.hashicorp.com/){target=_blank}.

??? Info "What is Infrastructure-as-Code (IaC)?"

    "Infrastructure as code (IaC) is the process of managing and provisioning computer data centers through machine-readable definition files, rather than physical hardware configuration or interactive configuration tools." - [:simple-wikipedia: Wikipedia](https://en.wikipedia.org/wiki/Infrastructure_as_code){target=_blank}

    IaC tools allow you to manage infrastructure with configuration files rather than through a graphical user interface. IaC allows you to build, change, and manage your infrastructure in a safe, consistent, and repeatable way by defining resource configurations that you can version, reuse, and share. -- [:simple-terraform: Terraform Documentation](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/infrastructure-as-code){target=_blank}

## Overview

This basic tutorial will guide you through setting up a Terraform project and deploying virtual machines (VMs) as infrastructure on AWS Cloud.  

!!! success "Goals"

    :material-play: Understand orchestration for deployment to AWS Cloud

    :material-play: Understand the benefits of Terraform

    :material-play: Ability to perform basic deployments on AWS using Terraform

    :material-play: Ability to perform provisioning of deployed AWS resources through Terraform

    ??? Failure "Things we won't cover"

        :material-play: AWS 
        
        :material-play: All of Terraform's features

## Prerequisites

* Basic understanding of [AWS](https://aws.amazon.com/){target=_blank} and VMs

* [:simple-terraform: Terraform](https://developer.hashicorp.com/terraform/downloads){target=_blank} installed on your local machine or use a VM provided by the workshop

!!! success "Outcomes"

    By the end of this tutorial, you will 
    
    :material-play: started, stopped, and destroyed a Terraform deployment on an AWS Cloud

??? Info "Terminology"

    **:material-play: Ansible** - is a suite of software tools that enables infrastructure as code

    **:material-play: Deploy** - to create a cloud resource or software
   
    **:material-play: Infrastructure** - is the collection of hardware and software elements such as computing power, networking, storage, and virtualization resources needed to enable cloud computing

    **:material-play: Orchestration** - is the automated configuring, coordinating, and managing of computer systems and software

    **:material-play: Playbook** - are a list of tasks that automatically execute against a host

    **:material-play: Provision** - making changes to a VM including updating the operating system, installing software, adding configurations

    **:material-play: Terraform** - is an infrastructure as code tool that lets you build, change, and version cloud and on-prem resources safely and efficiently

## Cloning the repository

You can clone the repository with the code. We will use the `terraform-aws-itsummit2024` subdirectory for this exercise. You'll want to cd into that directory

```bash
git clone https://github.com/cyverse/cacao-example-templates.git
cd cacao-example-templates
cd terraform-aws-itsummit2024 # you want to cd into the directory without the '-cacao'
```

## :simple-terraform: Terraform installation

[:simple-terraform: Official Documentation](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli#install-terraform){target=_blank}

??? Tip "Windows Installation"

    Download [Terraform](https://developer.hashicorp.com/terraform/downloads) using the appropriate distribution for your OS

??? Tip "Mac OS X Installation"

    Instructions for Mac OS X installation

    If you're on OS X, you can use [brew](https://brew.sh/) to install with the following commands:

    ```bash
    # install terraform -- taken from https://learn.hashicorp.com/tutorials/terraform/install-cli
    brew tap hashicorp/tap && brew install hashicorp/tap/terraform

    # install ansible and jq (for processing terraform's statefile into an ansible inventory)
    brew install ansible jq
    ```

??? Tip "Linux Installation"

    Instructions for Ubuntu 22.04 installation

    ```bash
    wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
    sudo apt update && sudo apt install terraform
    ```

    Install Ansible & J Query

    ```bash
    sudo apt-add-repository ppa:ansible/ansible
    sudo apt update & sudo apt install -y ansible jq
    ```

Confirm installation:

```bash
terraform
```

Should output:

```bash
Usage: terraform [global options] <subcommand> [args]

The available commands for execution are listed below.
The primary workflow commands are given first, followed by
less common or more advanced commands.

Main commands:
  init          Prepare your working directory for other commands
  validate      Check whether the configuration is valid
  plan          Show changes required by the current configuration
  apply         Create or update infrastructure
  destroy       Destroy previously-created infrastructure

All other commands:
  console       Try Terraform expressions at an interactive command prompt
  fmt           Reformat your configuration in the standard style
  force-unlock  Release a stuck lock on the current workspace
  get           Install or upgrade remote Terraform modules
  graph         Generate a Graphviz graph of the steps in an operation
  import        Associate existing infrastructure with a Terraform resource
  login         Obtain and save credentials for a remote host
  logout        Remove locally-stored credentials for a remote host
  metadata      Metadata related commands
  output        Show output values from your root module
  providers     Show the providers required for this configuration
  refresh       Update the state to match remote systems
  show          Show the current state or a saved plan
  state         Advanced state management
  taint         Mark a resource instance as not fully functional
  test          Experimental support for module integration testing
  untaint       Remove the 'tainted' state from a resource instance
  version       Show the current Terraform version
  workspace     Workspace management

Global options (use these before the subcommand, if any):
  -chdir=DIR    Switch to a different working directory before executing the
                given subcommand.
  -help         Show this help output, or the help for a specified subcommand.
  -version      An alias for the "version" subcommand.
```

### Configuration files

Terraform code is written in HCL (Hashicorp Configuration Language), and its configuration files typically end in the `.tf` file extension. 

Configuration `.tf` files can either be split into multiple files or maintained in a single file. 

When using multiple files, it is up to your discretion what the file names are, or how many you decide to split it into.

#### File Organization

An example for file organization of a terraform project might involve:

```css
terraform-project/
├── main.tf
├── inputs.tf
```

**:simple-terraform: Main Configuration File (`main.tf`):** - contains the primary infrastructure resources and configurations for virtual machines, networks, and storage.

**:simple-terraform: Inputs File (`inputs.tf`):** - contains inputs variables to terraform

**:simple-terraform: Modules and Reusable Configurations:** - create separate `.tf` files for reusable modules and configurations. Reuse across multiple projects or within the same project on multiple VMs.

#### :simple-terraform: `terraform.tfvars`

 A `terraform.tfvars` file is used to define the values of input variables. It can also be renamed `*.auto.tfvars`.
 
 It serves as a convenient way to store and manage variable values that you don't want to hardcode in your `.tf` files or provide via command-line arguments. 
 
 By using a `terraform.tfvars` file, you can easily customize and update the variable values for different environments or scenarios.

 The file should contain key-value pairs, where the key is the variable name and the value is the corresponding variable value. 
 
 The syntax for defining variables in the `terraform.tfvars` file can be either HCL or JSON.

When you run `terraform apply`, Terraform will automatically load the values from the `terraform.tfvars` file if it exists in the working directory. 

You can also create multiple `.tfvars` files and specify which one to use by passing the `-var-file` flag when executing Terraform commands:

```bash
terraform apply -var-file="custom.tfvars"
```

Variables can also be passed directly into the terraform command-line using the `-var`. You can also combine the use of terraform.tfvars, *.auto.tfvars, and command line flags to set input variables; however, you should understand the [rules for variable precedence before doing so](https://developer.hashicorp.com/terraform/language/values/variables#variable-definition-precedence){target=_blank}.

### :simple-terraform: Intermediate directories and files

When `terraform apply` are executed, Terraform generates some new project files, notably

`.terraform/` - this directory will contain the `terraform-provider-AWS_version` and a `README.md`, `LICENCE`, and `CHANGELOG.md` 

`terraform.lock.hcl` 

`terraform.tfstate`

`terraform.tfstate.backup`

## :simple-terraform: Terraform Commands

### :simple-terraform: `init`

Now, you are ready to initialize the Terraform project

```bash
cd ~/terraform
terraform init
```

??? success "Expected Response"

    ```bash
    Initializing the backend...

    Initializing provider plugins...
    - Reusing previous version of terraform-provider-AWS/AWS from the dependency lock file
    - Using previously-installed terraform-provider-AWS/AWS v1.51.1

    Terraform has been successfully initialized!

    You may now begin working with Terraform. Try running "terraform plan" to see
    any changes that are required for your infrastructure. All Terraform commands
    should now work.

    If you ever set or change modules or backend configuration for Terraform,
    rerun this command to reinitialize your working directory. If you forget, other
    commands will detect it and remind you to do so if necessary.
    ```

### :simple-terraform: `validate`
`validate` - scans your terraform directory and reports any syntax errors in your terraform

```bash
terraform validate
```

??? success "Expected Response"

    ```bash
    Success! The configuration is valid.
    ```


### :simple-terraform: apply

Using `terraform apply` will output the changes that will occur to your cloud. You can review the changes and decide to continue. Using the flag `-auto-approve` will also output the changes that will occur but will continue with the execution of the terraform as though you entered `yes`.

```bash
terraform apply
```

??? success "Expected response"

    ```bash
    Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
      + create

    Terraform will perform the following actions:

      # AWS_compute_floatingip_associate_v2.terraform_floatip_ubuntu20[0] will be created
      + resource "AWS_compute_floatingip_associate_v2" "terraform_floatip_ubuntu20" {
          + floating_ip = (known after apply)
          + id          = (known after apply)
          + instance_id = (known after apply)
          + region      = (known after apply)
        }

      # AWS_compute_instance_v2.Ubuntu20[0] will be created
      + resource "AWS_compute_instance_v2" "Ubuntu20" {
          + access_ip_v4        = (known after apply)
          + access_ip_v6        = (known after apply)
          + all_metadata        = (known after apply)
          + all_tags            = (known after apply)
          + availability_zone   = (known after apply)
          + created             = (known after apply)
          + flavor_id           = (known after apply)
          + flavor_name         = "m3.tiny"
          + force_delete        = false
          + id                  = (known after apply)
          + image_id            = (known after apply)
          + image_name          = "Featured-Ubuntu20"
          + key_pair            = "tswetnam-terraform-key"
          + metadata            = {
              + "terraform_controlled" = "yes"
            }
          + name                = "container_camp_Ubuntu20_0"
          + power_state         = "active"
          + region              = (known after apply)
          + security_groups     = [
              + "default",
              + "terraform_ssh_ping",
            ]
          + stop_before_destroy = false
          + updated             = (known after apply)

          + network {
              + access_network = false
              + fixed_ip_v4    = (known after apply)
              + fixed_ip_v6    = (known after apply)
              + floating_ip    = (known after apply)
              + mac            = (known after apply)
              + name           = "auto_allocated_network"
              + port           = (known after apply)
              + uuid           = (known after apply)
            }
        }

      # AWS_compute_secgroup_v2.terraform_ssh_ping will be created
      + resource "AWS_compute_secgroup_v2" "terraform_ssh_ping" {
          + description = "Security group with SSH and PING open to 0.0.0.0/0"
          + id          = (known after apply)
          + name        = "terraform_ssh_ping"
          + region      = (known after apply)

          + rule {
              + cidr        = "0.0.0.0/0"
              + from_port   = -1
              + id          = (known after apply)
              + ip_protocol = "icmp"
              + self        = false
              + to_port     = -1
            }
          + rule {
              + cidr        = "0.0.0.0/0"
              + from_port   = 22
              + id          = (known after apply)
              + ip_protocol = "tcp"
              + self        = false
              + to_port     = 22
            }
        }

      # AWS_networking_floatingip_v2.terraform_floatip_ubuntu20[0] will be created
      + resource "AWS_networking_floatingip_v2" "terraform_floatip_ubuntu20" {
          + address    = (known after apply)
          + all_tags   = (known after apply)
          + dns_domain = (known after apply)
          + dns_name   = (known after apply)
          + fixed_ip   = (known after apply)
          + id         = (known after apply)
          + pool       = "public"
          + port_id    = (known after apply)
          + region     = (known after apply)
          + subnet_id  = (known after apply)
          + tenant_id  = (known after apply)
        }

      # AWS_networking_secgroup_rule_v2.http_rule will be created
      + resource "AWS_networking_secgroup_rule_v2" "http_rule" {
          + direction         = "ingress"
          + ethertype         = "IPv4"
          + id                = (known after apply)
          + port_range_max    = 80
          + port_range_min    = 80
          + protocol          = "tcp"
          + region            = (known after apply)
          + remote_group_id   = (known after apply)
          + remote_ip_prefix  = "0.0.0.0/0"
          + security_group_id = (known after apply)
          + tenant_id         = (known after apply)
        }

      # AWS_networking_secgroup_rule_v2.https_rule will be created
      + resource "AWS_networking_secgroup_rule_v2" "https_rule" {
          + direction         = "ingress"
          + ethertype         = "IPv4"
          + id                = (known after apply)
          + port_range_max    = 443
          + port_range_min    = 443
          + protocol          = "tcp"
          + region            = (known after apply)
          + remote_group_id   = (known after apply)
          + remote_ip_prefix  = "0.0.0.0/0"
          + security_group_id = (known after apply)
          + tenant_id         = (known after apply)
        }

      # AWS_networking_secgroup_rule_v2.service_rule will be created
      + resource "AWS_networking_secgroup_rule_v2" "service_rule" {
          + direction         = "ingress"
          + ethertype         = "IPv4"
          + id                = (known after apply)
          + port_range_max    = 8080
          + port_range_min    = 8080
          + protocol          = "tcp"
          + region            = (known after apply)
          + remote_group_id   = (known after apply)
          + remote_ip_prefix  = "0.0.0.0/0"
          + security_group_id = (known after apply)
          + tenant_id         = (known after apply)
        }

      # AWS_networking_secgroup_v2.terraform_tcp_1 will be created
      + resource "AWS_networking_secgroup_v2" "terraform_tcp_1" {
          + all_tags    = (known after apply)
          + description = "Security group with TCP open to 0.0.0.0/0"
          + id          = (known after apply)
          + name        = "terraform_tcp_1"
          + region      = (known after apply)
          + tenant_id   = (known after apply)
        }

    Plan: 8 to add, 0 to change, 0 to destroy.

    Changes to Outputs:
      + floating_ip_ubuntu20 = [
          + null,
        ]

    Do you want to perform these actions?
      Terraform will perform the actions described above.
      Only 'yes' will be accepted to approve.
    ```

You should be prompted

```bash
 Do you want to perform these actions?
      Terraform will perform the actions described above.
      Only 'yes' will be accepted to approve.
```

??? success "Expected Response after choosing 'yes' "
    
    ```bash

    AWS_networking_secgroup_v2.terraform_tcp_1: Creating...
    AWS_networking_floatingip_v2.terraform_floatip_ubuntu20[0]: Creating...
    AWS_compute_secgroup_v2.terraform_ssh_ping: Creating...
    AWS_compute_instance_v2.Ubuntu20[0]: Creating...
    AWS_networking_secgroup_v2.terraform_tcp_1: Creation complete after 1s [id=4f0ab1d5-ca29-4f60-9a65-c38d2380719c]
    AWS_networking_secgroup_rule_v2.service_rule: Creating...
    AWS_networking_secgroup_rule_v2.https_rule: Creating...
    AWS_networking_secgroup_rule_v2.http_rule: Creating...
    AWS_networking_secgroup_rule_v2.http_rule: Creation complete after 1s [id=2d7d6f4c-a3db-48ca-996a-1fe0b18f4f41]
    AWS_networking_secgroup_rule_v2.service_rule: Creation complete after 2s [id=ead48696-4aae-436f-a12b-8bf3ceac253a]
    AWS_networking_secgroup_rule_v2.https_rule: Creation complete after 2s [id=20225b99-fd2a-4a05-912c-61056fa21e3b]
    AWS_compute_secgroup_v2.terraform_ssh_ping: Creation complete after 4s [id=1af38fb4-1d41-4ac2-9c9f-91fc6bbaa72f]
    AWS_networking_floatingip_v2.terraform_floatip_ubuntu20[0]: Creation complete after 7s [id=6af630bc-9c16-450c-b16f-0483294d8d75]
    AWS_compute_instance_v2.Ubuntu20[0]: Still creating... [10s elapsed]
    AWS_compute_instance_v2.Ubuntu20[0]: Creation complete after 15s [id=afc53214-d2bd-45d5-b8b5-b6886976df8c]
    AWS_compute_floatingip_associate_v2.terraform_floatip_ubuntu20[0]: Creating...
    AWS_compute_floatingip_associate_v2.terraform_floatip_ubuntu20[0]: Creation complete after 2s [id=149.165.168.217/afc53214-d2bd-45d5-b8b5-b6886976df8c/]

    Apply complete! Resources: 8 added, 0 changed, 0 destroyed.

    Outputs:

    floating_ip_ubuntu20 = [
      "149.165.168.217",
    ]
    ```

**Congratulations!** You now have a running VM, you can check its status on Horizon, or try to connect to it over `ssh`

The `floating_ip_ubuntu20` should be the new VM's IP address (yours will be different than this example).

`ssh ubuntu@<IP-ADDRESS>` 

Make sure that 

### :simple-terraform: `refresh`
`refresh` - will update (refresh) the current terraform state

This command is sometimes needed if haven't touched your terraform code in a while or there's a chance that your or other individuals manage your resources using horizon, exosphere, or the cli.

```bash
terraform refresh
```

??? success "Expected Response"

    ```bash
    
    ```


### :simple-terraform: `show`
`show` - will show the current terraform state, as stored in your state file

This command is useful when you 

```bash
terraform show
```

??? success "Expected Response"

    ```bash
    
    ```


### :simple-terraform: destroy

When you are ready to complete the use of your VMs or your deployment, you can `destroy` the project.

```bash
terraform destroy
```

??? success "Expected Response"

    ```bash
    AWS_compute_secgroup_v2.terraform_ssh_ping: Refreshing state... [id=1af38fb4-1d41-4ac2-9c9f-91fc6bbaa72f]
    AWS_compute_instance_v2.Ubuntu20[0]: Refreshing state... [id=afc53214-d2bd-45d5-b8b5-b6886976df8c]
    AWS_networking_secgroup_v2.terraform_tcp_1: Refreshing state... [id=4f0ab1d5-ca29-4f60-9a65-c38d2380719c]
    AWS_networking_floatingip_v2.terraform_floatip_ubuntu20[0]: Refreshing state... [id=6af630bc-9c16-450c-b16f-0483294d8d75]
    AWS_networking_secgroup_rule_v2.service_rule: Refreshing state... [id=ead48696-4aae-436f-a12b-8bf3ceac253a]
    AWS_networking_secgroup_rule_v2.https_rule: Refreshing state... [id=20225b99-fd2a-4a05-912c-61056fa21e3b]
    AWS_networking_secgroup_rule_v2.http_rule: Refreshing state... [id=2d7d6f4c-a3db-48ca-996a-1fe0b18f4f41]
    AWS_compute_floatingip_associate_v2.terraform_floatip_ubuntu20[0]: Refreshing state... [id=149.165.168.217/afc53214-d2bd-45d5-b8b5-b6886976df8c/]

    Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
    symbols:
      - destroy

    Terraform will perform the following actions:

      # AWS_compute_floatingip_associate_v2.terraform_floatip_ubuntu20[0] will be destroyed
      - resource "AWS_compute_floatingip_associate_v2" "terraform_floatip_ubuntu20" {
          - floating_ip = "149.165.168.217" -> null
          - id          = "149.165.168.217/afc53214-d2bd-45d5-b8b5-b6886976df8c/" -> null
          - instance_id = "afc53214-d2bd-45d5-b8b5-b6886976df8c" -> null
          - region      = "IU" -> null
        }

      # AWS_compute_instance_v2.Ubuntu20[0] will be destroyed
      - resource "AWS_compute_instance_v2" "Ubuntu20" {
          - access_ip_v4        = "10.0.24.205" -> null
          - all_metadata        = {
              - "terraform_controlled" = "yes"
            } -> null
          - all_tags            = [] -> null
          - availability_zone   = "nova" -> null
          - created             = "2023-03-23 23:09:42 +0000 UTC" -> null
          - flavor_id           = "1" -> null
          - flavor_name         = "m3.tiny" -> null
          - force_delete        = false -> null
          - id                  = "afc53214-d2bd-45d5-b8b5-b6886976df8c" -> null
          - image_id            = "27424df1-c3ea-4c4a-ad8b-6ea9a476f6f8" -> null
          - image_name          = "Featured-Ubuntu20" -> null
          - key_pair            = "tswetnam-terraform-key" -> null
          - metadata            = {
              - "terraform_controlled" = "yes"
            } -> null
          - name                = "container_camp_Ubuntu20_0" -> null
          - power_state         = "active" -> null
          - region              = "IU" -> null
          - security_groups     = [
              - "default",
              - "terraform_ssh_ping",
            ] -> null
          - stop_before_destroy = false -> null
          - tags                = [] -> null
          - updated             = "2023-03-23 23:09:52 +0000 UTC" -> null

          - network {
              - access_network = false -> null
              - fixed_ip_v4    = "10.0.24.205" -> null
              - mac            = "fa:16:3e:16:6e:8f" -> null
              - name           = "auto_allocated_network" -> null
              - uuid           = "e4ff98ff-d29a-48f4-a95b-88ebd6b0662f" -> null
            }
        }

      # AWS_compute_secgroup_v2.terraform_ssh_ping will be destroyed
      - resource "AWS_compute_secgroup_v2" "terraform_ssh_ping" {
          - description = "Security group with SSH and PING open to 0.0.0.0/0" -> null
          - id          = "1af38fb4-1d41-4ac2-9c9f-91fc6bbaa72f" -> null
          - name        = "terraform_ssh_ping" -> null
          - region      = "IU" -> null

          - rule {
              - cidr        = "0.0.0.0/0" -> null
              - from_port   = -1 -> null
              - id          = "611e1c0a-edc1-462d-8e1a-5f8a72c4d968" -> null
              - ip_protocol = "icmp" -> null
              - self        = false -> null
              - to_port     = -1 -> null
            }
          - rule {
              - cidr        = "0.0.0.0/0" -> null
              - from_port   = 22 -> null
              - id          = "ce99a035-5df2-4131-ba90-268d045d0ff3" -> null
              - ip_protocol = "tcp" -> null
              - self        = false -> null
              - to_port     = 22 -> null
            }
        }

      # AWS_networking_floatingip_v2.terraform_floatip_ubuntu20[0] will be destroyed
      - resource "AWS_networking_floatingip_v2" "terraform_floatip_ubuntu20" {
          - address   = "149.165.168.217" -> null
          - all_tags  = [] -> null
          - fixed_ip  = "10.0.24.205" -> null
          - id        = "6af630bc-9c16-450c-b16f-0483294d8d75" -> null
          - pool      = "public" -> null
          - port_id   = "71e9ae50-e281-4f98-afab-f1b6c1932806" -> null
          - region    = "IU" -> null
          - tags      = [] -> null
          - tenant_id = "db016a81886b4f918705e5dee2b24298" -> null
        }

      # AWS_networking_secgroup_rule_v2.http_rule will be destroyed
      - resource "AWS_networking_secgroup_rule_v2" "http_rule" {
          - direction         = "ingress" -> null
          - ethertype         = "IPv4" -> null
          - id                = "2d7d6f4c-a3db-48ca-996a-1fe0b18f4f41" -> null
          - port_range_max    = 80 -> null
          - port_range_min    = 80 -> null
          - protocol          = "tcp" -> null
          - region            = "IU" -> null
          - remote_ip_prefix  = "0.0.0.0/0" -> null
          - security_group_id = "4f0ab1d5-ca29-4f60-9a65-c38d2380719c" -> null
          - tenant_id         = "db016a81886b4f918705e5dee2b24298" -> null
        }

      # AWS_networking_secgroup_rule_v2.https_rule will be destroyed
      - resource "AWS_networking_secgroup_rule_v2" "https_rule" {
          - direction         = "ingress" -> null
          - ethertype         = "IPv4" -> null
          - id                = "20225b99-fd2a-4a05-912c-61056fa21e3b" -> null
          - port_range_max    = 443 -> null
          - port_range_min    = 443 -> null
          - protocol          = "tcp" -> null
          - region            = "IU" -> null
          - remote_ip_prefix  = "0.0.0.0/0" -> null
          - security_group_id = "4f0ab1d5-ca29-4f60-9a65-c38d2380719c" -> null
          - tenant_id         = "db016a81886b4f918705e5dee2b24298" -> null
        }

      # AWS_networking_secgroup_rule_v2.service_rule will be destroyed
      - resource "AWS_networking_secgroup_rule_v2" "service_rule" {
          - direction         = "ingress" -> null
          - ethertype         = "IPv4" -> null
          - id                = "ead48696-4aae-436f-a12b-8bf3ceac253a" -> null
          - port_range_max    = 8080 -> null
          - port_range_min    = 8080 -> null
          - protocol          = "tcp" -> null
          - region            = "IU" -> null
          - remote_ip_prefix  = "0.0.0.0/0" -> null
          - security_group_id = "4f0ab1d5-ca29-4f60-9a65-c38d2380719c" -> null
          - tenant_id         = "db016a81886b4f918705e5dee2b24298" -> null
        }

      # AWS_networking_secgroup_v2.terraform_tcp_1 will be destroyed
      - resource "AWS_networking_secgroup_v2" "terraform_tcp_1" {
          - all_tags    = [] -> null
          - description = "Security group with TCP open to 0.0.0.0/0" -> null
          - id          = "4f0ab1d5-ca29-4f60-9a65-c38d2380719c" -> null
          - name        = "terraform_tcp_1" -> null
          - region      = "IU" -> null
          - tags        = [] -> null
          - tenant_id   = "db016a81886b4f918705e5dee2b24298" -> null
        }

    Plan: 0 to add, 0 to change, 8 to destroy.

    Changes to Outputs:
      - floating_ip_ubuntu20 = [
          - "149.165.168.217",
        ] -> null

    Do you really want to destroy all resources?
      Terraform will destroy all your managed infrastructure, as shown above.
      There is no undo. Only 'yes' will be accepted to confirm.

      Enter a value: yes
    ```


??? success "Expected Response after choosing 'yes' "

    ```bash
    AWS_networking_secgroup_rule_v2.service_rule: Destroying... [id=ead48696-4aae-436f-a12b-8bf3ceac253a]
    AWS_networking_secgroup_rule_v2.https_rule: Destroying... [id=20225b99-fd2a-4a05-912c-61056fa21e3b]
    AWS_compute_secgroup_v2.terraform_ssh_ping: Destroying... [id=1af38fb4-1d41-4ac2-9c9f-91fc6bbaa72f]
    AWS_networking_secgroup_rule_v2.http_rule: Destroying... [id=2d7d6f4c-a3db-48ca-996a-1fe0b18f4f41]
    AWS_compute_floatingip_associate_v2.terraform_floatip_ubuntu20[0]: Destroying... [id=149.165.168.217/afc53214-d2bd-45d5-b8b5-b6886976df8c/]
    AWS_compute_floatingip_associate_v2.terraform_floatip_ubuntu20[0]: Destruction complete after 3s
    AWS_networking_floatingip_v2.terraform_floatip_ubuntu20[0]: Destroying... [id=6af630bc-9c16-450c-b16f-0483294d8d75]
    AWS_compute_instance_v2.Ubuntu20[0]: Destroying... [id=afc53214-d2bd-45d5-b8b5-b6886976df8c]
    AWS_networking_secgroup_rule_v2.https_rule: Destruction complete after 6s
    AWS_networking_floatingip_v2.terraform_floatip_ubuntu20[0]: Destruction complete after 6s
    AWS_networking_secgroup_rule_v2.service_rule: Still destroying... [id=ead48696-4aae-436f-a12b-8bf3ceac253a, 10s elapsed]
    AWS_networking_secgroup_rule_v2.http_rule: Still destroying... [id=2d7d6f4c-a3db-48ca-996a-1fe0b18f4f41, 10s elapsed]
    AWS_networking_secgroup_rule_v2.service_rule: Destruction complete after 11s
    AWS_compute_instance_v2.Ubuntu20[0]: Still destroying... [id=afc53214-d2bd-45d5-b8b5-b6886976df8c, 10s elapsed]
    AWS_compute_instance_v2.Ubuntu20[0]: Destruction complete after 10s
    AWS_networking_secgroup_rule_v2.http_rule: Destruction complete after 16s
    AWS_networking_secgroup_v2.terraform_tcp_1: Destroying... [id=4f0ab1d5-ca29-4f60-9a65-c38d2380719c]
    AWS_networking_secgroup_v2.terraform_tcp_1: Destruction complete after 9s
    AWS_compute_secgroup_v2.terraform_ssh_ping: Destroying... [id=1af38fb4-1d41-4ac2-9c9f-91fc6bbaa72f]
    AWS_compute_secgroup_v2.terraform_ssh_ping: Destruction complete after 2s

    Destroy complete! Resources: 8 destroyed.
    ```


## Troubleshooting

??? failure "My deployment cannot authenticate to the provider"

    Make sure that you run `source *-openrc.sh` to provide your AWS credentials. 

    Also make sure that you are using the correct `~/.ssh/id_rsa.pub` key and that it has been [injected to AWS](#create-an-ssh-keypair-with-AWS)

??? failure "`terraform destroy` did not complete" 

    there was an error like:

    ```bash
    Error: Error deleting AWS_compute_secgroup_v2 1af38fb4-1d41-4ac2-9c9f-91fc6bbaa72f: Bad request with: [DELETE https://js2.jetstream-cloud.org:8774/v2.1/os-security-groups/1af38fb4-1d41-4ac2-9c9f-91fc6bbaa72f], error message: {"badRequest": {"code": 400, "message": "Security Group 1af38fb4-1d41-4ac2-9c9f-91fc6bbaa72f in use.\nNeutron server returns request_ids: ['req-e3d1548c-9d4c-4445-9642-952977a44853']"}}
    ```

    Try running `terraform destroy` one more time, occasionally Horizon times out while destroying deployments.

