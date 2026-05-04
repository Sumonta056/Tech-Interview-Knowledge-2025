# Docker Cleanup!

#### Docker Stats

```
docker system df
```

![Screenshot 2026-04-28 at 10 52 41 AM](https://github.com/user-attachments/assets/3519bfbc-97b5-49be-a903-46c3002ad3fd)

* Check what is eating space

```
docker system df -v
```

The -v flag breaks it down per image, per volume, per container. You'll finally see WHERE the space went.

* Kill the build cache

```
docker builder prune  # remove all build cache

docker builder prune --filter "until=72h"
```

* Clean dangling images (the safe default)

```
docker image prune
```

This removes only untagged images — the leftover layers from failed or intermediate builds. Zero risk to your running containers.

* Clean unused volumes (carefully)

```
docker volume ls -f dangling=true
docker volume prune
```

* Container Cleanup

```
docker container prune
```
