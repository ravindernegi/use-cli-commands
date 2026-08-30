As a **Senior DevOps Engineer**, I would divide Docker commands into **4 levels**:

1. **Beginner** — everyday Docker usage
2. **Intermediate** — debugging containers/images/networks/volumes
3. **Advanced** — production troubleshooting
4. **Production / Incident Debugging** — commands you should be comfortable running during an outage

> The important skill is not memorizing 100 commands. You should know **what to inspect first, how containers connect, where data lives, and how to trace a failing request**.

---

# 🐳 Docker Commands Every DevOps Engineer Should Know

## 1. Docker Environment / Basic Information

| Level    | Command            | Example            | Use Case                            |
| -------- | ------------------ | ------------------ | ----------------------------------- |
| Beginner | `docker version`   | `docker version`   | Check Docker client/server versions |
| Beginner | `docker info`      | `docker info`      | Get Docker engine information       |
| Beginner | `docker --help`    | `docker --help`    | Discover Docker commands            |
| Beginner | `docker ps`        | `docker ps`        | List running containers             |
| Beginner | `docker ps -a`     | `docker ps -a`     | List running + stopped containers   |
| Beginner | `docker images`    | `docker images`    | List local images                   |
| Beginner | `docker system df` | `docker system df` | Check Docker disk usage             |

### Most important

```bash
docker ps
docker ps -a
docker images
docker info
docker system df
```

I recommend starting almost every debugging session with:

```bash
docker ps -a
```

---

# 2. Container Lifecycle

| Level    | Command             | Example                          | Use Case                        |
| -------- | ------------------- | -------------------------------- | ------------------------------- |
| Beginner | `docker run`        | `docker run nginx`               | Create and start container      |
| Beginner | `docker run -d`     | `docker run -d nginx`            | Run in background               |
| Beginner | `docker run --name` | `docker run -d --name web nginx` | Give container a name           |
| Beginner | `docker start`      | `docker start web`               | Start stopped container         |
| Beginner | `docker stop`       | `docker stop web`                | Gracefully stop container       |
| Beginner | `docker restart`    | `docker restart web`             | Restart container               |
| Beginner | `docker kill`       | `docker kill web`                | Immediately terminate container |
| Beginner | `docker rm`         | `docker rm web`                  | Remove stopped container        |
| Beginner | `docker rm -f`      | `docker rm -f web`               | Force remove running container  |

### Typical workflow

```bash
docker run -d --name my-nginx nginx

docker stop my-nginx

docker start my-nginx

docker restart my-nginx

docker rm my-nginx
```

---

# 3. Port Mapping

This is **extremely important for DevOps**.

| Command | Example                       | Use Case                            |
| ------- | ----------------------------- | ----------------------------------- |
| `-p`    | `docker run -p 8080:80 nginx` | Map host → container port           |
| `-P`    | `docker run -P nginx`         | Automatically publish exposed ports |

Example:

```bash
docker run -d \
  --name web \
  -p 8080:80 \
  nginx
```

Now:

```text
Mac/Linux Host
     |
     | localhost:8080
     ↓
Docker Container
     |
     | port 80
     ↓
Nginx
```

Check:

```bash
docker ps
```

You may see:

```text
0.0.0.0:8080->80/tcp
```

### Important concept

```text
-p HOST_PORT:CONTAINER_PORT
```

For example:

```bash
-p 5173:5173
```

means:

```text
localhost:5173 → container:5173
```

---

# 4. Container Logs — 🔥 Most Important for Debugging

| Level        | Command               | Example                       | Use Case            |
| ------------ | --------------------- | ----------------------------- | ------------------- |
| Beginner     | `docker logs`         | `docker logs api`             | View container logs |
| Beginner     | `docker logs -f`      | `docker logs -f api`          | Follow live logs    |
| Intermediate | `docker logs --tail`  | `docker logs --tail 100 api`  | Last 100 lines      |
| Intermediate | `docker logs --since` | `docker logs --since 10m api` | Recent logs         |
| Intermediate | `docker logs -t`      | `docker logs -t api`          | Show timestamps     |

### Production debugging

```bash
docker logs --tail 200 api
```

Then:

```bash
docker logs -f api
```

Or:

```bash
docker logs --since 30m api
```

### Search logs

```bash
docker logs api 2>&1 | grep ERROR
```

or:

```bash
docker logs api 2>&1 | grep -i exception
```

This is one of the most useful Docker debugging techniques.

---

# 5. Enter a Running Container — 🔥🔥

| Level        | Command           | Example                    | Use Case                     |
| ------------ | ----------------- | -------------------------- | ---------------------------- |
| Beginner     | `docker exec`     | `docker exec api ls`       | Run command inside container |
| Beginner     | `docker exec -it` | `docker exec -it api bash` | Open shell                   |
| Beginner     | `docker exec -it` | `docker exec -it api sh`   | Alpine/minimal images        |
| Intermediate | `docker exec`     | `docker exec api env`      | Inspect environment          |
| Intermediate | `docker exec`     | `docker exec api ps`       | Inspect processes            |

