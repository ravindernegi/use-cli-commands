Absolutely. If you're preparing for a **DevOps / Platform / Cloud Engineer** role, Linux commands should become muscle memory—not something you look up every time.

Below is the command set I recommend practicing **daily**, especially for your Docker + Node.js/NestJS + microservices + PostgreSQL/Kafka/Redis environment.

## 1. File & Directory Commands

| Command  | Example                 | Use case                                        |
| -------- | ----------------------- | ----------------------------------------------- |
| `pwd`    | `pwd`                   | Show current directory                          |
| `ls`     | `ls -lah`               | List files with permissions, size, hidden files |
| `cd`     | `cd /var/log`           | Move between directories                        |
| `mkdir`  | `mkdir -p app/logs`     | Create directories                              |
| `touch`  | `touch app.log`         | Create an empty file                            |
| `cp`     | `cp app.log backup.log` | Copy files                                      |
| `mv`     | `mv app.log logs/`      | Move/rename files                               |
| `rm`     | `rm app.log`            | Delete a file                                   |
| `rm -rf` | `rm -rf old-build/`     | Delete directory recursively                    |
| `file`   | `file app.log`          | Identify file type                              |
| `stat`   | `stat app.log`          | View file metadata                              |
| `tree`   | `tree -L 2`             | Visualize directory structure                   |

**Daily practice:**

```bash
mkdir -p ~/devops/linux/{app,logs,backup}
cd ~/devops/linux
touch app/app.log
cp app/app.log backup/
ls -lah
tree
```

---

# 2. Reading Files & Logs

These are **extremely important for DevOps**.

| Command   | Example                        | Use case                |
| --------- | ------------------------------ | ----------------------- |
| `cat`     | `cat app.log`                  | Read small files        |
| `less`    | `less app.log`                 | Read large files        |
| `head`    | `head -20 app.log`             | First 20 lines          |
| `tail`    | `tail -20 app.log`             | Last 20 lines           |
| `tail -f` | `tail -f app.log`              | Follow live logs        |
| `grep`    | `grep "ERROR" app.log`         | Search text             |
| `grep -i` | `grep -i "error" app.log`      | Case-insensitive search |
| `grep -r` | `grep -r "PORT" .`             | Search recursively      |
| `wc`      | `wc -l app.log`                | Count lines             |
| `sort`    | `sort app.log`                 | Sort output             |
| `uniq`    | `uniq -c`                      | Count duplicate lines   |
| `cut`     | `cut -d: -f1 /etc/passwd`      | Extract fields          |
| `awk`     | `awk '{print $1}' access.log`  | Process columns         |
| `sed`     | `sed 's/ERROR/WARN/g' app.log` | Transform text          |

### Real DevOps example

Your NestJS container is producing errors:

```bash
docker logs banking-api
```

You want only errors:

```bash
docker logs banking-api | grep -i error
```

Follow errors in real time:

```bash
docker logs -f banking-api | grep -i error
```

This combination should become second nature.

---

# 3. Linux Process Management

When something is consuming CPU/RAM or hanging, these commands are essential.

| Command          | Example               | Use case                           |
| ---------------- | --------------------- | ---------------------------------- |
| `ps`             | `ps aux`              | List processes                     |
| `ps aux \| grep` | `ps aux \| grep node` | Find Node processes                |
| `top`            | `top`                 | Live CPU/memory monitoring         |
| `htop`           | `htop`                | Better interactive process monitor |
| `pgrep`          | `pgrep node`          | Find process PID                   |
| `kill`           | `kill 1234`           | Gracefully terminate process       |
| `kill -9`        | `kill -9 1234`        | Force terminate                    |
| `pkill`          | `pkill node`          | Kill matching processes            |
| `jobs`           | `jobs`                | View shell jobs                    |
| `bg`             | `bg %1`               | Resume job in background           |
| `fg`             | `fg %1`               | Bring job to foreground            |
| `nohup`          | `nohup ./app &`       | Keep process running after logout  |

