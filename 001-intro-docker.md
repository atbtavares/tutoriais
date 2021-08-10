# Tutorial hands-on docker

## Comandos utilizados neste tutorial

```
docker images

docker ps

docker ps -a

docker run ubuntu /bin/echo Hello!

docker run -i -t ubuntu /bin/bash

docker run -it --name app_nginx ubuntu

docker rm "ad96e9bfb220ab5f5b86bfe8a34f3b1c0b80ebaa8b8cd838150b10b487ad5119"

docker commit 43576c70d511 ubuntu/app_nginx

docker run -it --rm -p 8080:80 --name app_nginx ubuntu/app_nginx

docker run -it --rm -p 8080:80 --name app_nginx ubuntu/app_nginx /bin/bash

docker run -d -p 8080:80 --name app_nginx ubuntu/nginx /usr/sbin/nginx -g "daemon off;"
```



## Comandos Muito Básicos

Alguns comandos básicos para verificar dados da versão e informações da instalação do docker

```
docker
docker vesion # versão da engine
docker info   # informações da instalação do docker
```

Comando **docker** executado:

```
atbta@ANDRE:~/tutoriais$ docker

Usage:  docker [OPTIONS] COMMAND

A self-sufficient runtime for containers

Options:
      --config string      Location of client config files (default "/home/atbta/.docker")
  -c, --context string     Name of the context to use to connect to the daemon (overrides DOCKER_HOST env var and default context set with "docker
                           context use")
  -D, --debug              Enable debug mode
  -H, --host list          Daemon socket(s) to connect to
  -l, --log-level string   Set the logging level ("debug"|"info"|"warn"|"error"|"fatal") (default "info")
      --tls                Use TLS; implied by --tlsverify
      --tlscacert string   Trust certs signed only by this CA (default "/home/atbta/.docker/ca.pem")
      --tlscert string     Path to TLS certificate file (default "/home/atbta/.docker/cert.pem")
      --tlskey string      Path to TLS key file (default "/home/atbta/.docker/key.pem")
      --tlsverify          Use TLS and verify the remote
  -v, --version            Print version information and quit

Management Commands:
  builder     Manage builds
  buildx*     Build with BuildKit (Docker Inc., v0.5.1-docker)
  compose*    Docker Compose (Docker Inc., v2.0.0-beta.6)
  config      Manage Docker configs
  container   Manage containers
  context     Manage contexts
  image       Manage images
  manifest    Manage Docker image manifests and manifest lists
  network     Manage networks
  node        Manage Swarm nodes
  plugin      Manage plugins
  scan*       Docker Scan (Docker Inc., v0.8.0)
  secret      Manage Docker secrets
  service     Manage services
  stack       Manage Docker stacks
  swarm       Manage Swarm
  system      Manage Docker
  trust       Manage trust on Docker images
  volume      Manage volumes

Commands:
  attach      Attach local standard input, output, and error streams to a running container
  build       Build an image from a Dockerfile
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  events      Get real time events from the server
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  history     Show the history of an image
  images      List images
  import      Import the contents from a tarball to create a filesystem image
  info        Display system-wide information
  inspect     Return low-level information on Docker objects
  kill        Kill one or more running containers
  load        Load an image from a tar archive or STDIN
  login       Log in to a Docker registry
  logout      Log out from a Docker registry
  logs        Fetch the logs of a container
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  ps          List containers
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  rmi         Remove one or more images
  run         Run a command in a new container
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  search      Search the Docker Hub for images
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  version     Show the Docker version information
  wait        Block until one or more containers stop, then print their exit codes

Run 'docker COMMAND --help' for more information on a command.

To get more help with docker, check out our guides at https://docs.docker.com/go/guides/
```

Comando **docker version** executado:

