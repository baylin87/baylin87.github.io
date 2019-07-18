---
title: docker machine
date: 2017-08-14 12:50:30
tags: [Docker,docker-machine]
comments: true
categories: 系统运维
---



> [官方参考文档](https://docs.docker.com/machine/overview/)
>
> [阿里Docker CE镜像源站](https://yq.aliyun.com/articles/110806)

# What is Docker Machine ?

Docker Machine is **a tool that lets you install Docker Engine on virtual hosts**, and manage the hosts with `docker-machine` commands. You can use Machine to create Docker hosts on your local Mac or Windows box, on your company network, in your data center, or on cloud providers like Azure, AWS, or Digital Ocean.

当在一台宿主机上安装docker，我们需要如下几个步骤（Ubuntu）

1. 安装 https CA 证书
2. 添加 GPG key
3. 添加 docker apt 源
4. 安装 docker

但对于多主机环境，每台都如此则显得重复工作且效率低下，因此有了docker-machine---能在多台主机上批量安装配置docker，主机可以是本地虚拟机、物理机、云主机等，支持环境如下：

1. 常规 Linux 操作系统
2. 虚拟化平台 - VirtualBox、VMWare、Hyper-V
3. OpenStack
4. 公有云 - Amazon Web Services、Microsoft Azure、Google Compute Engine、Digital Ocean 等

在docker machine中，都称之为Provider，不同Provider对应不同driver来安装配置docker host。

<!--more-->

# Install Docker Machine

1. Install Docker 

   ```bash
   curl -sSL http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/internet | sh -
   ```

   If you would like to use Docker as a non-root user, you should now consider
   adding your user to the "docker" group with something like:

     ```bash
   sudo usermod -aG docker <your username>
     ```

   **[配置镜像加速器](https://yq.aliyun.com/articles/29941?spm=5176.100239.blogcont110806.19.d0XnVG)**

   ```bash
   sudo mkdir -p /etc/docker
   sudo tee /etc/docker/daemon.json <<-'EOF'
   {
     "registry-mirrors": ["填入你的阿里云专属加速器地址"]
   }
   EOF
   sudo systemctl daemon-reload
   sudo systemctl restart docker
   ```

   ​

2. Download the Docker Machine binary and extract it to your PATH.

   ```bash
   $ curl -L https://github.com/docker/machine/releases/download/v0.12.2/docker-machine-`uname -s`-`uname -m` >/tmp/docker-machine &&
   chmod +x /tmp/docker-machine &&
   sudo cp /tmp/docker-machine /usr/local/bin/docker-machine
   ```

   ​

3. Check the installation by displaying the Machine version:

   ```bash
   $ docker-machine version
   docker-machine version 0.12.2, build 9371605
   ```

   ​

4. Installing bash completion scripts

   ```bash
   scripts=( docker-machine-prompt.bash docker-machine-wrapper.bash docker-machine.bash ); for i in "${scripts[@]}"; do sudo wget https://raw.githubusercontent.com/docker/machine/v0.12.2/contrib/completion/bash/${i} -P /etc/bash_completion.d; done

   ```

   To enable the `docker-machine` shell prompt, add `$(__docker_machine_ps1)` to your `PS1`setting in `~/.bashrc`.

   ```bash
   PS1='[\u@\h \W$(__docker_machine_ps1)]\$ '
   ```

# Use Machine to run Docker containers

To run a Docker container,you:

- create a new(or start an exiting) Docker virtual machine
- switch your environment to your new VM
- use the docker client to create,load and manage containers

## Machine drivers---Generic

Create machines using an existing VM/Host with SSH.

This is useful if you are using a provider that Machine does not support directly or if you would like to import an existing host to allow Docker Machine to manage.

The driver will perform a list of tasks on create:

- If docker is not running on the host, it will be installed automatically.
- It will update the host packages (`apt-get update`, `yum update`…).
- It will generate certificates to secure the docker daemon.
- The docker daemon will be restarted, thus all running containers will be stopped.
- The hostname will be changed to fit the machine name.

```shell
docker-machine create --driver generic --generic-ip-address=192.168.56.104 host1
```

### Sudo privileges

The user that is used to SSH into the host can be specified with `--generic-ssh-user` flag. This user has to have password-less sudo privileges. If it’s not the case, you need to edit the `sudoers`file and configure the user as a sudoer with `NOPASSWD`. See https://help.ubuntu.com/community/Sudoers .

#### ENVIRONMENT VARIABLES AND DEFAULT VALUES

| CLI option               | Environment variable  | Default |
| ------------------------ | --------------------- | ------- |
| `--generic-engine-port`  | `GENERIC_ENGINE_PORT` | `2376`  |
| **--generic-ip-address** | `GENERIC_IP_ADDRESS`  | -       |
| `--generic-ssh-key`      | `GENERIC_SSH_KEY`     | -       |
| `--generic-ssh-user`     | `GENERIC_SSH_USER`    | `root`  |
| `--generic-ssh-port`     | `GENERIC_SSH_PORT`    | `22`    |



## create 过程：

① 通过 ssh 登录到远程主机。
② 安装 docker。
③ 拷贝证书。
④ 配置 docker daemon。
⑤ 启动 docker。



# Machine CLI

```shell
Options:

  --debug, -D						Enable debug mode

  --storage-path, -s "/home/binlv/.docker/machine"	Configures storage path [$MACHINE_STORAGE_PATH]

  --tls-ca-cert 					CA to verify remotes against [$MACHINE_TLS_CA_CERT]

  --tls-ca-key 						Private key to generate certificates [$MACHINE_TLS_CA_KEY]

  --tls-client-cert 					Client cert to use for TLS [$MACHINE_TLS_CLIENT_CERT]

  --tls-client-key 					Private key used in client TLS auth [$MACHINE_TLS_CLIENT_KEY]

  --github-api-token 					Token to use for requests to the Github API [$MACHINE_GITHUB_API_TOKEN]

  --native-ssh						Use the native (Go-based) SSH implementation. [$MACHINE_NATIVE_SSH]

  --bugsnag-api-token 					BugSnag API token for crash reporting [$MACHINE_BUGSNAG_API_TOKEN]

  --help, -h						show help

  --version, -v						print the version

```



```shell
Commands:

  active				Print which machine is active

  config				Print the connection config for machine

  create				Create a machine

  env				Display the commands to set up the environment for the Docker client

  inspect				Inspect information about a machine

  ip					Get the IP address of a machine

  kill					Kill a machine

  ls					List machines

  provision			Re-provision existing machines

  regenerate-certs	Regenerate TLS Certificates for a machine

  restart				Restart a machine

  rm					Remove a machine

  ssh				Log into or run a command on a machine with SSH.

  scp				Copy files between machines

  start				Start a machine

  status				Get the status of a machine

  stop				Stop a machine

  upgrade			Upgrade a machine to the latest version of Docker

  url					Get the URL of a machine

  version				Show the Docker Machine version or a machine docker version

  help				Shows a list of commands or help for one command

```



