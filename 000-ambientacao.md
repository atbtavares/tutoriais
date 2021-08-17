# Tutorial hands-on docker (0/10)

Essa sessão é basicamente um overview simples e rápido. 

Requisitos:

* As outras páginas do tutorial são bem práticas e você já deve ter instalado o docker;
* A medida que for lendo, execute os comandos no seu computador.

A principal função do docker é a virtualização do ambiente da aplicação.

De forma bem direta: você "programa" o que a sua aplicação precisa para ser executada, definindo seus pacotes, configurações de servidor de aplicação, dependências de hardware e software. 

Você não vai precisar configurar novamente o ambiente de forma manual. 

Uma vez parametrizado o quanto de disco, processamento e memória, bem como versão/configuração do sistema operacional e/ou servidor de aplicação, você consegue replicar facilmente em outro sistema operacional. 

Alguns benefícios que você pode conseguir são:

|                        DESENVOLVEDOR                         |                        INFRAESTRUTURA                        | CLIENTE                                                      |
| :----------------------------------------------------------: | :----------------------------------------------------------: | ------------------------------------------------------------ |
| Configuração rápida do ambiente de desenvolvimento. É o fim dos projetos herdados em que há caça as configurações que o desenvolvedor fez. | Espaço de configuração separado por aplicação. Um ajuste no jboss ou no linux não afeta outras aplicações | Acesso a mais recursos de máquina se necessário              |
| Pode baixar da internet um ambiente completo já configurado e validado pelos fornecedores do software |       Simplificar a realização até de deploys manuais        | Cobrança mais precisa por recursos de harware utilizados, por sistema |
| Ambientes iguais de desenvolvimento, homologação, produção, testes, stages e treinamento - em relação a software | Ambientes mais fáceis de configurar: desenvolvimento, homologação, produção, testes, stages e treinamento - em relação ao hardware e a rede |                                                              |
| Maior controle nos requisitos da aplicação (hardware e software) | Uma aplicação que tem situação onde consome mais recursos que o previsto não afetará outras aplicações |                                                              |

Aceito sugestões de melhoria nos itens acima via chat do Teams (para Andre Tavares de Brito). Toda sugestão é bem vinda o tempo todo.

Nas aulas 1/10 e 2/10, você aprenderá 

* Criar um container com ubuntu
* Criar um container com ubuntu e um servidor nginx
* Criar uma imagem com a configuração desejada
* Criar um roteiro automatizado (Dockerfile) para configuração de uma imagem
* Mover uma imagem ou container de uma máquina para outra
* Baixar uma imagem pronta do Dockerhub

[TOC]

Você pode ir direto a próxima aula [aqui](https://github.com/atbtavares/tutoriais/blob/main/001-intro-docker.md)

## Comandos Muito Básicos

Alguns comandos básicos para verificar dados da versão e informações da instalação do docker

```
docker        # como usar
docker vesion # versão da engine
docker info   # informações da instalação do docker
```

Em sistemas operacionais, um **processo** é um programa em execução. Já no docker, um **container** é uma imagem em execução. 

Existem vários comandos para o docker, mas estes são os principais que veremos ao longo da primeira e da segunda aula: 

| **`docker run`**  | **`docker ps`**   | **`docker build`** | **`docker pull`** |
| ----------------- | ----------------- | ------------------ | ----------------- |
| **`docker push`** | **`docker logs`** | **`docker stats`** | **`docker load`** |
| **`docker save`** | **`docker stop`** | **`docker start`** |                   |

Você pode ver mais detalhes executando o comando **`docker`** no terminal:

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

Comando **`docker version`** executado:

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

Comando **`docker info`** traz informações do número de containers no docker, como também número de imagens e mais:

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



## Verificando alguns comandos de ajuda

O comando `docker ps --help` mostra outros parâmetros e funções do `docker ps` . Para cada par (como `docker ps`ou ainda `docker stats`) haverá um parâmetro `--help` que traz mais informações do seu uso.

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

O `docker run` também tem uma lista de parâmetros e suas funções:

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

Por enquanto é só e isso basta para uma ambientação rápida.

Próxima aula [001-intro-docker.md](https://github.com/atbtavares/tutoriais/blob/main/001-intro-docker.md)



Caso você tenha pouco espaço em disco, já existam muitas imagens carregadas e você quer deixar o ambiente mais enxuto você pode limpar o `docker`

## Para limpar o ambiente

Você pode **remover** todos os **containers parados**, **redes não usadas**, **imagens** sem container associados e todo o **cache de build** com o `system prune -a`. Do inglês, *prune*, significa: podar, suprimir, aparar ou desbastar.

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
5agmb69gtjxty7ttiq748028o
uvf61lug4isz01h0aftrw9ow7
iuc8pcwdxm7wmo1xmg15kq3y9
ckcv4ud2vm6g1xuir4kcpyt9m
1xxm2o4v77ryao1a81i7uz9kh
tnmi2tvkzvr3we32hs3ww3d0t
q22pcexlu2m6v22ls3hymrcb7
jvyr32cp7bfk4fhjrqav7rwa2
4b12sf5892tj2a7ooede0tpxl
6g8k5oualcuccrwcmbq9krofn
3imdx8qtcgbxdc3nfk1ot7lxo
sfjpf4job7ah2sztzlfanb1y9
v0wx40m7zs2cuochzgmmzauqw

Total reclaimed space: 490.5MB
```

