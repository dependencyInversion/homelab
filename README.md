# homelab

## 🚀 Ansible Setup: Deploy a Docker Compose App from GitHub

This Ansible playbook automates the deployment of a Docker Compose application from a **public GitHub repository**.
It performs the following steps:

* Installs Docker Engine and the Compose v2 plugin
* Clones a public GitHub repository containing a `docker-compose.yml`
* Creates a systemd service to **automatically start the app on boot**

---

### 🧱 Prerequisites

1. **Control machine** with:

   * Ansible ≥ 2.12 installed
   * SSH access to your target host

2. **Target host** (Ubuntu/Debian recommended):

   * Accessible via SSH
   * `sudo` privileges
   * Internet access (to install Docker and clone the repo)

---

### 📁 Project Structure

```
ansible/
├─ inventory
├─ site.yml
└─ roles/
   └─ compose_app/
      ├─ defaults/main.yml
      ├─ tasks/main.yml
      ├─ templates/compose-app.service.j2
      └─ handlers/main.yml
```

---

### ⚙️ Configuration

#### `inventory`

Define your target host(s):

```ini
[servers]
myhost ansible_host=YOUR.SERVER.IP ansible_user=YOUR_USER
```

#### `roles/compose_app/defaults/main.yml`

Adjust these variables as needed:

```yaml
compose_repo_url: "https://github.com/owner/repo.git"
compose_repo_version: "main"
compose_app_dir: "/opt/compose-app"
compose_service_name: "compose-app"
compose_run_user: "{{ ansible_user | default('ubuntu') }}"
compose_file: "docker-compose.yml"
compose_env_file: ".env"
compose_env_vars: {}
```

---

### ▶️ Deployment Steps

#### 1. Install Ansible (on your control machine)

```bash
sudo apt update && sudo apt install -y ansible git
```

#### 2. Clone this Ansible project

```bash
git clone https://github.com/YOUR_USERNAME/ansible-compose-deploy.git
cd ansible-compose-deploy
```

#### 3. Update configuration

Edit:

* `inventory` → target host & user
* `defaults/main.yml` → repository URL, compose directory, etc.

#### 4. Run the playbook

```bash
ansible-playbook -i inventory site.yml
```

#### 5. Verify deployment

```bash
systemctl status compose-app
docker ps
```

#### 6. Test auto-start on reboot

Reboot the server:

```bash
sudo reboot
```

Then check:

```bash
docker ps
```

Your Compose app should be up and running automatically.

---

### 🛠️ Maintenance

#### Update the GitHub repo & restart

```bash
ansible-playbook -i inventory site.yml
```

The playbook detects repo or unit changes and restarts the app automatically.

#### Manually manage the service

```bash
sudo systemctl restart compose-app
sudo systemctl stop compose-app
sudo systemctl disable compose-app
```

#### View logs

```bash
journalctl -u compose-app -n 100 -f
```

---

### 🧩 Notes

* Uses Docker Compose v2 (`docker compose`, not `docker-compose`).
* Works best on Ubuntu 20.04+ or Debian 11+.
* To use private repos, configure SSH keys and change `compose_repo_url` to an SSH URL.
* You can encrypt sensitive values (like `.env` variables) with **Ansible Vault**.

---

### 📜 License

MIT — feel free to adapt and reuse.