### Example

Find your Node.js process:

```bash
ps aux | grep node
```

Example output:

```text
ravinder   1234   85.2  ... node dist/main.js
```

Check it:

```bash
top -p 1234
```

Terminate gracefully:

```bash
kill 1234
```

Only use:

```bash
kill -9 1234
```

when a normal `kill` doesn't work.

---

# 4. CPU & Memory

| Command  | Example         | Use case                     |
| -------- | --------------- | ---------------------------- |
| `free`   | `free -h`       | Check RAM                    |
| `uptime` | `uptime`        | Load average + uptime        |
| `lscpu`  | `lscpu`         | CPU information              |
| `nproc`  | `nproc`         | Number of CPUs               |
| `vmstat` | `vmstat 1`      | CPU/memory/system statistics |
| `top`    | `top`           | Process resource usage       |
| `htop`   | `htop`          | Interactive monitoring       |
| `watch`  | `watch free -h` | Repeat command               |
| `dmesg`  | `dmesg \| tail` | Kernel messages              |

### Important habit

When a server is slow:

```bash
uptime
free -h
top
df -h
```

This gives you a quick first diagnosis.

---

# 5. Disk & Storage

Very important in production.

| Command       | Example                 | Use case                |
| ------------- | ----------------------- | ----------------------- |
| `df`          | `df -h`                 | Disk filesystem usage   |
| `du`          | `du -sh *`              | Directory sizes         |
| `du`          | `du -sh /var/log/*`     | Find large logs         |
| `lsblk`       | `lsblk`                 | Block devices           |
| `mount`       | `mount`                 | Mounted filesystems     |
| `find`        | `find /var/log -type f` | Find files              |
| `find -size`  | `find . -size +1G`      | Find huge files         |
| `find -mtime` | `find . -mtime +7`      | Files older than 7 days |

### Production scenario

Server says:

```text
No space left on device
```

Immediately:

```bash
df -h
```

Then:

```bash
du -sh /var/*
```

Then:

```bash
du -sh /var/log/*
```

Find huge files:

```bash
find /var -type f -size +1G 2>/dev/null
```

This is a **real DevOps troubleshooting workflow**.

---

# 6. Permissions & Ownership

Linux permissions are foundational.

| Command  | Example                    | Use case                |
| -------- | -------------------------- | ----------------------- |
| `chmod`  | `chmod 755 script.sh`      | Change permissions      |
| `chown`  | `chown user:user app.log`  | Change owner            |
| `chgrp`  | `chgrp developers app.log` | Change group            |
| `ls -l`  | `ls -l app.log`            | Check permissions       |
| `umask`  | `umask`                    | Default permission mask |
| `id`     | `id`                       | User/group information  |
| `whoami` | `whoami`                   | Current user            |

Understand:

```text
-rwxr-xr--
```

as:

```text
owner  group  others
rwx    r-x    r--
```

And know:

```bash
chmod 755 script.sh
```

means:

```text
Owner  = rwx
Group  = r-x
Others = r-x
```

---

# 7. Users & Groups

| Command   | Example                           | Use case                   |
| --------- | --------------------------------- | -------------------------- |
| `whoami`  | `whoami`                          | Current user               |
| `id`      | `id ubuntu`                       | User/group information     |
| `who`     | `who`                             | Logged-in users            |
| `w`       | `w`                               | Logged-in users + activity |
| `passwd`  | `passwd`                          | Change password            |
| `su`      | `su - deploy`                     | Switch user                |
| `sudo`    | `sudo systemctl restart nginx`    | Execute as root            |
| `useradd` | `sudo useradd appuser`            | Create user                |
| `usermod` | `sudo usermod -aG docker appuser` | Modify user                |

---

# 8. Networking — **Must Know**

For DevOps, this section is critical.