### Bash

```bash
docker exec -it api bash
```

If bash doesn't exist:

```bash
docker exec -it api sh
```

Once inside:

```bash
ls
pwd
env
ps
```

Exit:

```bash
exit
```

### Very useful

```bash
docker exec api env
```

Check environment variables without entering the container.

---

# 6. Inspect Container — 🔥🔥🔥

One of the most important commands for a Senior DevOps Engineer:

```bash
docker inspect <container>
```

Example:

```bash
docker inspect api
```

It gives information about:

- Container configuration
- IP address
- Network
- Mounts
- Environment variables
- Port bindings
- Image
- Restart policy
- Volumes
- Container state

Useful:

```bash
docker inspect api | less
```

Get IP:

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' api
```

Get container status:

```bash
docker inspect -f '{{.State.Status}}' api
```

Get restart count:

```bash
docker inspect -f '{{.RestartCount}}' api
```

---

# 7. Container Resource Usage — 🔥🔥🔥

| Command                    | Example            | Use Case                |
| -------------------------- | ------------------ | ----------------------- |
| `docker stats`             | `docker stats`     | Live CPU/memory/network |
| `docker stats <container>` | `docker stats api` | Monitor one container   |
| `docker top`               | `docker top api`   | Show processes          |

### Production incident

Application is slow?

Run:

```bash
docker stats
```

You might discover:

```text
CONTAINER    CPU %    MEM USAGE
api          98%      3.8GiB
postgres     12%      1.2GiB
redis         2%      200MiB
```

Now you know where to investigate.

---

# 8. Image Commands

| Command                | Example                        | Use Case         |
| ---------------------- | ------------------------------ | ---------------- |
| `docker images`        | `docker images`                | List images      |
| `docker pull`          | `docker pull nginx:latest`     | Download image   |
| `docker build`         | `docker build -t my-api .`     | Build image      |
| `docker tag`           | `docker tag api:latest api:v1` | Tag image        |
| `docker push`          | `docker push repo/api:v1`      | Push image       |
| `docker rmi`           | `docker rmi api:v1`            | Delete image     |
| `docker image inspect` | `docker image inspect nginx`   | Inspect image    |
| `docker history`       | `docker history nginx`         | See image layers |

---

# 9. Build Debugging

Build:

```bash
docker build -t my-api .
```

Build without cache:

```bash
docker build --no-cache -t my-api .
```

Very useful when Docker seems to be using an old layer.

```bash
docker build --progress=plain -t my-api .
```

This gives detailed build output.

### Debug Dockerfile

```bash
docker history my-api
```

You can see how the image was constructed layer by layer.

---

# 10. Copy Files Between Host and Container

| Command     | Example                           | Use Case         |
| ----------- | --------------------------------- | ---------------- |
| `docker cp` | `docker cp api:/app/log.txt .`    | Container → Host |
| `docker cp` | `docker cp config.json api:/app/` | Host → Container |

Example:

```bash
docker cp api:/app/logs/error.log ./error.log
```

Useful when you need to extract a file from a running container.

---

# 11. Environment Variables

See environment variables:

```bash
docker exec api env
```

Or:

```bash
docker inspect api
```

Run with environment variable:

```bash
docker run -d \
  --name api \
  -e NODE_ENV=production \
  -e PORT=3000 \
  my-api
```

Use `.env`:

```bash
docker run --env-file .env my-api
```

### Production debugging

Check:

```bash
docker exec api env | sort
```

This can quickly reveal:

```text
DATABASE_URL
REDIS_HOST
NODE_ENV
PORT
KAFKA_BROKERS
```

---

# 12. Volumes — 🔥 Important for Databases

List volumes:

```bash
docker volume ls
```

Inspect:

```bash
docker volume inspect postgres_data
```

Create:

```bash
docker volume create postgres_data
```

Remove:

```bash
docker volume rm postgres_data
```

Example:

```bash
docker run -d \
  --name postgres \
  -v postgres_data:/var/lib/postgresql/data \
  postgres
```

### Important distinction

```text
Container
   ↓
Ephemeral

Volume
   ↓
Persistent
```

If you remove a container:

```bash
docker rm postgres
```

the named volume can remain.

---

# 13. Bind Mounts

Example:

```bash
docker run -d \
  -v $(pwd):/app \
  my-api
```

This means:

```text
Host directory
      ↓
     /app
      ↓
