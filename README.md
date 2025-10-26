# DevOps Task 3: Infrastructure as Code (IaC) using Terraform

## 📋 Project Overview

This project demonstrates **Infrastructure as Code (IaC)** using **Terraform** to provision a local Docker container. The objective is to automate the creation and management of Docker infrastructure through code, making it reproducible, version-controlled, and easily manageable.

### What is Infrastructure as Code (IaC)?
Infrastructure as Code is a practice that allows you to define and manage your infrastructure using code and configuration files. Instead of manually setting up servers, networks, and other infrastructure components, you write code that describes your desired infrastructure state.

### Why Terraform?
Terraform is a popular IaC tool that:
- Uses a declarative approach (you describe what you want, not how to get it)
- Supports multiple cloud providers and services
- Maintains state to track infrastructure changes
- Provides a consistent workflow across different environments
- Enables infrastructure versioning and collaboration

## 🛠️ Technologies Used

- **Terraform** - Infrastructure as Code tool
- **Docker** - Containerization platform
- **Docker Provider for Terraform** - Terraform plugin for Docker
- **Operating System** - Windows 10/11
- **Shell** - PowerShell

## 📋 Prerequisites

Before running this project, ensure you have the following software installed:

### Required Software:
1. **Terraform** (version 1.0+)
   - Download from: https://www.terraform.io/downloads
   - Verify installation: `terraform --version`

2. **Docker Desktop** (latest version)
   - Download from: https://www.docker.com/products/docker-desktop
   - Verify installation: `docker --version`
   - Ensure Docker Desktop is running

3. **Git** (optional, for version control)
   - Download from: https://git-scm.com/downloads

## 📁 Project Structure

```
task4/
├── main.tf                    # Main Terraform configuration file
├── terraform.tfstate          # Terraform state file (auto-generated)
├── terraform.tfstate.backup   # Backup of previous state
├── .terraform/                # Terraform working directory (auto-generated)
├── .terraform.lock.hcl        # Provider version lock file (auto-generated)
└── README.md                  # This documentation file
```

## 🚀 Step-by-Step Process

### Step 1: Install Required Software
1. **Install Terraform:**
   - Download Terraform from the official website
   - Extract and add to your system PATH
   - Verify: `terraform --version`

2. **Install Docker Desktop:**
   - Download and install Docker Desktop
   - Start Docker Desktop application
   - Verify: `docker --version`

### Step 2: Initialize Terraform Directory
Navigate to your project directory and initialize Terraform:
```bash
cd task4
terraform init
```

### Step 3: Write Terraform Code
The project uses the following Terraform configuration in `main.tf`:

```hcl
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0.2"
    }
  }
}

provider "docker" {}

# Pull nginx image from Docker Hub
resource "docker_image" "nginx_image" {
  name         = "nginx:latest"
  keep_locally = false
}

# Create Docker container using that image
resource "docker_container" "nginx_container" {
  name  = "terraform-nginx"
  image = docker_image.nginx_image.image_id

  ports {
    internal = 80
    external = 8081
  }
}
```

### Step 4: Execute Terraform Commands

#### 4.1 Initialize Terraform
```bash
terraform init
```
**Expected Output:**
```
Initializing the backend...
Initializing provider plugins...
- Finding kreuzwerker/docker versions matching "~> 3.0.2"...
- Installing kreuzwerker/docker v3.0.2...
Terraform has been successfully initialized!
```

#### 4.2 Plan the Infrastructure
```bash
terraform plan
```
**Expected Output:**
```
Terraform will perform the following actions:

  # docker_image.nginx_image will be created
  + resource "docker_image" "nginx_image" {
      + id           = (known after apply)
      + image_id     = (known after apply)
      + name         = "nginx:latest"
      + repo_digest  = (known after apply)
    }

  # docker_container.nginx_container will be created
  + resource "docker_container" "nginx_container" {
      + name  = "terraform-nginx"
      + image = (known after apply)
      + ports = [
          + {
              + external = 8081
              + internal = 80
            },
        ]
    }

Plan: 2 to add, 0 to change, 0 to destroy.
```

#### 4.3 Apply the Configuration
```bash
terraform apply
```
**Expected Output:**
```
Terraform will perform the actions described above.
Only 'yes' will be accepted to approve.

Enter a value: yes

docker_image.nginx_image: Creating...
docker_image.nginx_image: Still creating... [10s elapsed]
docker_image.nginx_image: Creation complete after 12s [id=sha256:...]
docker_container.nginx_container: Creating...
docker_container.nginx_container: Creation complete after 2s [id=...]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

### Step 5: Verify Container Creation
Check if the container is running:
```bash
docker ps
```

**Expected Output:**
```
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                  NAMES
abc123def456   nginx     "/docker-entrypoint.…"   2 minutes ago   Up 2 minutes   0.0.0.0:8081->80/tcp   terraform-nginx
```

Test the nginx server:
- Open browser and navigate to: `http://localhost:8081`
- You should see the nginx welcome page

### Step 6: Destroy Infrastructure
When you're done, clean up the resources:
```bash
terraform destroy
```

**Expected Output:**
```
Terraform will perform the following actions:

  # docker_container.nginx_container will be destroyed
  - resource "docker_container" "nginx_container" {
      - name  = "terraform-nginx" -> null
      - image = "sha256:..." -> null
    }

  # docker_image.nginx_image will be destroyed
  - resource "docker_image" "nginx_image" {
      - name = "nginx:latest" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted.

  Enter a value: yes

docker_container.nginx_container: Destroying... [id=...]
docker_container.nginx_container: Destruction complete after 1s
docker_image.nginx_image: Destroying... [id=...]
docker_image.nginx_image: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.
```