```
atbta@ANDRE:~$ docker version
Client: Docker Engine - Community
 Cloud integration: 1.0.17
 Version:           20.10.7
 API version:       1.41
 Go version:        go1.13.15
 Git commit:        f0df350
 Built:             Wed Jun  2 11:56:47 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.7
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       b0f5bc3
  Built:            Wed Jun  2 11:54:58 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.6
  GitCommit:        d71fcd7d8303cbf684402823e425e9dd2e99285d
 runc:
  Version:          1.0.0-rc95
  GitCommit:        b9ee9c6314599f1b4a7f497e1f1f856fe433d3b7
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

Comando **docker info** executado:

```
atbta@ANDRE:~$ docker info
Client:
 Context:    default
 Debug Mode: false
 Plugins:
  buildx: Build with BuildKit (Docker Inc., v0.5.1-docker)
  compose: Docker Compose (Docker Inc., v2.0.0-beta.6)
  scan: Docker Scan (Docker Inc., v0.8.0)

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 20.10.7
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Cgroup Version: 1
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 io.containerd.runtime.v1.linux runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: d71fcd7d8303cbf684402823e425e9dd2e99285d
 runc version: b9ee9c6314599f1b4a7f497e1f1f856fe433d3b7
 init version: de40ad0
 Security Options:
  seccomp
   Profile: default
 Kernel Version: 5.10.16.3-microsoft-standard-WSL2
 Operating System: Docker Desktop
 OSType: linux
 Architecture: x86_64
 CPUs: 8
 Total Memory: 4.524GiB
 Name: docker-desktop
 ID: NB66:KDLB:THLW:HMEB:LMPS:LO24:Z432:3JIU:CEHV:UPDT:26YR:CCFQ
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Live Restore Enabled: false
```



## INÍCIO - Executando um container com ubuntu

Vamos executar um container com **ubuntu** que imprima na tela **Hello** usando o comand  **`echo`**. O comando **`docker run`** é a criação do processo no SO Host, a criação do container.

```bash
docker run ubuntu /bin/echo Hello!
```

Como não tem imagem do **ubuntu** na máquina local ela é baixada do Docker Hub. Após o docker baixar o **ubuntu** é executado o binário **`bin/echo`** dele para o argumento "**Hello!**"

```
atbta@ANDRE:~$ docker run ubuntu /bin/echo Hello!
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
16ec32c2132b: Pull complete
Digest: sha256:82becede498899ec668628e7cb0ad87b6e1c371cb8a1e597d83a47fac21d6af3
Status: Downloaded newer image for ubuntu:latest
Hello!
```

Onde aparece **`16ec32c2132b: Pull complete`** é uma camada. Neste caso há somente uma, mas quanto mais complexa a imagem mais camadas terá.

As camadas no docker podem ser reaproveitadas em outras imagens: se a gente instalar o debian, ele não vai baixar tudo do debian mas vai usar o que for possível do ubuntu que já baixou antes, em outras palavras, uma ou outra camada da imagem do ubuntu. 

Podemos usar o comando **`docker images`** para verificar que foi executado o passo de baixar a imagem do ubuntu no comando **`docker run`** realizado anteriormente:

```bash
atbta@ANDRE:~$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
ubuntu       latest    1318b700e415   2 weeks ago   72.8MB
```

É baixado do ubuntu somente a parte que é necessária para executar o comando solicitado pela aplicação.

Quando executamos novamente a imagem já está presente e não é mais baixada:

```
atbta@ANDRE:~$ docker run ubuntu /bin/echo Hello! 2
Hello! 2
```

Um novo container com ubuntu foi criado, o comando bash echo foi executado e seu resultado foi para a sua saída padrão "Hello! 2". Para verificar que o container executou e terminou podemos usar o comando :

```bash
docker ps -a
```

O parametro `-a` mostra os containers que terminaram a sua execução:

```
atbta@ANDRE:~$ docker ps -a
CONTAINER ID  IMAGE   COMMAND             CREATED            STATUS                       PORTS   NAMES
e3cb1eee0ce0  ubuntu  "/bin/echo Hello! 2" 17 seconds ago     Exited (0) 17 seconds ago           competent_hawking
3814c7c99d86  ubuntu  "/bin/echo Hello!"  About a minute ago Exited (0) About a minute ago        gracious_saha
```

Um container é volátil e não se deve salvar dados nele. Após o término de sua execução ele é destruido.



## Verificando alguns comandos de ajuda

O comando `docker ps --help` mostra outros parâmetros e funções do `docker ps` 

```
atbta@ANDRE:~$ docker ps --help

