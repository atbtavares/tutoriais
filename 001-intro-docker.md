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
16ec32c2132b: Already exists
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

A imagem

É baixado do ubuntu somente o que é necessário para executar o comando solicitado pela aplicação.

Quando executamos novamente a imagem já está presente e não é mais baixada:

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2164ff60-74c1-426d-abc9-1ce98c34e6fa/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2164ff60-74c1-426d-abc9-1ce98c34e6fa/Untitled.png)

O container com ubuntu foi criado, o comando bash echo foi executado e seu resultado foi para a sua saída padrão. Para verificar que o comando terminou de executar podemos usar o comando 

```bash
docker ps -a
```

O parametro -a mostra os containers que terminaram a sua execução

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fe0021c6-694d-4109-8f56-f61db8a19e27/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fe0021c6-694d-4109-8f56-f61db8a19e27/Untitled.png)
