# Thesis Project: Infrastructure as Code & Network Resilience (Demo Version)

**Student:** [Your Name]  
**Project Title:** Evaluating the Efficacy of Infrastructure as Code in SOHO Environments  

---

## 1. Overview
This repository contains the practical implementation of the thesis. While the production environment is designed to be provisioned via **Terraform** on a bare-metal **Proxmox** hypervisor (Linux), this specific configuration is a **Portable Demo Artifact**.

It has been adapted to run on **Docker Desktop** (Windows/macOS/Linux) to facilitate evaluation without requiring a dedicated hypervisor.

### Key Differences in Demo Mode
* **Networking:** Uses `bridge` mode instead of `host` mode to ensure compatibility with Windows/Mac networking stacks.
* **Ports:** Standard ports (DNS `53`, HTTP `80`) are remapped to high-range ports (e.g., `5353`, `8000`) to prevent conflicts with the host operating system.
* **WireGuard:** Runs in userspace mode (`WIREGUARD_GO=true`) to avoid kernel dependency issues on non-Linux hosts.

---

## 2. Prerequisites
* **Docker Desktop** (Installed and running)
* **Git** (Optional, if cloning the repo)

---

## 3. Quick Start Guide
Follow these steps to deploy the application stack locally.

1.  **Navigate to the docker directory:**
    Open your terminal or command prompt and enter the folder containing the project files.
    ```bash
    cd path/to/thesis-project/docker
    ```

2.  **Launch the Stack:**
    Run the following command to start the services using the demo configuration file.
    ```bash
    docker compose -f docker-compose.demo.yml up -d
    ```

3.  **Verify Status:**
    Ensure all containers are running:
    ```bash
    docker compose -f docker-compose.demo.yml ps
    ```

---

## 4. Service Access & Credentials
Once the stack is running, you can access the services via the following URLs:

| Service | Function | URL | Username | Password |
| :--- | :--- | :--- | :--- | :--- |
| **Pi-hole** | DNS Filter / Ad Block | `http://localhost:8080/admin` | N/A | `admin` |
| **Nginx Proxy** | Reverse Proxy Manager | `http://localhost:8181` | `admin@example.com` | `changeme` |
| **n8n** | Workflow Automation | `http://localhost:5678` | `admin` | `thesis_demo` |
| **Unbound** | Recursive Resolver | *(Backend Service Only)* | N/A | N/A |

---

## 5. Testing the Logic
To verify the stack is functioning as intended:

1.  **Test DNS Resolution:**
    You can force a DNS query through the containerized stack (mapped to port 5353):
    ```bash
    # On Windows (Command Prompt)
    nslookup -P 5353 google.com 127.0.0.1
    
    # On macOS/Linux
    dig @127.0.0.1 -p 5353 google.com
    ```
    *Result:* You should see a successful answer. Check the Pi-hole Query Log to see the request appear.

2.  **Test Ad Blocking:**
    Query a known ad domain:
    ```bash
    nslookup -P 5353 doubleclick.net 127.0.0.1
    ```
    *Result:* The query should return `0.0.0.0`, confirming the block list is active.

---

## 6. Shutdown
To stop the project and clean up resources:

```bash
docker compose -f docker-compose.demo.yml down