Usage:  docker ps [OPTIONS]

List containers

Options:
  -a, --all             Show all containers (default shows just running)
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print containers using a Go template
  -n, --last int        Show n last created containers (includes all states) (default -1)
  -l, --latest          Show the latest created container (includes all states)
      --no-trunc        Don't truncate output
  -q, --quiet           Only display container IDs
  -s, --size            Display total file sizes

```

O docker run também tem uma lista enorme de parâmetros e suas funções

```
atbta@ANDRE:~$ docker run --help

Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Run a command in a new container

Options:
      --add-host list                  Add a custom host-to-IP mapping (host:ip)
  -a, --attach list                    Attach to STDIN, STDOUT or STDERR
      --blkio-weight uint16            Block IO (relative weight), between 10 and 1000, or 0 to disable (default 0)
      --blkio-weight-device list       Block IO weight (relative device weight) (default [])
      --cap-add list                   Add Linux capabilities
      --cap-drop list                  Drop Linux capabilities
      --cgroup-parent string           Optional parent cgroup for the container
      --cgroupns string                Cgroup namespace to use (host|private)
                                       'host':    Run the container in the Docker host's cgroup namespace
                                       'private': Run the container in its own private cgroup namespace
                                       '':        Use the cgroup namespace as configured by the
                                                  default-cgroupns-mode option on the daemon (default)
      --cidfile string                 Write the container ID to the file
      --cpu-period int                 Limit CPU CFS (Completely Fair Scheduler) period
      --cpu-quota int                  Limit CPU CFS (Completely Fair Scheduler) quota
      --cpu-rt-period int              Limit CPU real-time period in microseconds
      --cpu-rt-runtime int             Limit CPU real-time runtime in microseconds
  -c, --cpu-shares int                 CPU shares (relative weight)
      --cpus decimal                   Number of CPUs
      --cpuset-cpus string             CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string             MEMs in which to allow execution (0-3, 0,1)
  -d, --detach                         Run container in background and print container ID
      --detach-keys string             Override the key sequence for detaching a container
      --device list                    Add a host device to the container
      --device-cgroup-rule list        Add a rule to the cgroup allowed devices list
      --device-read-bps list           Limit read rate (bytes per second) from a device (default [])
      --device-read-iops list          Limit read rate (IO per second) from a device (default [])
      --device-write-bps list          Limit write rate (bytes per second) to a device (default [])
      --device-write-iops list         Limit write rate (IO per second) to a device (default [])
      --disable-content-trust          Skip image verification (default true)
      --dns list                       Set custom DNS servers
      --dns-option list                Set DNS options
      --dns-search list                Set custom DNS search domains
      --domainname string              Container NIS domain name
      --entrypoint string              Overwrite the default ENTRYPOINT of the image
  -e, --env list                       Set environment variables
      --env-file list                  Read in a file of environment variables
      --expose list                    Expose a port or a range of ports
      --gpus gpu-request               GPU devices to add to the container ('all' to pass all GPUs)
      --group-add list                 Add additional groups to join
      --health-cmd string              Command to run to check health
      --health-interval duration       Time between running the check (ms|s|m|h) (default 0s)
      --health-retries int             Consecutive failures needed to report unhealthy
      --health-start-period duration   Start period for the container to initialize before starting health-retries countdown (ms|s|m|h) (default 0s)
      --health-timeout duration        Maximum time to allow one check to run (ms|s|m|h) (default 0s)
      --help                           Print usage
  -h, --hostname string                Container host name
      --init                           Run an init inside the container that forwards signals and reaps processes
  -i, --interactive                    Keep STDIN open even if not attached
      --ip string                      IPv4 address (e.g., 172.30.100.104)
      --ip6 string                     IPv6 address (e.g., 2001:db8::33)
      --ipc string                     IPC mode to use
      --isolation string               Container isolation technology
      --kernel-memory bytes            Kernel memory limit
  -l, --label list                     Set meta data on a container
      --label-file list                Read in a line delimited file of labels
      --link list                      Add link to another container
      --link-local-ip list             Container IPv4/IPv6 link-local addresses
      --log-driver string              Logging driver for the container
      --log-opt list                   Log driver options
      --mac-address string             Container MAC address (e.g., 92:d0:c6:0a:29:33)
  -m, --memory bytes                   Memory limit
      --memory-reservation bytes       Memory soft limit
      --memory-swap bytes              Swap limit equal to memory plus swap: '-1' to enable unlimited swap
      --memory-swappiness int          Tune container memory swappiness (0 to 100) (default -1)
      --mount mount                    Attach a filesystem mount to the container
      --name string                    Assign a name to the container
      --network network                Connect a container to a network
      --network-alias list             Add network-scoped alias for the container
      --no-healthcheck                 Disable any container-specified HEALTHCHECK
      --oom-kill-disable               Disable OOM Killer
      --oom-score-adj int              Tune host's OOM preferences (-1000 to 1000)
      --pid string                     PID namespace to use
      --pids-limit int                 Tune container pids limit (set -1 for unlimited)
      --platform string                Set platform if server is multi-platform capable
      --privileged                     Give extended privileges to this container
  -p, --publish list                   Publish a container's port(s) to the host
  -P, --publish-all                    Publish all exposed ports to random ports
      --pull string                    Pull image before running ("always"|"missing"|"never") (default "missing")
      --read-only                      Mount the container's root filesystem as read only
      --restart string                 Restart policy to apply when a container exits (default "no")
      --rm                             Automatically remove the container when it exits
      --runtime string                 Runtime to use for this container
      --security-opt list              Security Options
      --shm-size bytes                 Size of /dev/shm
      --sig-proxy                      Proxy received signals to the process (default true)
      --stop-signal string             Signal to stop a container (default "SIGTERM")
      --stop-timeout int               Timeout (in seconds) to stop a container
      --storage-opt list               Storage driver options for the container
      --sysctl map                     Sysctl options (default map[])
      --tmpfs list                     Mount a tmpfs directory
  -t, --tty                            Allocate a pseudo-TTY
      --ulimit ulimit                  Ulimit options (default [])
  -u, --user string                    Username or UID (format: <name|uid>[:<group|gid>])
      --userns string                  User namespace to use
      --uts string                     UTS namespace to use
  -v, --volume list                    Bind mount a volume
      --volume-driver string           Optional volume driver for the container
      --volumes-from list              Mount volumes from the specified container(s)
  -w, --workdir string                 Working directory inside the container