| Command       | Example                         | Use case              |
| ------------- | ------------------------------- | --------------------- |
| `ip`          | `ip addr`                       | View IP addresses     |
| `ip route`    | `ip route`                      | Routing table         |
| `ping`        | `ping google.com`               | Basic connectivity    |
| `curl`        | `curl http://localhost:3000`    | Test HTTP service     |
| `wget`        | `wget https://example.com/file` | Download/test HTTP    |
| `ss`          | `ss -tulpn`                     | View listening ports  |
| `netstat`     | `netstat -tulpn`                | Older port inspection |
| `dig`         | `dig google.com`                | DNS lookup            |
| `nslookup`    | `nslookup google.com`           | DNS lookup            |
| `traceroute`  | `traceroute google.com`         | Network path          |
| `hostname`    | `hostname`                      | Machine hostname      |
| `hostname -I` | `hostname -I`                   | Machine IP            |

### Extremely important

Suppose your NestJS application should listen on port `3000`.

Check:

```bash
ss -tulpn | grep 3000
```

Test locally:

```bash
curl http://localhost:3000
```

Test a health endpoint:

```bash
curl http://localhost:3000/health
```

Check DNS:

```bash
dig api.example.com
```

---

# 9. SSH

You will use SSH constantly.

| Command       | Example                            | Use case                  |
| ------------- | ---------------------------------- | ------------------------- |
| `ssh`         | `ssh user@server`                  | Connect to server         |
| `ssh -p`      | `ssh -p 2222 user@server`          | Custom SSH port           |
| `ssh -i`      | `ssh -i key.pem user@server`       | SSH with key              |
| `scp`         | `scp app.tar user@server:/tmp/`    | Copy files                |
| `rsync`       | `rsync -av app/ user@server:/app/` | Efficient synchronization |
| `ssh-keygen`  | `ssh-keygen -t ed25519`            | Generate SSH key          |
| `ssh-copy-id` | `ssh-copy-id user@server`          | Install public key        |

A very useful pattern:

```bash
ssh user@server
```

Then:

```bash
uptime
free -h
df -h
```

You've already performed a basic server health check.

---

# 10. Services & systemd

Modern Linux servers commonly use `systemd`.

| Command             | Example                        | Use case        |
| ------------------- | ------------------------------ | --------------- |
| `systemctl status`  | `systemctl status nginx`       | Service status  |
| `systemctl start`   | `sudo systemctl start nginx`   | Start service   |
| `systemctl stop`    | `sudo systemctl stop nginx`    | Stop service    |
| `systemctl restart` | `sudo systemctl restart nginx` | Restart         |
| `systemctl reload`  | `sudo systemctl reload nginx`  | Reload config   |
| `systemctl enable`  | `sudo systemctl enable nginx`  | Start at boot   |
| `systemctl disable` | `sudo systemctl disable nginx` | Disable startup |
| `journalctl`        | `journalctl -u nginx`          | Service logs    |
| `journalctl -f`     | `journalctl -u nginx -f`       | Follow logs     |

Example:

```bash
sudo systemctl status nginx
```

If broken:

```bash
sudo journalctl -u nginx -n 100
```

---

# 11. Environment Variables

Very important for Node.js and containers.

| Command    | Example                      | Use case                   |
| ---------- | ---------------------------- | -------------------------- |
| `env`      | `env`                        | Show environment variables |
| `printenv` | `printenv NODE_ENV`          | Read variable              |
| `export`   | `export NODE_ENV=production` | Set variable               |
| `unset`    | `unset NODE_ENV`             | Remove variable            |
| `echo`     | `echo $NODE_ENV`             | Display variable           |

Example:

```bash
export NODE_ENV=production
export PORT=3000
```

Check:

```bash
echo $NODE_ENV
echo $PORT
```

---

# 12. Package Management

Ubuntu/Debian:

