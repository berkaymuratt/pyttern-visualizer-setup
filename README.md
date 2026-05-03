# PYTTERN — Usability Test Environment

This folder contains everything you need to run **PYTTERN** (backend + web UI) on
your own machine for the usability test. The images are already built and
published on Docker Hub, so you only need Docker installed — no source code, no
build step.

---

## 1. Prerequisites

Install **Docker Desktop** (Windows / macOS) or **Docker Engine + Docker Compose**
(Linux).

- Windows: <https://www.docker.com/products/docker-desktop/>
- macOS: <https://www.docker.com/products/docker-desktop/>
- Linux: <https://docs.docker.com/engine/install/>

After installation, verify it works:

```bash
docker --version
docker compose version
```

Make sure Docker Desktop is **running** before going further.

---

## 2. Pick the right Compose file for your CPU architecture

There are two Compose files in this folder. The right one depends on your
**CPU architecture**, not your operating system.

| File                          | Use this if your CPU is …                              | Image tag |
| ----------------------------- | ------------------------------------------------------ | --------- |
| `docker-compose.amd64.yml`    | **Intel / AMD** (a.k.a. `x86_64`)                      | `amd64`   |
| `docker-compose.arm64.yml`    | **ARM** (a.k.a. `aarch64`) — incl. Apple Silicon Macs  | `arm64`   |

### How to find out which one you have

**Windows**
- Press `Win + R`, type `cmd`, press Enter, then run:
  ```bash
  echo %PROCESSOR_ARCHITECTURE%
  ```
  - `AMD64` → use `docker-compose.amd64.yml`
  - `ARM64` → use `docker-compose.arm64.yml`

**macOS**
- Click the Apple menu → *About This Mac*.
  - "Apple M1 / M2 / M3 / M4 …" → use `docker-compose.arm64.yml`
  - "Intel …" → use `docker-compose.amd64.yml`

**Linux**
- Run in a terminal:
  ```bash
  uname -m
  ```
  - `x86_64` → use `docker-compose.amd64.yml`
  - `aarch64` or `arm64` → use `docker-compose.arm64.yml`

> Most Windows PCs and most Linux desktops/servers are `amd64`.
> Most Macs sold since late 2020 are `arm64` (Apple Silicon).

---

## 3. Start PYTTERN

Open a terminal **inside this `Usability-Test-Env` folder**, then run the
command for your architecture:

**Intel / AMD (`amd64`)**
```bash
docker compose -f docker-compose.amd64.yml up -d
```

**ARM / Apple Silicon (`arm64`)**
```bash
docker compose -f docker-compose.arm64.yml up -d
```

The first run will download the images (~a few hundred MB) and may take a
couple of minutes. Subsequent runs start in seconds.

When it's done, open your browser at:

**<http://localhost:5173>**

That's it — the app is ready for the usability test.

---

## 4. Stopping PYTTERN

In the same folder, run the matching command:

**Intel / AMD (`amd64`)**
```bash
docker compose -f docker-compose.amd64.yml down
```

**ARM / Apple Silicon (`arm64`)**
```bash
docker compose -f docker-compose.arm64.yml down
```

---

## 5. Updating to the latest image

If a new version is announced, pull the latest images and restart:

```bash
docker compose -f <your-compose-file> pull
docker compose -f <your-compose-file> up -d
```

Replace `<your-compose-file>` with the filename you used in step 3.

---

## 6. What's inside

- **Frontend** (React/Vite) — served on `http://localhost:5173`
- **Backend** (Python) — served on `http://localhost:5001`
- A local `./sessions` folder is created automatically and mounted into the
  backend container. It stores your test sessions so they persist across
  restarts.

The frontend container is configured to talk to the backend container over the
internal Docker network (`API_URL=http://backend:5001`), so you don't need to
change anything.

---

## 7. Troubleshooting

**Port already in use (`5173` or `5001`)**
Something else on your machine is using the port. Either stop that process or
change the host-side port in the compose file (e.g. `"5174:5173"`) and open the
new port in the browser.

**`docker: command not found` or `docker compose` not recognized**
Docker Desktop is not installed or not running. Install it (step 1) and start
it.

**Image pull is very slow / stuck**
Check your internet connection and retry:
```bash
docker compose -f <your-compose-file> pull
```

**"no matching manifest for linux/… in the manifest list entries"**
You picked the wrong Compose file for your CPU. Re-read step 2 and switch to
the other file.

**App loads but nothing happens / API errors**
Check that both containers are running:
```bash
docker compose -f <your-compose-file> ps
```
Both `pyttern-frontend` and `pyttern-backend` should show as `running`.
View backend logs with:
```bash
docker compose -f <your-compose-file> logs backend
```

---

## 8. Clean up after the test

Remove the containers and the downloaded images entirely:

```bash
docker compose -f <your-compose-file> down
docker rmi berkaymuratt/pyttern-frontend:amd64 berkaymuratt/pyttern-backend:amd64
# or replace amd64 with arm64 if that's what you used
```

Thank you for participating in the usability test!