```



A seguir vamos usar os parâmetros `-i` e `-t` 

```
  -i, --interactive                    Keep STDIN open even if not attached
  -t, --tty                            Allocate a pseudo-TTY
```



## Container com terminal interativo de ubuntu 

Agora vamos criar um novo container com ubuntu e verificar parametros de rede para que possamos perceber um pouco como funciona a interação do SO hospedeiro com o SO do container. 

Vamos começar executando o `docker run`

```
docker run -i -t ubuntu /bin/bash
```

Agora podemos executar comandos dentro do container **ubuntu**

```
atbta@ANDRE:~$ docker run -i -t ubuntu /bin/bash
root@107fb53948b8:/#
```

Digitamos o comando `uname -a` e verificamos que realmente estamos dentro do container ao ver que o sistema é diferente do SO hospedeiro

```
root@107fb53948b8:/# uname -a
Linux 107fb53948b8 5.10.16.3-microsoft-standard-WSL2 #1 SMP Fri Apr 2 22:23:49 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

Vamos instalar as ferramentas para verificar a rede **apt update** e depois **apt install net-tools**:

```
root@107fb53948b8:/# apt update && apt install net-tools
Get:1 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]
Get:2 http://archive.ubuntu.com/ubuntu focal InRelease [265 kB]
Get:3 http://archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]
Get:4 http://security.ubuntu.com/ubuntu focal-security/multiverse amd64 Packages [30.6 kB]
Get:5 http://archive.ubuntu.com/ubuntu focal-backports InRelease [101 kB]
....
The following NEW packages will be installed:
  net-tools
0 upgraded, 1 newly installed, 0 to remove and 3 not upgraded.
Need to get 196 kB of archives.
...
(Reading database ... 4127 files and directories currently installed.)
Preparing to unpack .../net-tools_1.60+git20180626.aebd88e-1ubuntu1_amd64.deb ...
Unpacking net-tools (1.60+git20180626.aebd88e-1ubuntu1) ...
Setting up net-tools (1.60+git20180626.aebd88e-1ubuntu1) ...
root@107fb53948b8:/#

```