| Command       | Example                  | Use case             |
| ------------- | ------------------------ | -------------------- |
| `apt update`  | `sudo apt update`        | Update package index |
| `apt upgrade` | `sudo apt upgrade`       | Upgrade packages     |
| `apt install` | `sudo apt install nginx` | Install package      |
| `apt remove`  | `sudo apt remove nginx`  | Remove package       |
| `apt search`  | `apt search nginx`       | Search packages      |
| `dpkg`        | `dpkg -l`                | Installed packages   |

---

# 13. Archives & Compression

| Command    | Example                    | Use case        |
| ---------- | -------------------------- | --------------- |
| `tar`      | `tar -czf app.tar.gz app/` | Create archive  |
| `tar -xzf` | `tar -xzf app.tar.gz`      | Extract archive |
| `gzip`     | `gzip app.log`             | Compress        |
| `gunzip`   | `gunzip app.log.gz`        | Decompress      |
| `zip`      | `zip -r app.zip app/`      | ZIP archive     |
| `unzip`    | `unzip app.zip`            | Extract ZIP     |

Very common deployment operation:

```bash
tar -czf release.tar.gz dist package.json
```

Then:

```bash
scp release.tar.gz server:/tmp/
```

---

# 14. `find` — One of the Most Important Commands

Master this.

| Requirement             | Command                        |
| ----------------------- | ------------------------------ |
| Find file               | `find . -name "app.log"`       |
| Find all `.log`         | `find . -name "*.log"`         |
| Find directories        | `find . -type d`               |
| Find files              | `find . -type f`               |
| Files > 100 MB          | `find . -type f -size +100M`   |
| Files modified today    | `find . -mtime 0`              |
| Files older than 7 days | `find . -mtime +7`             |
| Delete `.tmp` files     | `find . -name "*.tmp" -delete` |

Be careful with:

```bash
-delete
```

on production systems.

---

# 15. Pipes & Redirection

This is where Linux becomes extremely powerful.

### Pipe

```bash
ps aux | grep node
```

Output of one command becomes input to another.

### Redirect output

```bash
ls -lah > files.txt
```

### Append

```bash
echo "deployment successful" >> deploy.log
```

### Error redirection

```bash
command 2> error.log
```

### Both stdout + stderr

```bash
command > output.log 2>&1
```

### Discard output

```bash
command > /dev/null 2>&1
```

You should become very comfortable with:

```bash
|
>
>>
2>
2>&1
```

---

# 16. `xargs`

Very useful when working with large command outputs.

Example:

```bash
find . -name "*.log" | xargs ls -lh
```

Or:

```bash
cat services.txt | xargs -n1 docker restart
```

Understand what you're executing before using `xargs` with destructive commands.

---

# 17. Git + Linux

DevOps engineers constantly combine Git with Linux.

| Command        | Example              | Use case         |
| -------------- | -------------------- | ---------------- |
| `git clone`    | `git clone repo.git` | Clone repository |
| `git status`   | `git status`         | Working tree     |
| `git log`      | `git log --oneline`  | Commit history   |
| `git branch`   | `git branch`         | Branches         |
| `git diff`     | `git diff`           | Changes          |
| `git pull`     | `git pull`           | Update code      |
| `git checkout` | `git checkout main`  | Switch branch    |

For your banking platform:

```bash
git pull
pnpm install
pnpm build
docker compose up -d
docker compose ps
docker compose logs -f
```

That's a very realistic DevOps workflow.

---

# 18. Docker + Linux Commands

Since you're learning Docker, practice these together.

| Command             | Example                        | Use case           |
| ------------------- | ------------------------------ | ------------------ |
| `docker ps`         | `docker ps`                    | Running containers |
| `docker ps -a`      | `docker ps -a`                 | All containers     |
| `docker images`     | `docker images`                | Images             |
| `docker logs`       | `docker logs api`              | Container logs     |
| `docker exec`       | `docker exec -it api sh`       | Enter container    |
| `docker inspect`    | `docker inspect api`           | Container details  |
| `docker stats`      | `docker stats`                 | Resource usage     |
| `docker port`       | `docker port api`              | Port mappings      |
| `docker cp`         | `docker cp api:/app/log.txt .` | Copy files         |
| `docker network ls` | `docker network ls`            | Networks           |
| `docker volume ls`  | `docker volume ls`             | Volumes            |

