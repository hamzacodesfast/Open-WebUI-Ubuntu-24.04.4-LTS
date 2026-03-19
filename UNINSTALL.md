# Open WebUI — Clean Delete Guide
> Completely remove Open WebUI and Docker from Ubuntu 24.04 LTS

---

## Step 1 — Remove the Open WebUI Container

```bash
sudo docker stop open-webui
sudo docker rm open-webui
```

---

## Step 2 — Remove Docker Volumes (Data & Models)

> ⚠️ **This deletes all your chats, settings, users, and downloaded AI models.**

```bash
sudo docker volume rm open-webui ollama
```

---

## Step 3 — Remove the Docker Image

```bash
sudo docker rmi ghcr.io/open-webui/open-webui:ollama
```

---

## Step 4 — Remove All Unused Docker Resources (Nuclear Option)

Removes all stopped containers, unused images, networks, and volumes:

```bash
sudo docker system prune -a --volumes
```

---

## Step 5 — Uninstall Docker Completely

### If installed via apt

```bash
sudo apt-get purge -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo apt-get autoremove -y
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
sudo rm -f /etc/apt/sources.list.d/docker.list
sudo rm -f /etc/apt/keyrings/docker.asc
```

### If installed via Snap

```bash
sudo snap remove docker
```

> **Note:** After snap removal, clear your shell's cached path:
> ```bash
> hash -r
> ```

---

## Step 6 — Verify Everything is Gone

```bash
docker --version
which docker
```

Expected output:
```
Command 'docker' not found
```

You're now on a clean slate. To reinstall, see [INSTALL.md](./INSTALL.md).