Podemos verificar a versão da distro:

```
root@107fb53948b8:/# cat /etc/lsb-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.2 LTS"
```

E também verificar o IP do SO do container

```
root@107fb53948b8:/# ifconfig eth0
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.2  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:ac:11:00:02  txqueuelen 0  (Ethernet)
        RX packets 13966  bytes 20241259 (20.2 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4765  bytes 265519 (265.5 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

A faixa de IP é **172.17.0.0** com máscara **255.255.0.0** que é bem diferente do IP do SO hospedeiro. Quando vamos criar uma interface de rede para o container usamos o IP do host somente. 

Evitamos utilizar o IP do container porque a cada **docker run** ele vai mudar. Então quando se trabalha com **portainer** não se utiliza o IP do container mas sim o IP do SO hospedeiro. Os DNSs que um cliente vê não vai ter o IP do container, mas sim do SO hospedeiro.

Podemos verificar que o id do container ubuntu que criamos está listado em /etc/host: **107fb53948b8**

```
root@107fb53948b8:/# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2      107fb53948b8
```

Como pedimos interatividade ao executar o **docker run** se sairmos do bash a execução é terminada. E assim termina o ciclo de execução do container. 

Assim como um **programa** em execução é um **processo**, também uma **imagem docker** em execução é um **container**.

```
root@107fb53948b8:/# exit
exit
atbta@ANDRE:~$
```

Em um possível deploy automático com containers não posso fazer algo dentro do container para resolver qualquer problema, ou até mesmo realizar alguma configuração. Toda parametrização de deploy automático deve ser feita na imagem. 

## Criando um container com nginx 

Para nomear um container basta usar no `docker run`o parâmetro `--name`:

`docker run -i -t --name app_nginx ubuntu`

Agora vamos configurar uma máquina com nginx a partir dos seguintes passos:

* Executar um container iterativo
* Dentro do container, vamos instalar o nginx
* Vamos encerrar a sessão

```
atbta@ANDRE:~$ docker run -it --name app_nginx ubuntu
root@df964faea9a1:/# apt update && apt install nginx -y
Get:1 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]
Get:2 http://archive.ubuntu.com/ubuntu focal InRelease [265 kB]
Get:3 http://security.ubuntu.com/ubuntu focal-security/restricted amd64 Packages [432 kB]
Get:4 http://security.ubuntu.com/ubuntu focal-security/main amd64 Packages [989 kB]
...
Setting up nginx-core (1.18.0-0ubuntu1.2) ...
invoke-rc.d: could not determine current runlevel
invoke-rc.d: policy-rc.d denied execution of start.
Setting up nginx (1.18.0-0ubuntu1.2) ...
root@df964faea9a1:/# exit
atbta@ANDRE:~$
```

Caso tenha o container tenha parado por algum motivo como: 1) o servidor do docker ter sido desligado ou ter terminado ou 2) você teclou ctrl+c para encerrar a sessão, ou ainda 3) foi executado exit no bash. Nesse caso o container terá sido destruído.

No trecho abaixo estamos nessas circunstancias e ao tentar fazer novamente o comando para iniciar o container teremos um erro. 

```
atbta@ANDRE:~$ docker run -it --name app_nginx ubuntu
docker: Error response from daemon: Conflict. The container name "/app_nginx" is already in use by container "df964faea9a137edcb0b30b851fc2b16ccf271b68dd44acecd9ba5683aeae98c". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
```

Para resolver vamos apagar esse container então iniciar um novo onde repetiremos as configurações, já que o antigo teve sua execução terminada. Lembre-se de que após executado o container perde a memória e o que foi instalado, bem como arquivos criados. 

```
docker rm "df964faea9a137edcb0b30b851fc2b16ccf271b68dd44acecd9ba5683aeae98c"
```

Isso cria uma imagem com base no que tinha no container anterior. Posteriormente quando trabalharmos mais com criação de imagens, será apresentado melhores formas

```

