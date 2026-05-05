# PYTTERN — Usability Test Setup

> **Participating in the test?** Start here: [TEST-GUIDE.md](TEST-GUIDE.md) — it explains the two tasks and where to find all the files you need.

This folder contains everything you need to run **PYTTERN** (web UI + backend) on
your own machine for the usability test. The images are already built and
published on Docker Hub, so you only need Docker installed — no source code, no
build step.

---

## 1. Prerequisites

### Check if Docker is already installed

Open a terminal and run:

```bash
docker --version
docker compose version
```

If both commands print a version number, Docker is ready — **skip to step 2**.

If you see a "command not found" error (or Docker Desktop is installed but not running), follow the steps below.

### Install Docker (only if needed)

Install **Docker Desktop** (Windows / macOS) or **Docker Engine + Docker Compose**
(Linux):

- Windows: <https://www.docker.com/products/docker-desktop/>
- macOS: <https://www.docker.com/products/docker-desktop/>
- Linux: <https://docs.docker.com/engine/install/>

After installation, **start Docker Desktop** (Windows / macOS) and verify it works by running the two commands above again.

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

**<http://localhost:5173/>**

That's it — the app is ready for the usability test.

---

## 4. Troubleshooting

**Port already in use (`5173`)**
Something else on your machine is using the port. Either stop that process or
change the host-side port in the compose file (e.g. `"5174:5173"`) and open the
new port in the browser (http://localhost:5174/).

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


## 5. Stopping PYTTERN (After the Test)

At the end of usability test, run the matching command in the same folder:

**Intel / AMD (`amd64`)**
```bash
docker compose -f docker-compose.amd64.yml down
```

**ARM / Apple Silicon (`arm64`)**
```bash
docker compose -f docker-compose.arm64.yml down
```

---

## 6. Clean up after the test

Remove the containers and the downloaded images entirely:

```bash
docker compose -f <your-compose-file> down
docker rmi berkaymuratt/pyttern-frontend:amd64 berkaymuratt/pyttern-backend:amd64
# or replace amd64 with arm64 if that's what you used
```

Thank you for participating in the usability test!
