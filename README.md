# Project Zomboid Dedicated Server (Docker-Compose Template)

<p align="center">
  <img src="https://github.com/user-attachments/assets/71027cd0-3748-4606-b1e2-6c4f43afee8e" alt="Xomano HackerSpace Project Zomboid Server"/>
</p>

Spin up a **Project Zomboid** multiplayer server on any Linux box (or Windows WSL) with two commands – no Docker expertise required.

---

## 1. Quick Start

```bash
# 1. grab the files
git clone https://github.com/GGontijo/project-zomboid-server-docker.git
cd temp-pz

# 2. copy the example environment and edit it
cp .env.example .env        # then open .env in any text editor

# 3. launch the server
docker compose up -d        # first start downloads ~2 GB, be patient
```

Watch the console until you see `*** SERVER STARTED ***`:

```bash
docker compose logs -f
```

Now connect from Project Zomboid using **your-server-ip : 16261**.

---

## 2. Folder Layout

| Path | Purpose |
|------|---------|
| `docker-compose.yml` | Declares the PZ service, ports & volumes. |
| `.env` | Basic settings you **must** adjust (timezone, passwords). |
| `steamcmd/` | SteamCMD cache – makes future updates quick. |
| `serverfiles/` | World, mods, logs – **persistent data**. |
| `.template_serverfiles/` | Example config. |

The template drops these into `serverfiles/Server/`:

* `servertest.ini` – main server settings  
* `servertest_SandboxVars.lua` – sandbox rules  
* `servertest_spawnregions.lua` – spawn points  

Restart the container after editing any of them.

---

## 3. Environment Variables (`.env`)

| Key | Meaning | Example |
|-----|---------|---------|
| `TZ` | Container timezone | `America/Cuiaba` |
| `GAME_ID` | Steam ID for PZ (don’t change) | `380870` |
| `ADMIN_PWD` | **In-game admin password** | `MyS3curePass!` |
| `UID` / `GID` | Set if Docker runs as an unprivileged user | leave as-is |

> **Important:** Placeholder passwords `123`, `CHANGEME`, `CHANGE_ME` exist in `serverfiles/Server/servertest.ini`.  
> Change at least:
>
> * `Password`  – server join password  
> * `RCONPassword` – remote-console password  
> * any other obvious secrets

---

## 4. Copy Your Local “Host” Settings

1. In game: **Host → Create new multiplayer**, tweak everything → **Save**.  
2. Exit PZ and open **`C:\Users\<you>\Zomboid\Server\`** (Windows) or **`~/.zomboid/Server/`** (Linux/macOS).  
3. Copy every `servertest*` file into `serverfiles/Server/`, overwriting.  
4. Run `docker compose restart`.

Your Docker server now mirrors the in-game settings.

---

## 5. Ports

| Purpose | Container | Host | Proto |
|---------|-----------|------|-------|
| Game traffic | 16261 | 16261 | UDP |
| Additional players | 16262-16326 | 16262-16326 | UDP |
| RCON | 27015 | 27015 | TCP |

Already mapped in `docker-compose.yml`; just forward them in your router/hosting panel.

---

## 6. Updating

```bash
docker compose pull         # 1. grab latest image
docker compose down         # 2. stop old container
docker compose up -d        # 3. start again (auto-patches game files)
```

World and configs persist in `serverfiles/`.

---

## 7. Troubleshooting

| Symptom | Fix |
|---------|-----|
| Friends can’t join | Verify port forwarding via <https://canyouseeme.org>. |
| Mods don’t load | Add Workshop IDs to `WorkshopItems=` **and** mod names to `Mods=` in `servertest.ini`. |
| Server stuck on “DOWNLOADING” | First boot can be long; wait or restart if frozen > 30 min. |
| Wrong timezone in logs | Check `TZ=` in `.env`. |

---

Happy surviving! 🧟
