
#  Galaxy Interactive Tools Configuration Guide

This guide walks you through the setup process required to enable **Galaxy Interactive Tools (ITs)** using **Docker**. These configurations allow Galaxy to run tools like Jupyter, RStudio, etc., in isolated Docker containers.

---

## Prerequisites

### Install Docker

> **Note:** Before proceeding, make sure **Docker is installed** on your system. Galaxy uses Docker to manage interactive environments securely and efficiently.  
>  
> 🔗 [Get Docker for your OS](https://docs.docker.com/get-docker/)

---

## Step 1: Configure `job_conf.yml`

###  File Location:
- `config/job_conf.xml`

If the file doesn’t exist, you can create it in one of the following ways:

```bash
# Method 1: Copy from sample
cp config/job_conf.sample.yml config/job_conf.yml

# Method 2: Create a new file manually
touch config/job_conf.yml
```

### Insert the Following YML Configuration:

```yml

runners:
  local:
    load: galaxy.jobs.runners.local:LocalJobRunner
    workers: 4

execution:
  default: docker_dispatch
  environments:
    local:
      runner: local

    docker_local:
      runner: local
      docker_enabled: true
      docker_volumes: $defaults
      docker_sudo: false
      docker_net: bridge
      docker_auto_rm: false
      #docker_set_user: $UID
      docker_set_user:
      docker_run_extra_arguments: --add-host localhost:host-gateway
      require_container: true
      container_monitor: true

    docker_dispatch:
      runner: dynamic
      type: docker_dispatch
      docker_destination_id: docker_local
      default_destination_id: local
```

---

## Step 2: Modify `galaxy.yml`

### File Location:
- `config/galaxy.yml`

If `galaxy.yml` doesn't exist, create it from the sample file:

```bash
cp config/galaxy.yml.sample config/galaxy.yml
```

###  Modify or Add the Following Settings

Search for the following keys in the `galaxy.yml` file and make sure they are set to these values:

```yaml
  gx_it_proxy:                                                                      # 177
    enable: true                                                                    # 180
    port: 4002                                                                      # 189

  galaxy:
    interactivetools_enable: true                                                   # 1344
    interactivetools_proxy_host: localhost:4002                                     # 1353
    interactivetools_map: interactivetools_map.sqlite                               # 1362

 galaxy_infrastructure_url: http://localhost:8080                                   # 1438
 
outputs_to_working_directory: true                                                  # 2576
 
  # Essential: Prevents 404 errors when launching interactive tools
  interactivetools_upstream_proxy: false```

You might come across a couple of minor issues:
- galaxy database schema being outdated or not initialized for interactive tools.

Run: 

```
    scripts/manage_db.py upgrade
```

- If on wsl docker might fail to pull images because it attempts to use ipv6, explicitly configure it to use ipv4.

---

## Summary

| Component        | File              | What to Do                                |
|------------------|-------------------|--------------------------------------------|
| **Docker**       | _System-wide_     | Install Docker from [docker.com](https://docs.docker.com/get-docker) |
| **Job Settings** | `config/job_conf.xml` | Add Docker job destinations and plugin configs |
| **YAML Config**  | `config/galaxy.yml`   | Enable interactive tools and proxy settings |

---

##  Next Step: Test Your Setup

1. Start your Galaxy instance.
2. Try launching an interactive tool .
3. Ensure that a Docker container starts and opens in a new tab.

If you see a 404 error, double-check that:
- `interactivetools_upstream_proxy` is set to `false`
- Docker is running

---