atbta@ANDRE:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

atbta@ANDRE:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND                CREATED        STATUS                      PORTS     NAMES
ad96e9bfb220   ubuntu    "bash"                 20 hours ago   Exited (255) 2 hours ago              app_nginx
d1d2bbebd819   ubuntu    "/bin/bash"            21 hours ago   Exited (100) 20 hours ago             determined_ptolemy
bb8dc1c7cbc3   ubuntu    "/bin/echo Hello! 2"   21 hours ago   Exited (0) 21 hours ago               goofy_kilby
d8691994ec21   ubuntu    "/bin/echo Hello!"     21 hours ago   Exited (0) 21 hours ago               trusting_hofstadter

atbta@ANDRE:~$ docker rm "ad96e9bfb220ab5f5b86bfe8a34f3b1c0b80ebaa8b8cd838150b10b487ad5119"
ad96e9bfb220ab5f5b86bfe8a34f3b1c0b80ebaa8b8cd838150b10b487ad5119

atbta@ANDRE:~$ docker run -it --name app_nginx ubuntu
root@264d8f062aca:/#
```

Agora podemos executar o comando `apt update && apt install -y nginx curl` dentro do container ubuntu/app_nginx

```
root@43576c70d511:/# apt update && apt install -y nginx curl
....
Setting up libcurl4:amd64 (7.68.0-1ubuntu2.6) ...
Setting up curl (7.68.0-1ubuntu2.6) ...
Processing triggers for libc-bin (2.31-0ubuntu9.2) ...
Processing triggers for ca-certificates (20210119~20.04.1) ...
Updating certificates in /etc/ssl/certs...
0 added, 0 removed; done.
Running hooks in /etc/ca-certificates/update.d...
done.
root@43576c70d511:/# 
```



Iniciamos o servidor e testamos com o `curl`

```
root@43576c70d511:/# nginx
root@43576c70d511:/# curl http://localhost
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
root@43576c70d511:/#
root@43576c70d511:/# exit
exit
```

Tudo OK. Após sair da máquina vamos executar `docker commit id ubuntu/app_nginx`para salvar o estado atual da máquina

```
atbta@ANDRE:~$ docker commit 43576c70d511 ubunto/app_nginx
sha256:3e3fcf1072d5c2ba75b0afc0760c613e680634631b8fdcd9f9e79eb8da7fbd2f
atbta@ANDRE:~$
```

Verificamos que a imagem criada é um pouco maior devido as instalações que foram realizadas.

```
atbta@ANDRE:~$ docker images
REPOSITORY         TAG       IMAGE ID       CREATED          SIZE
ubunto/app_nginx   latest    3e3fcf1072d5   48 seconds ago   173MB
ubuntu             latest    1318b700e415   2 weeks ago      72.8MB
```

 Usar  o `docker commit` não é recomendado para salvar uma imagem. Em outro momento veremos a forma mais adequada para realizar essa operação.

Agora vamos criar um container a partir da imagem criada `ubunto/app_nginx`

Dessa vez no `docker run` utilizaremos a flag `-rm` indica que o container deve ser removido após execução do processo; Já a flag `-p`, indica que há mapeamento entre ambientes interno e externo através entre de portas do container e do host.

```
docker run -it -rm -p 8080:80 --name app_nginx ubunto/app_nginx
```

O resultado é um conflito porque o nome `app_nginx`deve ser único já que é um id de container

```
atbta@ANDRE:~$ docker run -it --rm -p 8080:80 --name app_nginx ubunto/app_nginx
docker: Error response from daemon: Conflict. The container name "/app_nginx" is already in use by container "43576c70d5115271b4db2847758295983b78b59353467ad66661fe9b875525f6". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
```

Nesse caso podemos remover com `docker rm app_nginx` para executar o comando `docker run -it --rm -p 8080:80 --name app_nginx ubunto/app_nginx`novamente

```
atbta@ANDRE:~$ docker rm "43576c70d5115271b4db2847758295983b78b59353467ad66661fe9b875525f6"
43576c70d5115271b4db2847758295983b78b59353467ad66661fe9b875525f6
```

Vamos adicionar mais um parâmetro para que possamos utilizar o bash, além disso após iniciar o container também vamos rodar o serviço do nginx com o comando linux `service nginx start`

```
docker run -it -rm -p 8080:80 --name app_nginx ubunto/app_nginx /bin/bash
```

```
atbta@ANDRE:~$ docker run -it --rm -p 8080:80 --name app_nginx ubunto/app_nginx /bin/bash
root@0e1dafd5a6e1:/# service nginx start
 * Starting nginx nginx                                                                                     [ OK ]
 root@0e1dafd5a6e1:/#
