# 🛠️ BookStack Setup Guide – Docker on Windows
**Date:** 2025-05-02  
**Maintainer:** Andrei Stan  

---

## 🧱 Prerequisites

- Docker Desktop (with WSL 2 backend recommended)
- Git Bash or PowerShell
- Windows 10/11 with virtualization enabled

---

## 📦 Folder Structure

```plaintext
Bookstack/
├── docker-compose.yml
├── bookstack_app_data/
└── bookstack_db_data/
```

---

## ⚙️ docker-compose.yml (Final Working Version)

```yaml
version: "3.8"

services:
  bookstack_db:
    image: lscr.io/linuxserver/mariadb:latest
    container_name: bookstack_db
    environment:
      - MYSQL_ROOT_PASSWORD=vinushka_root
      - MYSQL_DATABASE=bookstackapp
      - MYSQL_USER=bookstack
      - MYSQL_PASSWORD=vinushka
    volumes:
      - ./bookstack_db_data:/config
    restart: unless-stopped

  bookstack:
    image: lscr.io/linuxserver/bookstack:latest
    container_name: bookstack
    environment:
      - PUID=1000
      - PGID=1000
      - DB_HOST=bookstack_db
      - DB_USER=bookstack
      - DB_PASS=vinushka
      - DB_DATABASE=bookstackapp
      - APP_URL=http://localhost:6875
      - APP_KEY=your_generated_key
    ports:
      - 6875:80
    volumes:
      - ./bookstack_app_data:/config
    depends_on:
      - bookstack_db
    restart: unless-stopped
```

### Deploy

```bash
docker-compose up -d
```

Wait 1–2 minutes, then run:

```bash
docker exec -it bookstack /bin/bash
cd /app/www
php artisan key:generate
php artisan config:clear
php artisan cache:clear
php artisan config:cache
exit
```

Then:

```bash
docker restart bookstack
```

### Login

Visit: [http://localhost:6875](http://localhost:6875)

Default login:

- **Email:** `admin@admin.com`
- **Password:** `password`

---

## 🔐 Default Credentials

- **Email**: `admin@admin.com`
- **Password**: `password`

_Change them immediately after first login!_

---

## 🔑 Generating APP_KEY

Run inside the container:
```bash
docker exec -it bookstack /bin/bash
cd /app/www
php artisan key:generate --show
```

Paste that `base64:` key into `APP_KEY` in `docker-compose.yml`.

---

## 🔁 Final Setup Steps

```bash
docker-compose down -v
docker-compose up -d
```

Then visit: [http://localhost:6875](http://localhost:6875)

---

## 👥 Create More Users

After logging in as admin:

1. Go to the top-right profile menu → **Settings**
2. Navigate to **Users**
3. Click **+ New User**
4. Set email, password, and assign a role

Optional:
- Use **Roles & Permissions** for fine-grained control.

---

## 🎨 Optional: Theme or Branding

Customize `/bookstack_app_data/www/themes/` and enable via settings.

---

## 🧯 Troubleshooting

- `DB connection failed` → verify `DB_*` vars match across both containers.
- `APP_KEY missing` → generate via artisan and apply before restart.
- Use `docker logs bookstack` for real-time logs.

---

## 💬 Questions or Hellfire?
Talk to Senomy. She’s terrifyingly good at this.

---
