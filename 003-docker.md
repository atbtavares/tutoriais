### Tutorial hands-on docker (3/10)  - Logs

## O que vimos na aula passada

1 - Verificamos as estatísticas de um container como id, nome, cpu, memória, io e pids

2 - Criamos imagens com `docker build` e Dockerfile

3 - Aprendemos a expor uma porta do container com Dockerfile

4 - Criamos uma imagem com a pagina inicial do nginx customizada

## INÍCIO - Trabalhando com Logs

Vamos mapear o arquivo de log do nginx para o `docker logs` para não ter que entrar no container toda vez que eu precisar acessar o log.

 A saída padrão e de erro do ubuntu devem apontar para os arquivos de log do nginx. No Dockerfile vamos incluir a linha `RUN ls -sf /dev/stdout /var/log/nginx/acess.log` e também a linha `RUN ls -sf /dev/stderr /var/log/nginx/error.log`.

```
FROM ubuntu

MAINTAINER andre <atb.tavares@gmail.com>

RUN apt update

RUN apt install -y nginx

ADD index.html /var/www/html

RUN ln -sf /dev/stdout  /var/log/nginx/access.log

RUN ln -sf /dev/stderr  /var/log/nginx/error.log

EXPOSE 80

ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]

```



Fazemos então o build e verificamos que há duas novas linhas na saída do `docker build`

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker build -t app .
[+] Building 9.1s (12/12) FINISHED
 => [internal] load build definition from Dockerfile                                                    0.3s
 => => transferring dockerfile: 340B                                                                    0.1s
 => [internal] load .dockerignore                                                                       0.3s
 => => transferring context: 2B                                                                         0.1s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                        4.5s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                           0.0s
 => [internal] load build context                                                                       0.0s
 => => transferring context: 32B                                                                        0.0s
 => [1/6] FROM docker.io/library/ubuntu@sha256:82becede498899ec668628e7cb0ad87b6e1c371cb8a1e597d83a47fa 0.0s
 => CACHED [2/6] RUN apt update                                                                         0.0s
 => CACHED [3/6] RUN apt install -y nginx                                                               0.0s
 => CACHED [4/6] ADD index.html /var/www/html                                                           0.0s
 => [5/6] RUN ln -sf /dev/stdout  /var/log/nginx/access.log                                             2.6s
 => [6/6] RUN ln -sf /dev/stderr  /var/log/nginx/error.log                                              1.0s
 => exporting to image                                                                                  0.2s
 => => exporting layers                                                                                 0.2s
 => => writing image sha256:1317ff3c370f8235d025db1f176eb98cccbf0b98ac7b8afe1fd08e31f3963554            0.0s
 => => naming to docker.io/library/app                                                                  0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
```

Essas duas linhas são devido alteração que fizemos no Dockerfile

```
=> [5/6] RUN ln -sf /dev/stdout  /var/log/nginx/access.log                                              2.6s
=> [6/6] RUN ln -sf /dev/stderr  /var/log/nginx/error.log                                               1.0s
```

Vamos executar o container e testar se o nginx subiu

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker run -p 8080:80 -d  --name app_nginx app
665ca26a5c10b8ca737e2aafdf286a7b7cb07816f6deb016fcef46e24abf31af

atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                   NAMES
665ca26a5c10   app       "/usr/sbin/nginx -g …"   37 seconds ago   Up 35 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   app_nginx

atbta@ANDRE:~/ubuntu-app-nginx$ curl http://localhost:8080
<!DOCTYPE html>
<html lang="pt_BR">
<head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Aplicação Exemplo</title>
</head>
<body>
        <h1>NGINX - APP</h1>
</body>
</html>
```

Tudo OK.

Você pode acompanhar o log com `docker logs -f` ou `docker logs --follow`

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker logs app_nginx -f
172.17.0.1 - - [13/Aug/2021:15:41:33 +0000] "GET / HTTP/1.1" 200 246 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:90.0) Gecko/20100101 Firefox/90.0"
172.17.0.1 - - [13/Aug/2021:16:01:21 +0000] "GET / HTTP/1.1" 200 283 "-" "curl/7.68.0"
172.17.0.1 - - [13/Aug/2021:17:25:36 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:90.0) Gecko/20100101 Firefox/90.0"