```

Se você for em um navegador do SO host, então conseguirá acessar http://localhost:8080

Assim tornamos acessível o nginx executado dentro do container pelo navegador do SO hospedeiro

Vemos mais detalhes com o comando `netstat`

```

atbta@ANDRE:~/tutoriais$ sudo netstat -tlnp
[sudo] password for atbta:
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.0.1:6010          0.0.0.0:*               LISTEN      227/./wslconnect
tcp        0      0 127.0.0.1:6011          0.0.0.0:*               LISTEN      5853/./wslconnect
tcp6       0      0 :::8080                 :::*                    LISTEN      -
```

Quando usamos no `docker run` a opção `--rm` e terminamos o container, não sobra nenhum rastro da execução dele no `docker ps -a`. Além disso, o nginx também não fica mais rodando

```
atbta@ANDRE:~$ docker run -it --rm -p 8080:80 --name app_nginx ubunto/app_nginx /bin/bash
root@0e1dafd5a6e1:/# service nginx start
 * Starting nginx nginx                                                                                                                                [ OK ]
root@0e1dafd5a6e1:/# exit
exit
```

Então vamos executar o mesmo comando mas agora sem interatividade:

`docker run -d -p 8080:80 --name app_nginx ubunto/nginx /usr/sbin/nginx -g "daemon off;"` 

```
atbta@ANDRE:~$ docker run -d -p 8080:80 --name app_nginx ubuntu/nginx /usr/sbin/nginx -g "daemon off;"
Unable to find image 'ubuntu/nginx:latest' locally
latest: Pulling from ubuntu/nginx
ea301a4e9092: Pull complete
5a55ed894fa3: Pull complete
269462602d82: Pull complete
3c16aaaa1a67: Pull complete
d7ccd4b3ea3b: Pull complete
bb2c10db954a: Pull complete
Digest: sha256:ccdfd11a0710b0a7c16a2d4c5ed342830d56dd9553a93b0485df57aeb0b0e85b
Status: Downloaded newer image for ubuntu/nginx:latest
d2663f379b9a4d8c5d7e02bb2d81555ee0e82092227a4c05f4cdeed642452695