Container
```

Inspect mounts:

```bash
docker inspect api
```

Look for:

```text
Mounts
```

Very useful when:

> "My code changed on host but container isn't seeing it."

---

# 14. Docker Networks — 🔥🔥🔥

List networks:

```bash
docker network ls
```

Inspect:

```bash
docker network inspect my-network
```

Create:

```bash
docker network create backend
```

Connect:

```bash
docker network connect backend api
```

Disconnect:

```bash
docker network disconnect backend api
```

Remove:

```bash
docker network rm backend
```

---

# 15. Container-to-Container Debugging

Suppose:

```text
api
 ↓
postgres
 ↓
redis
```

Both containers are on:

```text
backend
```

Inside API:

```bash
docker exec -it api sh
```

Then test:

```bash
ping postgres
```

or:

```bash
nc -zv postgres 5432
```

Redis:

```bash
nc -zv redis 6379
```

### Important Docker concept

Inside Docker network:

```text
postgres:5432
```

not:

```text
localhost:5432
```

Because:

```text
localhost
```

inside the API container means **the API container itself**.

---

# 16. DNS Debugging

Very useful in microservices.

Inside container:

```bash
getent hosts postgres
```

or:

```bash
getent hosts api
```

You can check:

```bash
cat /etc/hosts
cat /etc/resolv.conf
```

If available:

```bash
nslookup postgres
```

or:

```bash
dig postgres
```

---

# 17. Network Connectivity Debugging

Check listening ports:

```bash
ss -lntp
```

Inside container:

```bash
ss -lntp
```

Test TCP:

```bash
nc -zv postgres 5432
```

Test HTTP:

```bash
curl http://api:3000/health
```

Test from host:

```bash
curl http://localhost:3000/health
```

This lets you determine whether the problem is:

```text
Application
     ↓
Container
     ↓
Docker Network
     ↓
Host Port
     ↓
Load Balancer
```

---

# 18. Health Check

Check container state:

```bash
docker ps
```

For detailed health:

```bash
docker inspect api
```

Look for:

```text
State.Health
```

Example Dockerfile:

```dockerfile
HEALTHCHECK CMD curl -f http://localhost:3000/health || exit 1
```

Then:

```bash
docker ps
```

may show:

```text
Up 5 minutes (healthy)
```

or:

```text
Up 5 minutes (unhealthy)
```

---

# 19. Processes Inside Container

```bash
docker top api
```

Then:

```bash
docker exec api ps aux
```

Useful when:

> Container is running but application isn't responding.

Check whether the expected process actually exists.

---

# 20. Restart Problems — 🔥 Production

Check:

```bash
docker ps -a
```

If:

```text
api    Exited (1)
```

immediately check:

```bash
docker logs api
```

Then:

```bash
docker inspect api
```

Check restart count:

```bash
docker inspect -f '{{.RestartCount}}' api
```

Check exit code:

```bash
docker inspect -f '{{.State.ExitCode}}' api
```

Check error:

```bash
docker inspect -f '{{.State.Error}}' api
```

---

# 21. OOM / Memory Debugging

If a container keeps restarting:

```bash
docker inspect api
```

Look at:

```text
OOMKilled
```

You can directly query:

```bash
docker inspect -f '{{.State.OOMKilled}}' api
```

Then:

```bash
docker stats api
```

If memory is continuously increasing:

```text
100 MB
500 MB
1 GB
2 GB
...
```

you may be dealing with a memory leak.

---

# 22. Docker Events — Advanced 🔥

This is extremely useful during incidents.

```bash
docker events
```

It shows real-time Docker events:

```text
container start
container stop
container die
container restart
network connect
network disconnect
```

For example:

```bash
docker events --filter container=api
```

Useful when you are asking:

> "Why does this container keep restarting?"

---

# 23. Docker System Cleanup

Check disk:

```bash
docker system df
```

Remove unused containers:

```bash
docker container prune
```

Remove unused images:

```bash
docker image prune
```

Remove unused volumes:

```bash
docker volume prune
```

Remove unused networks:

```bash
docker network prune
```

Remove everything unused:

```bash
docker system prune
```

More aggressive:

```bash
docker system prune -a
```

### ⚠️ Production warning

Don't blindly run:

```bash
docker system prune -a
```

on production.

First inspect:

```bash
docker system df
```

---

# 24. Docker Compose — 🔥🔥🔥

For modern DevOps, Docker Compose is essential for local environments.

Start:

```bash
docker compose up
```

Background:

```bash
docker compose up -d
```

Build:

```bash
docker compose build
```

Build + start:

```bash
docker compose up -d --build
```

Stop:

```bash
docker compose stop
```

Remove containers:

```bash
docker compose down
```

Remove containers + volumes:

```bash
docker compose down -v
```

### Logs

```bash
docker compose logs
```

Specific service:

```bash
docker compose logs api
```

Follow:

```bash
docker compose logs -f api
```

### Service status

```bash
docker compose ps
```

---

# 25. Docker Compose Debugging

Suppose your architecture is:

```text
                    ┌──────────┐
                    │   API    │
                    └────┬─────┘
                         │
              ┌──────────┴──────────┐
              ↓                     ↓
        ┌──────────┐          ┌──────────┐
        │ Postgres │          │  Redis   │
        └──────────┘          └──────────┘