```

### Dockerfile com multi-stage builds

Em um multi-stage build a construção da imagem acontece em etapas.

Como temos um exemplo de aplicação do Vue:  [ubuntu-app.zip](C:\Users\atbta\Downloads\ubuntu-app.zip) 

Após subir a aplicação vamos estudar o Dockerfile dela que tem multi-stage builds

```
atbta@ANDRE:~/ubuntu-app$ docker build -t app_vue .
[+] Building 110.2s (20/20) FINISHED
 => [internal] load build definition from Dockerfile                                                   0.1s
 => => transferring dockerfile: 418B                                                                   0.0s
 => [internal] load .dockerignore                                                                      0.1s
 => => transferring context: 66B                                                                       0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                        4.9s
 => [internal] load metadata for docker.io/library/node:lts-alpine3.13                                 4.9s
 => [auth] library/nginx:pull token for registry-1.docker.io                                           0.0s
 => [auth] library/node:pull token for registry-1.docker.io                                            0.0s
 => [production 1/3] FROM docker.io/library/nginx:latest@sha256:8f335768880da6baf72b70c701002b45f4932a 22.3s
 => => resolve docker.io/library/nginx:latest@sha256:8f335768880da6baf72b70c701002b45f4932acae8d574ded 0.1s
 => => sha256:8f335768880da6baf72b70c701002b45f4932acae8d574dedfddaf967fc3ac90 1.86kB / 1.86kB         0.0s
 => => extracting sha256:c90b090c213b9e42d7982715b827803437fcbf4337f4672fead618c60cd36b84              0.0s
 => => extracting sha256:1f41b2f2bf94740d411c54b48be7f5e9dfbe14f29d1a5cf64f39150d75f39740              0.0s
 => [build 1/9] FROM docker.io/library/node:lts-alpine3.13@sha256:5746903d7170e131afeeb8e6422633424852 19.7s
 => => resolve docker.io/library/node:lts-alpine3.13@sha256:5746903d7170e131afeeb8e642263342485222c4e1 0.1s
 => => sha256:3f9ad66a016cb456125cb82c82b070e4ba4018b635bdb1926b8240aac3c9bdfd 1.16kB / 1.16kB         0.0s
 => => sha256:a0564746d270a1f5d74346d4769e4125217e273c3b39752a51376353b0486f61 6.53kB / 6.53kB         0.0s
 => => extracting sha256:7121a0c5ed3697d8aca8b0a5f13e54f1b625879204319b26a806df9323348aeb              0.2s
 => => extracting sha256:01167fb2fbb67ec936b91ed3a483cecdc82bdd9ba7404a99f3cf989fc9d534bc              0.0s
 => [internal] load build context                                                                      0.1s
 => => transferring context: 407.08kB                                                                  0.1s
 => [build 2/9] RUN apk update                                                                         6.4s
 => [build 3/9] RUN apk add yarn                                                                       4.5s
 => [build 4/9] WORKDIR /app                                                                           0.1s
 => [build 5/9] COPY package.json .                                                                    0.1s
 => [build 6/9] COPY yarn.lock .                                                                       0.1s
 => [build 7/9] COPY . .                                                                               0.1s
 => [build 8/9] RUN yarn install                                                                       58.4s
 => [build 9/9] RUN yarn build                                                                         13.5s
 => [production 2/3] COPY --from=build /app/config/nginx/default.conf /etc/nginx/conf.d                0.1s
 => [production 3/3] COPY --from=build /app/dist /usr/share/nginx/html                                 0.1s
 => exporting to image                                                                                 0.2s
 => => exporting layers                                                                                0.1s
 => => writing image sha256:99bdd56b66a29ebb4893c70a742effbc763d95a21df94f2fb5f1384777db01b8           0.0s
 => => naming to docker.io/library/app_vue                                                             0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
atbta@ANDRE:~/ubuntu-app$


