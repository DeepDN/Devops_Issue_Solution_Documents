# GitLab CI Runner Installation Guide

## Linux Installation
```bash
# Add repository
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash

# Install
sudo apt-get install gitlab-runner
```

## Docker Installation
```bash
docker run -d --name gitlab-runner --restart always \
  -v /srv/gitlab-runner/config:/etc/gitlab-runner \
  -v /var/run/docker.sock:/var/run/docker.sock \
  gitlab/gitlab-runner:latest
```

## Register Runner
```bash
# Interactive registration
sudo gitlab-runner register

# Non-interactive
sudo gitlab-runner register \
  --url "https://gitlab.com/" \
  --registration-token "PROJECT_REGISTRATION_TOKEN" \
  --executor "docker" \
  --docker-image alpine:latest \
  --description "docker-runner"
```

## Configuration
```bash
# Edit config
sudo nano /etc/gitlab-runner/config.toml

# Start runner
sudo gitlab-runner start

# Check status
sudo gitlab-runner status
```

## Docker Executor Config
```toml
[[runners]]
  name = "docker-runner"
  url = "https://gitlab.com/"
  token = "TOKEN"
  executor = "docker"
  [runners.docker]
    image = "alpine:latest"
    privileged = false
    volumes = ["/cache"]
```
