Alguns comandos básicos

```
docker vesion # versão da engine
docker info   # informações da instalação do docker
```

Exemplos desses comandos executados:

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



Vamos executar um container que imprima na tela "Hello!"

```bash
docker run ubuntu /bin/echo Hello!
```

Como não tem imagem do ubuntu na máquina local ela é baixada antes da execução do  comando

Nesse comando, após o docker baixar o ubuntu é acessado o binário /bin/echo e executado para o argumento "Hello!"

```
atbta@ANDRE:~$ docker run ubuntu /bin/echo Hello!
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
16ec32c2132b: Pull complete
Digest: sha256:82becede498899ec668628e7cb0ad87b6e1c371cb8a1e597d83a47fac21d6af3
Status: Downloaded newer image for ubuntu:latest
Hello!
```

Podemos usar o comando docker images para verificar que o passo de baixar a imagem do ubuntu no comando anterior foi executado:

```bash
atbta@ANDRE:~$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
ubuntu       latest    1318b700e415   13 days ago   72.8MB
```

É baixado do ubuntu somente a parte que é necessária para executar o comando solicitado pela aplicação.

Quando executamos novamente a imagem já está presente e não é mais baixada:

```
atbta@ANDRE:~$ docker run ubuntu /bin/echo Hello! 2
Hello! 2
```

O container com ubuntu foi criado, o comando bash echo foi executado e seu resultado foi para a sua saída padrão. Para verificar que o comando terminou de executar podemos usar o comando 

```bash
docker ps -a
```

O parametro -a mostra os containers que terminaram a sua execução

```
atbta@ANDRE:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND                CREATED          STATUS                      PORTS     NAMES
bb8dc1c7cbc3   ubuntu    "/bin/echo Hello! 2"   39 seconds ago   Exited (0) 38 seconds ago             goofy_kilby
d8691994ec21   ubuntu    "/bin/echo Hello!"     3 minutes ago    Exited (0) 3 minutes ago              trusting_hofstadt
```

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



Agora vamos usar os parâmetros `-i` e `-t` 

```
  -i, --interactive                    Keep STDIN open even if not attached
  -t, --tty                            Allocate a pseudo-TTY
```



Isso irá nos prover acesso ao container conforme você pode verificar abaixo:

```
atbta@ANDRE:~$ docker run -i -t ubuntu /bin/bash
root@d1d2bbebd819:/#
root@d1d2bbebd819:/# uname -a
Linux d1d2bbebd819 5.10.16.3-microsoft-standard-WSL2 #1 SMP Fri Apr 2 22:23:49 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux

```

Lá dentro executamos o comando para verificar a versão da distribuição. Podemos executar alguns outros comandos dentro do container que estamos acessando

```
root@d1d2bbebd819:/# apt update
Get:1 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]
Get:2 http://archive.ubuntu.com/ubuntu focal InRelease [265 kB]
Get:3 http://security.ubuntu.com/ubuntu focal-security/multiverse amd64 Packages [30.6 kB]
Get:4 http://security.ubuntu.com/ubuntu focal-security/restricted amd64 Packages [432 kB]
Get:5 http://archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]
Get:6 http://security.ubuntu.com/ubuntu focal-security/main amd64 Packages [989 kB]
Get:7 http://archive.ubuntu.com/ubuntu focal-backports InRelease [101 kB]
Get:8 http://archive.ubuntu.com/ubuntu focal/universe amd64 Packages [11.3 MB]
Get:9 http://security.ubuntu.com/ubuntu focal-security/universe amd64 Packages [786 kB]
Get:10 http://archive.ubuntu.com/ubuntu focal/restricted amd64 Packages [33.4 kB]
Get:11 http://archive.ubuntu.com/ubuntu focal/multiverse amd64 Packages [177 kB]
Get:12 http://archive.ubuntu.com/ubuntu focal/main amd64 Packages [1275 kB]
Get:13 http://archive.ubuntu.com/ubuntu focal-updates/restricted amd64 Packages [478 kB]
Get:14 http://archive.ubuntu.com/ubuntu focal-updates/universe amd64 Packages [1056 kB]
Get:15 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 Packages [1427 kB]
Get:16 http://archive.ubuntu.com/ubuntu focal-updates/multiverse amd64 Packages [39.0 kB]
Get:17 http://archive.ubuntu.com/ubuntu focal-backports/main amd64 Packages [2668 B]
Get:18 http://archive.ubuntu.com/ubuntu focal-backports/universe amd64 Packages [6319 B]
Fetched 18.7 MB in 1min 7s (277 kB/s)
Reading package lists... Done
Building dependency tree
Reading state information... Done
3 packages can be upgraded. Run 'apt list --upgradable' to see them.

root@d1d2bbebd819:/# cat /etc/lsb-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.2 LTS"

root@d1d2bbebd819:/# exit
exit

atbta@ANDRE:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND                CREATED          STATUS                       PORTS     NAMES
d1d2bbebd819   ubuntu    "/bin/bash"            21 minutes ago   Exited (100) 5 seconds ago             determined_ptolemy
bb8dc1c7cbc3   ubuntu    "/bin/echo Hello! 2"   38 minutes ago   Exited (0) 37 minutes ago              goofy_kilby
d8691994ec21   ubuntu    "/bin/echo Hello!"     40 minutes ago   Exited (0) 40 minutes ago              trusting_hofstadter

atbta@ANDRE:~$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
ubuntu       latest    1318b700e415   13 days ago   72.8MB

```



Agora vamos configurar uma máquina com nginx a partir dos seguintes passos:

* Executar um container iterativo
* Dentro do container, vamos instalar o nginx



```
atbta@ANDRE:~$ docker run -it --name app_nginx ubuntu
root@ad96e9bfb220:/# apt update
Get:1 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]
Get:2 http://archive.ubuntu.com/ubuntu focal InRelease [265 kB]
Get:3 http://security.ubuntu.com/ubuntu focal-security/restricted amd64 Packages [432 kB]
Get:4 http://security.ubuntu.com/ubuntu focal-security/main amd64 Packages [989 kB]
Get:5 http://archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]
Get:6 http://archive.ubuntu.com/ubuntu focal-backports InRelease [101 kB]
Get:7 http://security.ubuntu.com/ubuntu focal-security/universe amd64 Packages [786 kB]
Get:8 http://security.ubuntu.com/ubuntu focal-security/multiverse amd64 Packages [30.6 kB]
...
Setting up nginx-core (1.18.0-0ubuntu1.2) ...
invoke-rc.d: could not determine current runlevel
invoke-rc.d: policy-rc.d denied execution of start.
Setting up nginx (1.18.0-0ubuntu1.2) ...
Processing triggers for libc-bin (2.31-0ubuntu9.2) ...
root@ad96e9bfb220:/#

```



OK















​	

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