```

Você pode subir o container com `docker run -p 8080:80 --name vue app_vue`

```
atbta@ANDRE:~/ubuntu-app$ docker run -p 8080:80 --name vue app_vue
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2021/08/18 16:39:29 [notice] 1#1: using the "epoll" event method
2021/08/18 16:39:29 [notice] 1#1: nginx/1.21.1
2021/08/18 16:39:29 [notice] 1#1: built by gcc 8.3.0 (Debian 8.3.0-6)
2021/08/18 16:39:29 [notice] 1#1: OS: Linux 5.10.16.3-microsoft-standard-WSL2
2021/08/18 16:39:29 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2021/08/18 16:39:29 [notice] 1#1: start worker processes
2021/08/18 16:39:29 [notice] 1#1: start worker process 30
2021/08/18 16:39:29 [notice] 1#1: start worker process 31
2021/08/18 16:39:29 [notice] 1#1: start worker process 32
2021/08/18 16:39:29 [notice] 1#1: start worker process 33
2021/08/18 16:39:29 [notice] 1#1: start worker process 34
2021/08/18 16:39:29 [notice] 1#1: start worker process 35
2021/08/18 16:39:29 [notice] 1#1: start worker process 36
2021/08/18 16:39:29 [notice] 1#1: start worker process 37

```

Teste a aplicação vue no navegador  do SO hospedeiro (http://localhost:8080)[http://localhost:8080]

![image-20210818124137654](C:\Users\atbta\AppData\Roaming\Typora\typora-user-images\image-20210818124137654.png)

O Dockerfile contém:

```
01. FROM node:lts-alpine3.13 as build
02. 
03. RUN apk update
04. RUN apk add yarn
05. 
06. WORKDIR /app
07. 
08. COPY package.json .
09. COPY yarn.lock .
10. COPY . .
11. RUN yarn install
12. RUN yarn build
13. 
14. FROM nginx:latest as production
15. COPY --from=build /app/config/nginx/default.conf /etc/nginx/conf.d
16. COPY --from=build /app/dist /usr/share/nginx/html
17. EXPOSE 80
18. CMD ["nginx", "-g", "daemon off;"]

```

Vamos explicar passo a passo cada ação realizada quando fazemos o `docker build`

No Dockerfile, a primeira linha `FROM node:lts-alpine3.13 as build` usou o Alpine Linux como SO da imagem quando fizemos o `docker build`. A industria usa essa distro de linux para seus pacotes porque é muito leve e enxuta.

Nas linhas 03 e 04, após ter baixado a imagem do alpine, foram realizados os comandos `apk update`e `apk add yarn`, para instalar o gerenciador de pacotes **yarn** no Alpine LInux, um requisito para para o vue.

Na linha 06, com o comando `WORKDIR /app`,  foi criado um diretório **app** lá na raiz do sistema operacional: `/`

Na parte dos comandos `COPY`, nas linhas 08 a 10, são copiados os arquivos `package.json`, `yarn.lock` e também os arquivos do diretório atual para `/app`.

O `yarn install` e o `yarn build`, nas linhas 11 e 12, fizeram o build da  aplicação.

Na linha 14 foi definido um apelido `production` para os próximos passos, com nginx: ` FROM nginx:latest as production`.

Nas linha 15 a 18 é onde foi feita a cópia dos arquivos de configuração do estágio anterior, apelidado como `build`, para o nginx. Também os arquivos de configuração e arquivos padrão para o nginx, bem como exposição da porta e configuração para a execução do nginx

### Importação e exportaçao de containers

Com o comando `docker save` é possível copiar um container em um arquivo.

Para exportar basta fazer `commit` do container escolhido e depois salvar a imagem em arquivo:

```
docker ps -q 
b02af9430141

docker commit b02af9430141 nova_imagem
934907fd0asd9304098sdf934907fd0asd9304098sdf934907fd0asd9304098sdf

docker save nova_imagem > nova_imagem.tar
```

Para importar em outra máquina:

```
docker load < nova_imagem.tar
```

Vamos fazer esse processo para a imagem `vue`

```
atbta@ANDRE:~/ubuntu-app$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                   CREATED             STATUS                      PORTS     NAMES
86d4d7bc24da   app_vue        "/docker-entrypoint.…"    About an hour ago   Exited (0) 8 minutes ago              vue
```

Vamos transformar esse container em imagem com o `docker commit`:

```
atbta@ANDRE:~/ubuntu-app$ docker commit 86d4d7bc24da vue-img
sha256:76c61ebd1ae77c23332c2f8bfef6ef77ae825b5af6eaffc9e2aedf6c2528ca31
```

Verificando se o container foi salvo:

```
atbta@ANDRE:~/ubuntu-app$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED             SIZE
vue-img      latest    76c61ebd1ae7   4 seconds ago       134MB
app_vue      latest    99bdd56b66a2   About an hour ago   134MB
app          latest    1317ff3c370f   6 hours ago         162MB
```

Tudo OK.

Para copiar em arquivo basta salvar a imagem com  `docker save` 

```
atbta@ANDRE:~/ubuntu-app$ docker save vue-img > vue-img.tar
atbta@ANDRE:~/ubuntu-app$ ls -lh
total 133M
-rw-r--r-- 1 atbta atbta 132M Aug 13 18:04 vue-img.tar