```

First:

```bash
docker compose ps
```

Then:

```bash
docker compose logs api
```

Then:

```bash
docker compose logs postgres
```

Then:

```bash
docker compose logs redis
```

Then:

```bash
docker compose exec api sh
```

Inside:

```bash
curl http://postgres:5432
```

or:

```bash
nc -zv postgres 5432
```

---

# 26. Production Docker Debugging — My Recommended Sequence

When someone says:

> **"Production API is down."**

Don't randomly execute commands.

Follow a structured process.

### Step 1 — Is the container running?

```bash
docker ps
```

If missing:

```bash
docker ps -a
```

---

### Step 2 — Check logs

```bash
docker logs --tail 200 api
```

---

### Step 3 — Check restart/exit status

```bash
docker inspect -f '{{.State.Status}}' api
```

```bash
docker inspect -f '{{.State.ExitCode}}' api
```

```bash
docker inspect -f '{{.State.OOMKilled}}' api
```

---

### Step 4 — Check resources

```bash
docker stats api
```

---

### Step 5 — Check processes

```bash
docker top api
```

---

### Step 6 — Enter container

```bash
docker exec -it api sh
```

---

### Step 7 — Test application internally

```bash
curl http://localhost:3000/health
```

---

### Step 8 — Test dependencies

```bash
nc -zv postgres 5432
```

```bash
nc -zv redis 6379
```

---

### Step 9 — Check Docker network

From host:

```bash
docker network inspect backend
```

---

### Step 10 — Check host ports

```bash
docker ps
```

Look for:

```text
0.0.0.0:3000->3000/tcp
```

Then from host:

```bash
curl http://localhost:3000/health
```

---

# 27. The "Golden" Docker Debugging Commands

If I were interviewing a DevOps engineer, these are the commands I'd expect them to be comfortable with:

```bash
docker ps
docker ps -a

docker logs <container>
docker logs -f <container>
docker logs --tail 100 <container>

docker exec -it <container> sh
docker exec -it <container> bash

docker inspect <container>

docker stats
docker top <container>

docker network ls
docker network inspect <network>

docker volume ls
docker volume inspect <volume>

docker images
docker image inspect <image>
docker history <image>

docker system df

docker events

docker compose ps
docker compose logs
docker compose exec
docker compose restart
docker compose down
docker compose up -d --build
```

---

# 28. Docker Commands by Skill Level

| Skill           | Commands You Should Know                                                                                  |
| --------------- | --------------------------------------------------------------------------------------------------------- |
| 🟢 Beginner     | `run`, `ps`, `images`, `start`, `stop`, `restart`, `rm`, `rmi`                                            |
| 🟡 Intermediate | `logs`, `exec`, `inspect`, `cp`, `build`, `pull`, `push`, `tag`, `stats`                                  |
| 🔵 Advanced     | `network`, `volume`, `events`, `history`, `system df`, health checks                                      |
| 🔴 Production   | `inspect`, `logs`, `stats`, `exec`, `network inspect`, `events`, `OOMKilled`, `ExitCode`, `restart count` |
| 🔥 Compose      | `up`, `down`, `ps`, `logs`, `exec`, `build`, `restart`                                                    |

---

# 29. One Mental Model You Should Memorize

When debugging Docker, think in this order:

```text
                    USER REQUEST
                         │
                         ↓
                  Host / LB / Proxy
                         │
                         ↓
                    Port Mapping
                         │
                         ↓
                    Container
                         │
              ┌──────────┴──────────┐
              ↓                     ↓
           Process              Environment
              │
              ↓
          Application
              │
       ┌──────┼───────┐
       ↓      ↓       ↓
    Postgres Redis   Kafka
       │      │       │
       └──────┴───────┘
              │
           Volumes
```

So your debugging commands map directly:

```text
Container running?
    → docker ps -a

What happened?
    → docker logs

What is configured?
    → docker inspect

What's consuming resources?
    → docker stats

What process is running?
    → docker top

Can I enter it?
    → docker exec

Can services communicate?
    → docker network inspect
    → nc
    → curl

Where is persistent data?
    → docker volume inspect

Why did something happen?
    → docker events
```

**This is the Docker troubleshooting mindset I would expect from a production DevOps engineer.**

And for your current DevOps learning path, I would practice these locally first with a small stack of **React/Node.js + PostgreSQL + Redis**, then move the same debugging workflow into a production-like environment.
