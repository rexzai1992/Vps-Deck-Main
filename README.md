# VPSDeck

**Your VPS, managed from a browser. No terminal required for everyday work.**

VPSDeck is a self-hosted control panel for people who run apps on a VPS but do not want every deploy, `.env` edit, file change, Docker restart, or server check to turn into an SSH session.

It turns a raw Linux server into a visual app workspace: deploy projects from GitHub, manage project files, edit environment variables, monitor ports and resources, inspect Docker Compose stacks, watch Ollama models, and update the panel itself from one secure web interface.

> **Live demo:** [vps.izzul.xyz](https://vps.izzul.xyz)
> **Login:** `demo` / `demo`

---

## Product overview

VPSDeck is built for solo founders, small teams, agencies, AI builders, and non-technical administrators who want the power and low cost of a VPS without living in the command line.

The product focuses on three ideas:

- **Simple Mode first:** daily tasks stay safe, visual, and scoped to registered projects.
- **Advanced Mode when needed:** unlock full-server administration only after password confirmation and with audit logging.
- **Own your stack:** VPSDeck runs on your server as a Go binary with SQLite, so you keep control of your files, apps, and deployment history.

Instead of replacing your VPS provider, VPSDeck sits on top of your existing server and makes it easier to operate.

---

## What you can do with VPSDeck

| Workflow | What VPSDeck provides |
|---|---|
| **Deploy an app** | Clone from GitHub, choose a branch, edit `.env`, deploy latest commits, and rebuild Docker Compose projects |
| **Manage project files** | Browse, upload, download, rename, move, copy, delete, ZIP download, and edit text files in the browser |
| **Edit configuration** | Use a key-value `.env` editor with secret masking instead of editing hidden files over SSH |
| **Monitor the server** | View CPU, RAM, disk, uptime, kernel, OS, load, public IP, local IP, and live ports |
| **Operate Docker projects** | Auto-discover Compose stacks, import them as projects, and run safe deploy actions |
| **Review deployments** | Track deployment history, commit IDs, output, status, and project-level deploy events |
| **Work with private repos** | Connect GitHub OAuth, browse repositories, and store tokens encrypted at rest |
| **Watch AI workloads** | Monitor local or remote Ollama health, version, installed models, loaded models, and VRAM usage |
| **Update VPSDeck** | Detect new commits on the configured branch and restart the panel through the installed updater |
| **Audit important actions** | Log logins, deploys, file changes, Advanced Mode entry, and sensitive operations |

---

## Who it is for

VPSDeck is useful when you:

- host multiple small apps on one VPS;
- need a simpler way to deploy GitHub projects;
- want a safer file manager for project folders;
- run Docker Compose apps and want a browser control surface;
- manage apps for clients or teammates who should not need SSH;
- run local AI tools such as Ollama and want visibility from the same panel;
- want a lightweight alternative to heavyweight hosting panels.

It is especially aimed at practical day-to-day VPS operation: ship the app, check what is running, fix the config, inspect logs, and recover quickly when something breaks.

---

## Main features

| Area | Features |
|---|---|
| **Dashboard** | Live CPU, RAM, disk, uptime, kernel, OS, public IP, local IP, and load averages |
| **Projects** | Register existing app folders, import Docker Compose stacks, and auto-detect project type |
| **Deployments** | GitHub clone/import, fast-forward-only deploys, dirty-tree protection, Docker Compose rebuilds |
| **File Manager** | Explorer-style project files, drag-and-drop upload, multi-select, rename, ZIP download, text editor |
| **Environment** | Key-value `.env` editor with secret masking and save-and-deploy workflow |
| **Ports** | Live TCP/UDP port monitor with project-port matching |
| **Ollama** | Health, version, installed model, loaded model, and playground support |
| **Docker** | Compose stack discovery and safe project import |
| **Updates** | Branch watcher, update banner, one-click update, and systemd restart path |
| **Security** | Authentication, CSRF protection, rate limiting, path validation, encrypted GitHub tokens |
| **Advanced Mode** | Full-filesystem Explorer protected by password re-confirmation, timeout, and audit logs |

---

## Product model

VPSDeck has two operating modes.

### Simple Mode

Simple Mode is the default. It keeps normal work scoped to registered projects and exposes the actions most people need every day:

- dashboard and health checks;
- project cards and project detail pages;
- GitHub deploys;
- project-scoped file management;
- `.env` editing;
- ports, logs, domains, backups, and settings.

### Advanced Mode

Advanced Mode is for deeper VPS administration. It can unlock broader filesystem access and higher-risk operations after password re-confirmation. Entry and exit are audited, and the mode times out after inactivity.

The goal is to keep dangerous operations available to the owner without putting them in the main path for everyday users.

---

## Screenshots and demo

Visit [vps.izzul.xyz](https://vps.izzul.xyz) to explore the full UI live.

Demo login:

```text
Username: demo
Password: demo
```

Demo mode uses realistic fake data, disables write actions, and is safe to expose publicly.

---

## Quick start (local)

```bash
git clone https://github.com/rexzai1992/Vps-Deck-Main.git
cd Vps-Deck-Main

VPSDECK_ADMIN_USERNAME=admin \
VPSDECK_ADMIN_PASSWORD='ChangeThisStrong123' \
go run ./cmd/server
```

Open [http://127.0.0.1:8080](http://127.0.0.1:8080).

After the first admin account is created, the bootstrap username and password environment variables are no longer needed.

```bash
go test ./...
go build ./cmd/server
```

Config lives in `configs/config.yaml`. A fully annotated example is available at `configs/config.example.yaml`.

---

## Install on Ubuntu VPS

The production installer creates a system user, builds the binary, configures systemd, and sets up an Nginx virtual host.

```bash
git clone https://github.com/rexzai1992/Vps-Deck-Main.git /tmp/vpsdeck-src
sudo bash /tmp/vpsdeck-src/scripts/install.sh
```

See [docs/INSTALL.md](docs/INSTALL.md) for DNS setup, HTTPS, rollback, and uninstall instructions.

---

## Demo mode

Run VPSDeck with read-only fake data:

```bash
VPSDECK_DEMO_MODE=true go run ./cmd/server
```

Demo mode auto-creates a `demo` / `demo` login and seeds the UI with fake projects, deployments, ports, system metrics, and Ollama models. Write actions are disabled.

---

## Deploy from GitHub

1. Open **Add Project -> Deploy from GitHub**.
2. Enter a public HTTPS repository URL, branch, and project name.
3. VPSDeck clones the project into `apps_dir`.
4. Edit the `.env` file in the browser.
5. Run **Deploy latest** from the project page.

Deployments are fast-forward-only, refuse dirty tracked files, record commit IDs and output, and can run `docker compose up -d --build` when configured.

---

## GitHub OAuth for private repos

```bash
export VPSDECK_GITHUB_ENABLED=true
export VPSDECK_GITHUB_CLIENT_ID=your_client_id
export VPSDECK_GITHUB_CLIENT_SECRET=your_client_secret
export VPSDECK_GITHUB_CALLBACK_URL=https://your-domain/integrations/github/callback
export VPSDECK_GITHUB_TOKEN_KEY=$(openssl rand -base64 32)
```

Create the OAuth App at **GitHub -> Settings -> Developer settings -> OAuth Apps**.

GitHub tokens are AES-encrypted at rest and never appear in repository URLs or shell commands.

---

## Ollama monitor

VPSDeck can monitor a local or remote Ollama instance:

```bash
VPSDECK_OLLAMA_BASE_URL=http://another-server:11434 go run ./cmd/server
```

It shows the Ollama version, installed models, loaded models, and VRAM usage. For remote Ollama, use a private network, VPN, or authenticated reverse proxy.

---

## Self-update

VPSDeck watches its configured GitHub branch and shows an update banner when a new commit is available.

On production installs, clicking **Update now** rebuilds and restarts the panel through the root-owned systemd updater installed by `scripts/install.sh`.

---

## Security model

VPSDeck is designed as a server administration tool, so security is part of the product design:

- bcrypt password hashing;
- SHA-256 session tokens in HttpOnly cookies;
- CSRF double-submit cookie protection;
- login rate limiting;
- strict Simple Mode path-root validation;
- path traversal and symlink escape protection;
- GitHub tokens encrypted at rest;
- repository URLs with embedded credentials rejected;
- sensitive actions recorded in the audit log;
- Advanced Mode protected by password confirmation and timeout.

---

## Tech stack

- **Go 1.25** - single binary, no application runtime dependency
- **Gin** - HTTP router and middleware
- **html/template** - server-rendered UI
- **SQLite** - local database storage
- **modernc.org/sqlite** - pure-Go SQLite driver, no CGo
- **gopsutil** - cross-platform system metrics
- **Docker Compose** - optional project deployment backend

---

## Documentation

- [Installation guide](docs/INSTALL.md)

---

## License

MIT
