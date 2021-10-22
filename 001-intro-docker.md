# Tutorial hands-on docker (1/10)

## Executando um container com ubuntu

Vamos executar um container com **ubuntu** que imprima na tela **Hello** usando o comand  **`echo`**. O comando **`docker run`** é a criação do processo no SO Host, a criação do container.

```bash
docker run ubuntu /bin/echo Hello!
```

Como não tem imagem do **ubuntu** na máquina local ela é baixada do Docker Hub. Após o docker baixar o **ubuntu** é executado o binário **`/bin/echo`** dele para o argumento "**Hello!**"

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

A faixa de IP é **172.17.0.0** com máscara **255.255.0.0** que é bem diferente do IP do SO hospedeiro. 
Quando criamos uma interface de rede para o container usamos somente o IP do host.

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

Assim como um **programa** em execução é um **processo**, de forma semelhante uma **imagem docker** em execução é um **container**.

Vamos sair do container para o host:

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

Depois de sair do container, ao tentar fazer novamente o comando para iniciar o container teremos o seguinte erro. 

```
atbta@ANDRE:~$ docker run -it --name app_nginx ubuntu
docker: Error response from daemon: Conflict. The container name "ubuntu/app_nginx" is already in use by container "df964faea9a137edcb0b30b851fc2b16ccf271b68dd44acecd9ba5683aeae98c". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
```

Isso aconteceu porque utilizamos o mesmo nome ```app_nginx```

Para resolver vamos apagar esse container então iniciar um novo onde repetiremos as configurações, já que o antigo teve sua execução terminada. Lembre-se de que após executado o container perde a memória e o que foi instalado, bem como arquivos criados. 

```
docker rm "df964faea9a137edcb0b30b851fc2b16ccf271b68dd44acecd9ba5683aeae98c"
```

```
atbta@ANDRE:~$ docker rm "df964faea9a137edcb0b30b851fc2b16ccf271b68dd44acecd9ba5683aeae98c"
df964faea9a137edcb0b30b851fc2b16ccf271b68dd44acecd9ba5683aeae98c

atbta@ANDRE:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

atbta@ANDRE:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND                CREATED          STATUS                   PORTS  NAMES
107fb53948b8   ubuntu    "/bin/bash"            14 minutes ago   Exited (0) 6 minutes ago        modest_cray
e3cb1eee0ce0   ubuntu    "/bin/echo Hello! 2"   17 minutes ago   Exited (0) 17 minutes ago       competent_hawking
3814c7c99d86   ubuntu    "/bin/echo Hello!"     19 minutes ago   Exited (0) 19 minutes ago       gracious_saha
```

Então criamos um novo container

```
atbta@ANDRE:~$ docker run -it --name app_nginx ubuntu
root@264d8f062aca:/#
```

Agora podemos executar o comando `apt update && apt install -y nginx curl` dentro do container app_nginx

```
root@7cdee1f4cf18:/# apt update && apt install -y nginx curl
....
Setting up libcurl4:amd64 (7.68.0-1ubuntu2.6) ...
Setting up curl (7.68.0-1ubuntu2.6) ...
Processing triggers for libc-bin (2.31-0ubuntu9.2) ...
Processing triggers for ca-certificates (20210119~20.04.1) ...
Updating certificates in /etc/ssl/certs...
0 added, 0 removed; done.
Running hooks in /etc/ca-certificates/update.d...
done.
root@7cdee1f4cf18:/# 
```

Iniciamos o servidor e testamos com o `curl`

```
root@7cdee1f4cf18:/# nginx
root@7cdee1f4cf18:/# curl http://localhost
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
root@7cdee1f4cf18:/#
root@7cdee1f4cf18:/# exit
exit
```

Tudo OK. Após sair do container com  `exit` vamos executar `docker commit app_nginx` para salvar o estado atual da máquina (container) em uma imagem com o nome ubuntu/app_nginx

Deve-se usar o mesmo id desse container que teve a execução terminada `7cdee1f4cf18`


```
atbta@ANDRE:~$ docker commit 7cdee1f4cf18 ubuntu/app_nginx
1b2fae00a95c460a50628c7d068aa34a4d0920543e08547dff93f6b834d6b17a
atbta@ANDRE:~$
```



Verificamos que a imagem criada é maior devido as instalações que foram realizadas: pacotes nginx e curl

```
atbta@ANDRE:~$ docker images
REPOSITORY         TAG       IMAGE ID       CREATED          SIZE
ubuntu/app_nginx   latest    1b2fae00a95c   26 seconds ago   173MB
ubuntu             latest    1318b700e415   2 weeks ago      72.8MB
```

 **Usar  o `docker commit` não é recomendado para salvar uma imagem**. Em outro momento veremos a forma mais adequada para realizar essa operação.

Agora vamos criar um **container** a partir da imagem criada **ubuntu/app_nginx**

Dessa vez no `docker run` utilizaremos a flag `-rm` indica que o container deve ser removido após execução do processo; Já a flag `-p`, indica que há mapeamento entre ambientes interno e externo através de portas do container e do host.

```
docker run -it --rm -p 8080:80 --name ubuntu/app_nginx app_nginx
```

O resultado é um conflito porque o nome `ubuntu/app_nginx`deve ser único

```
atbta@ANDRE:~$ docker run -it --rm -p 8080:80 --name app_nginx app_nginx
docker: Error response from daemon: Conflict. The container name "/app_nginx" is already in use by container "7cdee1f4cf187f35009f9b0e02b1e535db189136634df432787679abbde04ee9". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
```