```

















Para remover o cache

```
atbta@ANDRE:~/tutoriais$ docker system prune -a
WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all images without at least one container associated to them
  - all build cache

Are you sure you want to continue? [y/N] y
Deleted Containers:
99a1b3256fbf69905d80524db3d9010c09a8cdca8e92c59eaa389e3865a084b2
dc32d576c873fb0440d5c10989ae3ca0a5328b3d687326a1260dfdd49b7cd3b3

Deleted build cache objects:
m6t4f00zjwkzwr73xcruspipe
on3p3ujpqc3gahfqiu8q9eond
ynywiphp76xf208awvt6j6df5
i7ogm3vgrrnvk9nhakxfdfsti
n497ex8l8fg0495f83i8c1u90
wl9u0q9dvtkqsb8ojg0c6plit
rr6w539d8hwuq0jsozw41dq6n
lej2u4uqzvtewqb8t2b5srb86
rvsihgdnmdexxqggtpq60ihkn
1tpazyxkfou673pdyt6ck74ev
tqmx7rltf0y0vevswy06sm2rl
md5dxiholy5jvcvlkge0xnwc9
5agmb69gtjxty7ttiq748028o
uvf61lug4isz01h0aftrw9ow7
iuc8pcwdxm7wmo1xmg15kq3y9
ckcv4ud2vm6g1xuir4kcpyt9m
1xxm2o4v77ryao1a81i7uz9kh
tnmi2tvkzvr3we32hs3ww3d0t
q22pcexlu2m6v22ls3hymrcb7
jvyr32cp7bfk4fhjrqav7rwa2
rveu5eecrbsmcs09529vfw3os
czntwjy6ix9lvray1n161au59
2csbuec2x2rmiw7nx22o4nlst
ks1n4cvczp2jp59h1e4oaxoj1
iksnk07zp435movalxhq0dpc4
w95drez58jdg7rrlhvcsipyzz
8918qpw5pfib5o4litr4n5u6s
rspt048el74dmet5im7fcjx1c
pm1bysb0lvjvv9zynm2q6w0g3
i01zzh6ag3erh11lqq0e7ib47
7l77m6bexkdse72hjj5ia60qe
loy3t285bivqk5h01j7snzuyp
znsnbpe177dev28gf5m0geawx
v2u9zmfmzpxbnrdv5k2zsnzev
kngwzivjyw685i8kpzi1y8ajv
f7wgh1ko08g753t7lesbsnb5j
vh7o7c6lm8ek7b6p80cb063fn
t66vfdm5qz4nd838txystekkx
3qksr97l2ezz6gx7ctc1j03x1
v2j0wks13lq73anpgyxh60kkw
chg0l7cyixnpidmyhevjpbviw
fyaafp249tnejjr8jxqvyzfk4
f4pzik6bncbi3rzgtjyyth4ud
hs94xla2d2cw9jcoaesmly4jd
fuo5otb1sjdpaqget23wala1p
tyk0k94z9wf3fftpvq8tafm6n
94mrtb9yhv1i32847mwu6zsoy
xo38p3e11yvbcyu018o7azgcb
z0rj55l2fchvcqoxe8vprm6zx
rjiwxhwjpp115ggzlvcxfm5mq
s1kpwj6cq09uzqfy46ptys9hx
y1zgcpd05twsti5n46q58wtcq
4b12sf5892tj2a7ooede0tpxl
6g8k5oualcuccrwcmbq9krofn
3imdx8qtcgbxdc3nfk1ot7lxo
sfjpf4job7ah2sztzlfanb1y9
v0wx40m7zs2cuochzgmmzauqw

Total reclaimed space: 490.5MB
```

