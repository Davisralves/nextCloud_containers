# ☁️ Personal Cloud with Nextcloud on any machine

This project sets up a secure and efficient personal cloud using **Nextcloud**, with **Redis** and **MariaDB** support for better performance, all orchestrated with **Docker Compose**.

> Ideal for Raspberry Pi, but also works on any machine with Linux or Windows.

---

## 📦 Included services

- **Nextcloud**: File server with WebDAV, calendar, contacts and extensions support
- **MariaDB**: Database used by Nextcloud
- **Redis**: Cache to optimize performance and reduce database load

---

## 🚀 How to use

### 1. Clone the repository

```bash
git clone https://github.com/Davisralves/nextCloud_containers.git
cd nextCloud_containers
```

### 2. Configure environment variables

Copy the example file and customize the settings:

**Linux/macOS:**
```bash
cp .env.example .env
```

**Windows:**
```powershell
copy .env.example .env
```

Now edit the created `.env` file and change the default passwords:

```env
# User identity (use `id` to find out)
PUID=1000
PGID=1000

# Timezone
TZ=America/Sao_Paulo

# Nextcloud HTTPS port
PORT=8443

# Database - CHANGE THESE PASSWORDS!
MYSQL_ROOT_PASSWORD=your_root_password
DATABASE_PASSWORD=your_db_password

# Redis - CHANGE THIS PASSWORD!
REDIS_PASSWORD=redis_super_secret_password
```

> ⚠️ **Important**: Change all passwords (`your_root_password`, `your_db_password`, `redis_super_secret_password`) to secure values before running the project!

### 3. Run Docker Compose

```bash
docker-compose up -d
```

### 4. Wait for initialization

Wait a few minutes for all services to start. You can follow the logs with:

```bash
docker-compose logs -f
```

### 5. Access Nextcloud

Open your browser and go to:

```
https://localhost:8443
```

> 🔒 **Note**: Since we're using HTTPS with a self-signed certificate, your browser will show a security warning. Click "Advanced" and "Proceed to localhost".

### 6. Configure the administrator

The first time you access, you will be directed to the initial configuration screen:

1. **Create an administrator account:**
   - Username: `admin` (or whatever you prefer)
   - Password: choose a strong password

2. **Database configuration:**
   - Type: **MariaDB/MySQL**
   - Database user: `nextcloud`
   - Database password: the same defined in `DATABASE_PASSWORD` in the `.env` file
   - Database name: `nextcloud_db`
   - Database host: `nextcloud_db`

3. Click **"Finish setup"**

---

## 🔧 Additional configurations

### Configure Redis for cache

After the initial installation, add the following configurations to the Nextcloud configuration file:

1. Access the Nextcloud container:
```bash
docker exec -it nextcloud bash
```

2. Edit the configuration file:
```bash
nano /config/www/nextcloud/config/config.php
```

3. Add the Redis configurations:
```php
'memcache.local' => '\OC\Memcache\Redis',
'memcache.distributed' => '\OC\Memcache\Redis',
'memcache.locking' => '\OC\Memcache\Redis',
'redis' => array(
  'host' => 'nextcloud_redis',
  'port' => 6379,
  'password' => 'your_redis_password', // Same as in .env file
),
```

### Access from other devices on the network

To access Nextcloud from other devices on the same network:

1. Discover the host machine IP:
   ```bash
   # Linux/macOS
   ip addr show
   
   # Windows
   ipconfig
   ```

2. Access via: `https://YOUR_MACHINE_IP:8443`

3. Add the IP to the trusted configurations in Nextcloud by editing `config.php`:
   ```php
   'trusted_domains' => 
   array (
     0 => 'localhost',
     1 => 'YOUR_IP_HERE',
   ),
   ```

---

## 📱 Mobile apps

- **Android**: [Nextcloud on Play Store](https://play.google.com/store/apps/details?id=com.nextcloud.client)
- **iOS**: [Nextcloud on App Store](https://apps.apple.com/app/nextcloud/id1125420102)

Configure the apps with:
- **Server**: `https://YOUR_IP:8443`
- **User**: the administrator created in the initial setup
- **Password**: administrator password

---

## 🛠️ Useful commands

### Check containers status
```bash
docker-compose ps
```

### View logs in real time
```bash
docker-compose logs -f
```

### Stop services
```bash
docker-compose down
```

### Backup data
```bash
# Linux/macOS
tar -czf nextcloud-backup-$(date +%Y%m%d).tar.gz /docker-data/nextcloud/

# Windows (PowerShell)
Compress-Archive -Path "C:\docker-data\nextcloud\*" -DestinationPath "nextcloud-backup-$(Get-Date -Format 'yyyyMMdd').zip"
```

### Update containers
```bash
docker-compose pull
docker-compose up -d
```

---

## 🔍 Troubleshooting

### Container won't start
- Check if ports are not in use: `netstat -tulpn | grep 8443`
- Check the logs: `docker-compose logs nextcloud`

### Data permission error
```bash
# Linux/macOS
sudo chown -R 1000:1000 /docker-data/nextcloud/
```

### Forgot administrator password
1. Access the container: `docker exec -it nextcloud bash`
2. Reset password: `php /config/www/nextcloud/occ user:resetpassword admin`

---

## � See also

- [My LinkedIn post about this project](https://www.linkedin.com/feed/update/urn:li:activity:7358451783330885632/)

---

## �📄 License

This project is under the MIT license. See the [LICENSE](LICENSE) file for more details.
