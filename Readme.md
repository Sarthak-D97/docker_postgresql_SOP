# 🐘 Professional SOP: PostgreSQL Environment Setup

---

**Stack:** Docker Desktop + WSL 2 (Windows) / Native (macOS) + TablePlus UI

**Author:** Sarthak Chauhan

**Date:** February 12, 2026

---

## 📑 Table of Contents

1. [Prerequisites & System Architecture](https://www.google.com/search?q=%230-prerequisites--system-architecture)
2. [Phase 1: Docker Desktop & Environment Setup](https://www.google.com/search?q=%23phase-1-docker-desktop--environment-setup)
* [1.1 Windows: WSL 2 Backend (Essential)](https://www.google.com/search?q=%2311-windows-wsl-2-backend-essential)
* [1.2 macOS: Architecture Identification](https://www.google.com/search?q=%2312-macos-architecture-identification)


3. [Phase 2: TablePlus UI Installation](https://www.google.com/search?q=%23phase-2-tableplus-ui-installation)
4. [Phase 3: Deploying PostgreSQL via Docker](https://www.google.com/search?q=%23phase-3-deploying-postgresql-via-docker)
5. [Phase 4: Connecting TablePlus to PostgreSQL](https://www.google.com/search?q=%23phase-4-connecting-tableplus-to-postgresql)
6. [Phase 5: Maintenance & Troubleshooting](https://www.google.com/search?q=%23phase-5-maintenance--troubleshooting)

---

## 0. Prerequisites & System Architecture

Before starting, understand the flow:

* **Docker:** Acts as the "Engine" that runs PostgreSQL in an isolated environment.
* **WSL 2 (Windows Only):** Provides the Linux kernel required for Docker to run with high performance.
* **TablePlus:** The "Cockpit" where you visually manage your data and run queries.

---

## Phase 1: Docker Desktop & Environment Setup

### 1.1 Windows: WSL 2 Backend (Essential)

*Note: If you are on macOS, skip to section 1.2.*

Windows requires a Linux backend to run Docker containers natively. If you encounter kernel errors, use the steps below.

<img src="./assets/win_wsl_error.png" alt="WSL Error Example" width="800">

#### **Steps to Enable WSL Features:**

1. Open **PowerShell** or **Command Prompt** as **Administrator**.
2. Execute the following command to install WSL:
```powershell
wsl --install

```


3. Ensure your kernel is fully updated:
```powershell
wsl --update

```


4. Set WSL 2 as your default version:
```powershell
wsl --set-default-version 2

```



<img src="./assets/wsl_update.png" alt="WSL Update Command Success" width="800">

#### **Install Docker Desktop (Windows):**

1. Download the installer from the [Docker Desktop Official Site](https://www.docker.com/products/docker-desktop/).
2. Run the `.exe` installer.
3. **Important:** Ensure the checkbox **"Use WSL 2 instead of Hyper-V"** is checked.
4. Restart your computer if prompted.

---

### 1.2 macOS: Architecture Identification

1. Identify your chip: Click **Apple () Menu** > **About This Mac**.
2. Download the correct installer from [Docker Desktop](https://www.docker.com/products/docker-desktop/):
* **Apple Silicon:** (M1/M2/M3) Select the ARM64 version.
* **Intel:** Select the x86_64 version.


3. Open the `.dmg` file and drag the **Docker** icon into the **Applications** folder.

<img src="./assets/docker.png" alt="Docker Desktop Installation View" width="800">

---

## Phase 2: TablePlus UI Installation

1. Visit [TablePlus.com](https://tableplus.com/) and download the version for your OS.
2. **Installation:**
* **Windows:** Follow the standard `.exe` setup wizard.
* **macOS:** Drag the `.app` to your Applications folder.


3. **Initial Launch:** Open TablePlus. Click **Install** if prompted to add helper tools for the CLI.

<img src="./assets/tableplus.png" alt="TablePlus Initial Setup Screen" width="800">

---

## Phase 3: Deploying PostgreSQL via Docker

We use **Data Persistence** to ensure that if you delete the container, your data remains safe in a Docker Volume.

### 3.1 Create a Persistent Volume

Open your Terminal or PowerShell and run:

```bash
docker volume create sarthak_pg_data

```

### 3.2 Run the PostgreSQL Container

Execute the following command to pull the image and start the server:

```bash
docker run -d \
  --name local-postgres-dev \
  -e POSTGRES_USER=sarthak_admin \
  -e POSTGRES_PASSWORD=my_secure_pass_123 \
  -e POSTGRES_DB=dev_database \
  -p 5432:5432 \
  -v sarthak_pg_data:/var/lib/postgresql/data \
  --restart always \
  postgres:latest

```

<img src="./assets/command.png" alt="Terminal Command Execution" width="800">

#### **🔍 Command Breakdown:**

* **`--name`**: Custom name for your container.
* **`-e`**: Sets your User, Password, and DB Name.
* **`-p 5432:5432`**: Maps your local port to the container.
* **`-v`**: Attaches your persistent volume.

<img src="./assets/docker_img.png" alt="Docker Image Pulled" width="800">
<img src="./assets/docker_container.png" alt="Docker Container Running Status" width="800">

---

## Phase 4: Connecting TablePlus to PostgreSQL

1. Launch **TablePlus** and click the **"+"** (Create a new connection) icon.
2. Select **PostgreSQL** and click **Create**.
3. **Enter Connection Details:**
* **Name:** `Sarthak Local Dev`
* **Host:** `127.0.0.1`
* **Port:** `5432`
* **User:** `sarthak_admin`
* **Password:** `my_secure_pass_123`
* **Database:** `dev_database`


4. Set **SSL Mode** to `Disable`.
5. Click **Test**. Once the fields turn green, click **Save** and **Connect**.

<img src="./assets/tableplus_connection.png" alt="TablePlus New Connection List" width="800">
<img src="./assets/tableplus_form.png" alt="Filled TablePlus Connection Form" width="800">

---

## Phase 5: Maintenance & Troubleshooting

### 5.1 Essential Docker Commands

* **Check Health:** `docker ps`
* **Stop DB:** `docker stop local-postgres-dev`
* **Start DB:** `docker start local-postgres-dev`

### 5.2 SQL Verification Test

In the TablePlus Query Editor (**Cmd + E** / **Ctrl + E**), run:

```sql
CREATE TABLE developers (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    stack VARCHAR(50)
);

INSERT INTO developers (name, stack) VALUES ('Sarthak', 'Next.js / PostgreSQL');
SELECT * FROM developers;

```

<img src="./assets/tableplus_dashboard.png" alt="TablePlus Dashboard with Successful Query" width="800">

### 5.3 Troubleshooting

* **Port 5432 Busy:** Stop any local Postgres services running outside Docker.
* **WSL Error:** Re-run `wsl --update` and restart Docker Desktop.

---