## 📊 Output / Logs Section

### Sample Console Outputs

#### Terraform Init Output:
```
Initializing the backend...
Initializing provider plugins...
- Finding kreuzwerker/docker versions matching "~> 3.0.2"...
- Installing kreuzwerker/docker v3.0.2...
- Installed kreuzwerker/docker v3.0.2 (signed by a HashiCorp partner, key ID 0E6C4F4B)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/cli/plugins/signing.html

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!
```

#### Docker PS Output:
```
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                  NAMES
a1b2c3d4e5f6   nginx     "/docker-entrypoint.…"   5 minutes ago   Up 5 minutes   0.0.0.0:8081->80/tcp   terraform-nginx
```

## ⚠️ Common Errors & Fixes

### 1. Docker Not Running
**Error:** `Cannot connect to the Docker daemon at unix:///var/run/docker.sock`
**Solution:** 
- Start Docker Desktop application
- Wait for Docker to fully start (green icon in system tray)
- Verify with: `docker ps`

### 2. Provider Plugin Issue
**Error:** `Failed to query available provider packages`
**Solution:**
```bash
# Clear Terraform cache and reinitialize
rm -rf .terraform
terraform init
```

### 3. State Lock Issue
**Error:** `Error acquiring the state lock`
**Solution:**
```bash
# Force unlock (use with caution)
terraform force-unlock <lock-id>
```

### 4. Port Already in Use
**Error:** `Port 8081 is already allocated`
**Solution:**
- Change the external port in `main.tf`
- Or stop the container using that port: `docker stop <container-name>`

### 5. Permission Denied
**Error:** `Permission denied while trying to connect to the Docker daemon`
**Solution:**
- Ensure your user is in the docker group (Linux)
- Run Docker Desktop as administrator (Windows)

## ❓ Interview Questions (and Short Answers)

### 1. What is Infrastructure as Code (IaC)?
**Answer:** IaC is a practice of managing and provisioning infrastructure through code and configuration files, rather than through manual processes. It enables automation, version control, and consistency across environments.

### 2. What is Terraform?
**Answer:** Terraform is an open-source IaC tool by HashiCorp that allows you to define, provision, and manage infrastructure using a declarative configuration language called HCL (HashiCorp Configuration Language).

### 3. What is a state file?
**Answer:** The state file (`terraform.tfstate`) is a JSON file that stores the current state of your infrastructure. It tracks which resources exist, their configuration, and dependencies. Terraform uses this to determine what changes need to be made.

### 4. Difference between `terraform plan` and `terraform apply`?
**Answer:** 
- `terraform plan` shows you what changes Terraform will make without actually making them (dry run)
- `terraform apply` executes the planned changes and actually creates/modifies/destroys resources

### 5. How to handle secrets in Terraform?
**Answer:** 
- Use environment variables: `export TF_VAR_password="secret"`
- Use Terraform Cloud/Enterprise for secure variable storage
- Use external secret management systems (AWS Secrets Manager, Azure Key Vault)
- Never commit secrets to version control

### 6. What is a Terraform provider?
**Answer:** A provider is a plugin that Terraform uses to interact with APIs of different services (AWS, Azure, Docker, etc.). It translates Terraform configuration into API calls.

### 7. What is the difference between `terraform destroy` and `terraform apply -destroy`?
**Answer:** Both destroy infrastructure, but `terraform destroy` is a dedicated command for destruction, while `terraform apply -destroy` is the apply command with a destroy flag.

## 🎯 Conclusion

### What I Learned:
- **Infrastructure Automation:** How to automate Docker container provisioning using Terraform
- **State Management:** Understanding how Terraform tracks infrastructure state
- **Provider Integration:** Working with the Docker provider for Terraform
- **Best Practices:** Proper project structure and workflow for IaC projects

### Benefits of Terraform for Infrastructure Automation:
1. **Reproducibility:** Same infrastructure can be created consistently across environments
2. **Version Control:** Infrastructure changes are tracked and can be reviewed
3. **Collaboration:** Team members can work together on infrastructure changes
4. **Rollback Capability:** Easy to revert to previous infrastructure states
5. **Documentation:** Infrastructure is self-documenting through code
6. **Cost Management:** Easy to destroy resources when not needed
7. **Multi-Cloud Support:** Same tool can manage resources across different cloud providers

## 📤 GitHub Submission Info

### How to Clone and Run This Project:

1. **Clone the repository:**
   ```bash
   git clone <repository-url>
   cd task4
   ```

2. **Prerequisites:** Ensure Docker Desktop and Terraform are installed and running

3. **Initialize and Apply:**
   ```bash
   terraform init
   terraform plan
   terraform apply
   ```

4. **Verify:** Check running containers with `docker ps`

5. **Clean up:** Run `terraform destroy` when done

### Project Files:
- `main.tf` - Contains the complete Terraform configuration
- `terraform.tfstate` - State file (auto-generated, do not edit manually)
- `README.md` - This documentation

### Notes:
- The project creates an nginx container accessible on port 8081
- All resources are local Docker containers
- State files are included for demonstration purposes
- In production, consider using remote state storage

---

**Author:** [Your Name]  
**Date:** [Current Date]  
**Task:** DevOps Task 3 - Infrastructure as Code with Terraform