```

Para efeito de testes, vamos remover a imagem da qual criamos o arquivo vue-img.tar. Em seguida vamos carregar ela novamente para o docker através do arquivo. O melhor caso seria ter uma outra máquina com docker para levar o arquivo pra lá/

```
atbta@ANDRE:~/ubuntu-app$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
vue-img      latest    76c61ebd1ae7   17 minutes ago   134MB
app_vue      latest    99bdd56b66a2   2 hours ago      134MB
app          latest    1317ff3c370f   7 hours ago      162MB

```



```
atbta@ANDRE:~/ubuntu-app$ docker rmi vue-img
Untagged: vue-img:latest
Deleted: sha256:76c61ebd1ae77c23332c2f8bfef6ef77ae825b5af6eaffc9e2aedf6c2528ca31
Deleted: sha256:d12ac3e7245684e7841c80ac11bbbdf327924ba451b285b552cdb074c8438b31
```



```
atbta@ANDRE:~/ubuntu-app$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
app_vue      latest    99bdd56b66a2   2 hours ago   134MB
app          latest    1317ff3c370f   7 hours ago   162MB

atbta@ANDRE:~/ubuntu-app$ docker load < vue-img.tar
4f6b3b27f72b: Loading layer [==================================================>]  11.26kB/11.26kB
Loaded image: vue-img:latest
```



```
atbta@ANDRE:~/ubuntu-app$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
vue-img      latest    76c61ebd1ae7   40 minutes ago   134MB
app_vue      latest    99bdd56b66a2   2 hours ago      134MB
app          latest    1317ff3c370f   7 hours ago      162MB
```



### Usando o docker hub

Para obter uma imagem pronta, online, posso pesquisar no Docker Hub https://hub.docker.com/  ou na linha de comandos com `docker search <nome>`

Exemplo

```
atbta@ANDRE:~/ubuntu-app$ docker search php
NAME                       DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
php                        While designed for web development, the PHP …   6088      [OK]
phpmyadmin/phpmyadmin      A web interface for MySQL and MariaDB.          1136                 [OK]
phpmyadmin                 phpMyAdmin - A web interface for MySQL and M…   295       [OK]
webdevops/php-nginx        Nginx with PHP-FPM                              209                  [OK]
php-zendserver             Zend Server - the integrated PHP application…   198       [OK]
webdevops/php-apache-dev   PHP with Apache for Development (eg. with xd…   144                  [OK]
webdevops/php-apache       Apache with PHP-FPM (based on webdevops/php)    119                  [OK]
osixia/phpldapadmin        phpLDAPadmin with easy configuration via env…   116
phpunit/phpunit            PHPUnit is a programmer-oriented testing fra…   82                   [OK]
nazarpc/phpmyadmin         phpMyAdmin as Docker container, based on off…   61                   [OK]
phpipam/phpipam-www        phpIPAM is an open-source web IP address man…   52
tetraweb/php               PHP 5.5, 5.6, 7.0, 7.1 for CI and running te…   36                   [OK]
pierrecdn/phpipam          phpIPAM web IP address management applicatio…   36                   [OK]
thecodingmachine/php       General-purpose ultra-configurable PHP images   35                   [OK]
chialab/php                Adding common PHP extensions to some of the …   28                   [OK]
devilbox/php-fpm           PHP-FPM Docker images based on original PHP …   25
phpstan/phpstan            Docker container to check your application w…   19                   [OK]
phpdockerio/php7-fpm       PHP 7 FPM base container for PHPDocker.io.      18                   [OK]
phpdockerio/php73-fpm      PHP 7.3 FPM base container for PHPDocker.io.    18
appsvc/php                 Azure App Service php dockerfiles               16                   [OK]
phpipam/phpipam-cron       phpIPAM is an open-source web IP address man…   14
arm32v7/php                While designed for web development, the PHP …   13
graze/php-alpine           Smallish php7 alpine image with some common …   12                   [OK]
phpdockerio/php7-cli       PHP 7 CLI base container image for PHPDocker…   2                    [OK]
ccitest/php                CircleCI test images for PHP                    0                    [OK]
```

Se tem um `[OK]` na coluna de oficial, foi o fornecedor que preparou a imagem, mas se tem na coluna AUTOMATED então significa que foi criado pela comunidade e que facilita criar processos de automação.

No dockerhub.com você verá mais detalhes das imagens oficiais e da comunidade de forma mais simples e rápida.

Verifique o `docker search --help`

Após achar a imagem podemos baixar ela com `docker pull <nome img>`

```
atbta@ANDRE:~/ubuntu-app$ docker search jboss
NAME                                        DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
jboss/wildfly                               WildFly application server image                578                  [OK]
jboss/keycloak                              Keycloak server                                 515                  [OK]
jboss/drools-workbench-showcase             Drools Workbench Showcase                       80                   [OK]
jboss/base-jdk                                                                              80                   [OK]
jboss/drools-workbench                      Drools Workbench                                61                   [OK]
tutum/jboss                                 JBoss image - listens in port 8080, 9990. Fo…   55                   [OK]
jboss/kie-server-showcase                   KIE Execution Server Showcase                   51                   [OK]
jboss/jbpm-workbench-showcase               jBPM Workbench Showcase                         46                   [OK]
jboss/kie-server                            KIE Execution Server                            40                   [OK]
jboss/base                                                                                  31                   [OK]
jboss/jbpm-workbench                        jBPM Workbench                                  30                   [OK]
jboss/jbpm-server-full                      jBPM Workbench Showcase Docker image            20                   [OK]
jboss/business-central-workbench-showcase                                                   13
jboss/keycloak-ha-postgres                                                                  13                   [OK]
jboss/infinispan-server                     Infinispan is an open source data grid platf…   10                   [OK]
daggerok/jboss-eap-7.2                      Docker hub JBoss EAP 7.2 automation build (i…   8
daggerok/jboss-eap-7.1                      Docker hub JBoss EAP 7.1 automation build (i…   6                    [OK]
daggerok/jboss                              JBOSS automation build                          6                    [OK]
jboss/keycloak-proxy                        This image is deprecated and will be replace…   5                    [OK]
jboss/keycloak-adapter-wildfly              Deprecated - this image is no longer maintai…   4                    [OK]
daggerok/jboss-eap-6.4                      Docker hub JBoss EAP 6.4 automation build (i…   4                    [OK]
scalified/jboss-eap                         JBOSS EAP Docker Images                         3
paulosalgado/jboss5                         JBoss AS 5.1.0.GA + JDK 7                       3                    [OK]
jboss/base-maven                            Maven Base Docker Image                         1                    [OK]
inspectit/jboss                             JBoss with inspectIT                            1                    [OK]

atbta@ANDRE:~/ubuntu-app$ docker pull jboss/wildfly
Using default tag: latest
latest: Pulling from jboss/wildfly
75f829a71a1c: Pull complete
7b11f246b3d3: Pull complete
b765648c2a58: Pull complete
506aff4a9c5a: Pull complete
7b9c6d08c8d3: Pull complete
Digest: sha256:1f39dbbe874367e008c6d70fa687f1afec00a0304a53a8cf7d832b77221f6922
Status: Downloaded newer image for jboss/wildfly:latest
docker.io/jboss/wildfly:latest

```

Verificamos com `docker images`

```
atbta@ANDRE:~/ubuntu-app$ docker images
REPOSITORY      TAG       IMAGE ID       CREATED             SIZE
vue-img         latest    76c61ebd1ae7   About an hour ago   134MB
app_vue         latest    99bdd56b66a2   2 hours ago         134MB
app             latest    1317ff3c370f   8 hours ago         162MB
jboss/wildfly   latest    a6ba50806be9   7 weeks ago         724MB
```







## Comandos utilizados neste tutorial

```

```

