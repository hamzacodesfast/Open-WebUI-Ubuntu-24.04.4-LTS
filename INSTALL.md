# Open WebUI — Clean Install Guide
> Tested on Ubuntu 24.04.4 LTS

---

## Prerequisites

- Ubuntu 24.04 LTS
- `sudo` privileges
- Internet connection
- ~4GB free disk space

---

## Step 1 — Install Docker

### Add Docker's GPG Key and Repository

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
```

### Install Docker Engine

```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Start and Enable Docker

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

### (Optional) Run Docker Without sudo

```bash
sudo usermod -aG docker $USER
newgrp docker
```

> **Note:** Log out and back in for the group change to take full effect.

### Verify Docker Works

```bash
sudo docker run hello-world
```

You should see a `Hello from Docker!` success message.

---

## Step 2 — Install Open WebUI

```bash
sudo docker run -d -p 3000:8080 \
  -v ollama:/root/.ollama \
  -v open-webui:/app/backend/data \
  --name open-webui --restart always \
  ghcr.io/open-webui/open-webui:ollama
```

> **Note:** The image is ~3GB. Download may take a few minutes.

### What These Flags Do

| Flag | Purpose |
|------|---------|
| `-d` | Run in background (detached) |
| `-p 3000:8080` | Map port 3000 on host to 8080 in container |
| `-v ollama:/root/.ollama` | Persist downloaded AI models |
| `-v open-webui:/app/backend/data` | Persist chats, settings, users |
| `--restart always` | Auto-restart on reboot |

---

## Step 3 — Access Open WebUI

Open your browser and go to:

```
http://localhost:3000
```

> **Note:** On some systems it may be on `http://localhost:8080` instead. Try both.

The first account you register becomes the **admin account**.

---

## Managing the Container

```bash
# Check status
sudo docker ps

# Stop
sudo docker stop open-webui

# Start
sudo docker start open-webui

# View logs
sudo docker logs open-webui

# Update to latest version
sudo docker pull ghcr.io/open-webui/open-webui:ollama
sudo docker stop open-webui && sudo docker rm open-webui
# Then re-run the docker run command above — your data is preserved
```

---

## Troubleshooting

See [UNINSTALL.md](./UNINSTALL.md) if you need to start from scratch.

| Error | Fix |
|-------|-----|
| `Cannot connect to the Docker daemon` | `sudo systemctl reset-failed docker.service && sudo systemctl start docker.service` |
| `Unit docker.service not found` | Docker not installed — run Step 1 from the beginning |
| `Unit docker.socket failed — Device or resource busy` | Snap conflict — run `sudo snap remove docker` first |
| `docker.sock: no such file or directory` | Daemon stopped — run `sudo systemctl start docker.service` |
| `Start request repeated too quickly` | Run `sudo systemctl reset-failed docker.service` to clear the lock |
| Open WebUI not loading on port 3000 | Try `http://localhost:8080` instead |