Nesse caso podemos remover com `docker rm ubuntu/app_nginx` para executar o comando `docker run -it --rm -p 8080:80 --name app_nginx ubunto/app_nginx`novamente

```
atbta@ANDRE:~$ docker rm "7cdee1f4cf187f35009f9b0e02b1e535db189136634df432787679abbde04ee9"
7cdee1f4cf187f35009f9b0e02b1e535db189136634df432787679abbde04ee9
```

Vamos repetir o comando **docker run** adicionando mais um parâmetro para que possamos utilizar o bash, além disso após iniciar o container também vamos rodar o serviço do nginx com o comando linux `service nginx start`

```
docker run -it --rm -p 8080:80 --name app_nginx ubuntu/app_nginx /bin/bash
```

```
atbta@ANDRE:~$ docker run -it --rm -p 8080:80 --name app_nginx ubuntu/app_nginx /bin/bash
root@f4e8687fc6af:/# service nginx start
 * Starting nginx nginx                                                                                     [ OK ]
 root@f4e8687fc6af:/#
```

Se você for em um navegador do SO host, então conseguirá acessar http://localhost:8080. Se o SO hospedeiro é um ambiente linux, pode abrir um novo terminal e usar o comando ```curl http://localhost```  ou ainda o ```w3m http://localhost```. Para verificar o funcionamento do nginx internamente, você pode instalar o curl ou w3m e testar com o o link http://localhost. Note que a porta 80 do container é mapeada para o externo sendo acessível ao hospedeiro pela porta 8080

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

Quando usamos no `docker run` a opção `--rm` e terminamos o container, não sobra nenhum rastro da execução dele no `docker ps -a`, também é claro que o nginx também não está mais sendo executado. Você pode verificar no navegador do host o link http://localhost:8080

```
root@f4e8687fc6af:/# exit
exit
atbta@ANDRE:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND                CREATED       STATUS                   PORTS     NAMES
107fb53948b8   ubuntu    "/bin/bash"            2 hours ago   Exited (0) 2 hours ago             modest_cray
e3cb1eee0ce0   ubuntu    "/bin/echo Hello! 2"   2 hours ago   Exited (0) 2 hours ago             competent_hawking
3814c7c99d86   ubuntu    "/bin/echo Hello!"     2 hours ago   Exited (0) 2 hours ago             gracious_saha
```

Então vamos executar com o `docker run` a mesma imagem  `ubuntu/app_nginx` o mesmo comando mas agora sem interatividade (removeremos o `--rm`e adicionamos; adicionamos `-d` para executar o container em background e imprimir seu id), além disso vamos pedir para executar o nginx informando o binário /usr/sbin/nginx com os padrâmetros `-g "daemon off."`:

`docker run -d -p 8080:80 --name app_nginx ubuntu/app_nginx /usr/sbin/nginx -g "daemon off;"` 

```
atbta@ANDRE:~$ docker run -d -p 8080:80 --name app_nginx ubuntu/app_nginx /usr/sbin/nginx -g "daemon off;"
94330c076b26dfabf3d2af7990e138a09a18ca76a6231420583ed95a12e4e573
atbta@ANDRE:~$
```

Podemos verificar que o container está executando em background com `docker ps`

```
atbta@ANDRE:~$ docker ps
CONTAINER ID IMAGE            COMMAND                CREATE  STATUS  PORTS                                NAMES
94330c076b26 ubuntu/app_nginx "/usr/sbin/nginx -g …" 34 seco Up 32 s 0.0.0.0:8080->80/tcp, :::8080->80/tcpapp_nginx
```

Quando eu quiser executar um comando no terminal desse container posso usar `docker exec`

```
docker exec -it app_nginx /bin/bash
```

Então conseguimos acessar o bash do container e verificar o log do servidor nginx

```
atbta@ANDRE:~$ docker exec -it app_nginx /bin/bash
root@94330c076b26:/# uname -a
Linux 94330c076b26 5.10.16.3-microsoft-standard-WSL2 #1 SMP Fri Apr 2 22:23:49 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
root@94330c076b26:/# tail -f /var/log/nginx/access.log
127.0.0.1 - - [10/Aug/2021:18:16:18 -0400] "GET / HTTP/1.1" 200 612 "-" "curl/7.68.0"

```

Conseguimos pausar e continuar a execução do container com `docker stop id` e `docker start id`

```
atbta@ANDRE:~$ docker stop 94330c076b26
94330c076b26
atbta@ANDRE:~$ docker start 94330c076b26
94330c076b26
```

Normalmente não é possível excluir um container em execução com o `docker rm`, inclusive se está em background. Mas podemos forçar com o atributo `-f`

```
atbta@ANDRE:~$ docker ps
CONTAINER ID   IMAGE              COMMAND                  CREATED          STATUS          PORTS                                   NAMES
94330c076b26   ubuntu/app_nginx   "/usr/sbin/nginx -g …"   40 minutes ago   Up 21 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp   app_nginx

atbta@ANDRE:~$ docker rm app_nginx
Error response from daemon: You cannot remove a running container 94330c076b26dfabf3d2af7990e138a09a18ca76a6231420583ed95a12e4e573. Stop the container before attempting removal or force remove

atbta@ANDRE:~$ docker rm -f app_nginx
app_nginx

atbta@ANDRE:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
atbta@ANDRE:~$

```



Próxima aula [002-docker-imgs.md](https://github.com/atbtavares/tutoriais/blob/main/002-docker-imgs.md)



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