A very important debugging sequence:

```bash
docker ps
```

```bash
docker logs api
```

```bash
docker exec -it api sh
```

Inside:

```bash
ps
ls -lah
env
```

---

# 19. Process + Network Troubleshooting

Memorize this workflow.

### Problem:

> "My application isn't accessible."

Run:

```bash
ps aux | grep node
```

Then:

```bash
ss -tulpn
```

Then:

```bash
curl localhost:3000
```

Then:

```bash
curl localhost:3000/health
```

If Docker:

```bash
docker ps
```

```bash
docker logs api
```

```bash
docker port api
```

This is much more valuable than memorizing 500 commands.

---

# 20. Advanced Commands You Should Eventually Learn

| Command      | Example                                     | Why learn it                   |
| ------------ | ------------------------------------------- | ------------------------------ |
| `lsof`       | `lsof -i :3000`                             | Find process using port        |
| `strace`     | `strace -p 1234`                            | Debug system calls             |
| `tcpdump`    | `sudo tcpdump -i any port 5432`             | Network packet debugging       |
| `nc`         | `nc -vz localhost 5432`                     | Test TCP connectivity          |
| `openssl`    | `openssl s_client -connect example.com:443` | TLS debugging                  |
| `dig`        | `dig api.example.com`                       | DNS debugging                  |
| `journalctl` | `journalctl -xe`                            | System/service troubleshooting |
| `dmesg`      | `dmesg \| tail`                             | Kernel issues                  |
| `vmstat`     | `vmstat 1`                                  | System performance             |
| `iostat`     | `iostat -xz 1`                              | Disk I/O                       |
| `sar`        | `sar -n DEV 1`                              | Historical/system metrics      |

---

# Your Daily 30-Minute Linux Practice

Don't try to memorize everything above.

Do this **every day**:

### 5 min — Files

```bash
pwd
ls -lah
cd
mkdir
cp
mv
rm
find
```

### 5 min — Logs

```bash
cat
less
head
tail
tail -f
grep
awk
sed
```

### 5 min — System

```bash
ps aux
top
free -h
df -h
du -sh
uptime
```

### 5 min — Networking

```bash
ip addr
ip route
ss -tulpn
curl
ping
dig
lsof
```

### 5 min — Permissions

```bash
ls -l
chmod
chown
id
sudo
```

### 5 min — DevOps

Use your own project:

```bash
git pull
docker ps
docker images
docker logs
docker exec
docker stats
docker network ls
```

---

## ⭐ The 25 Commands I'd Make Muscle Memory

If you want to become a strong DevOps engineer, start with these:

```text
pwd
ls
cd
mkdir
cp
mv
rm
cat
less
tail
grep
find
ps
top
kill
df
du
free
chmod
chown
curl
ss
ssh
systemctl
journalctl
```

Then add:

```text
awk
sed
xargs
lsof
dig
tcpdump
strace
rsync
tar
```

### Most importantly

Don't learn Linux as a **command dictionary**.

Learn it as a **troubleshooting system**:

```text
Application problem
       ↓
Process?
       ↓
ps / top
       ↓
Port?
       ↓
ss / lsof
       ↓
Network?
       ↓
ping / curl / nc / dig
       ↓
Logs?
       ↓
tail / grep / journalctl
       ↓
CPU / RAM?
       ↓
top / free / vmstat
       ↓
Disk?
       ↓
df / du
       ↓
Permissions?
       ↓
ls -l / chmod / chown
```

That mindset is far more important for a **Senior DevOps / Platform Engineer** than knowing hundreds of commands.
