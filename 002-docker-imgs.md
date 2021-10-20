# Tutorial hands-on docker (2/10)  - Imagens

## Ultima aula

1 - Criamos um **container** para imprimir Hello! no terminal, com `docker run`

2 - Fizemos um **container interativo** na linha de comandos, com `docker run -it`

3 - Criamos um **container em background** com nginx funcionando e servindo páginas, mapeando da porta 80 dele para a porta 8080 do SO do hospedeiro. Foi usado `docker run -d`

4 - Criamos uma **imagem** apenas como teste, com `docker commit`, para exemplificar o uso de imagens.

5 - Também aprendemos como **parar** e **continuar** a execução de um container, através do `docker stop`e do `docker start`

6 - Em um container executando background conseguimos executar um binário nele através do `docker exec`

## INÍCIO - Trabalhando com imagens

Na aula anterior, criamos uma imagem com ubuntu + nginx instalado, chamada de **ubuntu/app_nginx**. Podemos verificar isso com `docker images`

```
atbta@ANDRE:~$ docker images
REPOSITORY         TAG       IMAGE ID       CREATED        SIZE
ubuntu/app_nginx   latest    1b2fae00a95c   20 hours ago   173MB
ubuntu             latest    1318b700e415   2 weeks ago    72.8MB
```

Agora vamos iniciar essa imagem num container em background com o comando **`docker run -p 8080:80 -d --name app_nginx ubuntu/app_nginx  /usr/bin/nginx -g "daemon off;"`**. O parâmetro `--name` dá nome ao container. O `-p` mapeia a porta 80 do container para 8080 do hospedeiro.

```
atbta@ANDRE:~$ docker run -p 8080:80 -d --name app_nginx ubuntu/app_nginx  /usr/sbin/nginx -g "daemon off;"
0e2178aa16e4e47186889965ee956564a7b36ec2587dacfc2854def6823d4514
```

O `docker run` acima retornou o container id completo

Se já houver um container com o nome **app_nginx**, você pode apagar ele com `docker rm -f` se ele estiver em background senão `docker -rm`

```
atbta@ANDRE:~$ docker ps
CONTAINER ID IMAGE            COMMAND                CREATED STATUS  PORTS                                NAMES
0e2178aa16e4 ubuntu/app_nginx "/usr/sbin/nginx -g …" 23 min  Up 23  0.0.0.0:8080->80/tcp, :::8080->80/tcp app_nginx
```

Podemos verificar as estatísticas da execução do container **app_nginx** com `docker stats`

```
docker stats app_nginx
```

```
CONTAINER ID   NAME        CPU %     MEM USAGE / LIMIT     MEM %     NET I/O     BLOCK I/O   PIDS
0e2178aa16e4   app_nginx   0.00%     7.582MiB / 4.524GiB   0.16%     906B / 0B   0B / 0B     9

```

O ideal é limitar o uso de memória do container para que ele não possa consumir toda memória do SO hospedeiro

A forma que criamos a imagem **ubuntu/app_nginx** torna necessário usar muitos parâmetros ao criar um container a partir dela. Nesse caso o binário do nginx e parametrizações dele. Ao longo do tutorial vamos fazer outras com menos parâmetros.

Agora, primeiro vamos remover a imagem com `docker rmi`. Obtemos o container id no `docker ps`e o image id no `docker images`. Antes excluímos o container e depois a imagem, ambos pelo ID.

```
atbta@ANDRE:~$ docker rm -f 0e2178aa16e4 
0e2178aa16e4

atbta@ANDRE:~$ docker rmi 1b2fae00a95c
Deleted: sha256:1b2fae00a95c460a50628c7d068aa34a4d0920543e08547dff93f6b834d6b17a
Deleted: sha256:a3456e0e66f7e988b05ded14f1ac937ad76ee28aff3b41c2fb3d14b718702dea

```

Para remover todas as imagens precisamos dos IDs. 

Podemos listar o id delas com `docker images -q`. Usamos esse comando dentro de `$()` no comando `docker rmi` para apagar todas

```
atbta@ANDRE:~$0 docker rmi $(docker images -q) -f
Untagged: ubuntu:latest
Untagged: ubuntu@sha256:82becede498899ec668628e7cb0ad87b6e1c371cb8a1e597d83a47fac21d6af3
Deleted: sha256:1318b700e415001198d1bf66d260b07f67ca8a552b61b0da02b3832c778f221b
```

## Hoje vamos trabalhar com imagens e...

1) Mapeamento de portas
2) Copia de arquivos para a imagem
3) Inicialização de serviços na imagem

## Construindo imagens  com `Dockerfile`

Imagens são criadas a partir de arquivos chamados Dockerfile. Dentro dele constam comandos linux que são executados na imagem onde cada comando é uma camada da imagem. Segue um exemplo de Dockerfile abaixo:

```
FROM ubuntu
MAINTAINER Andre Tavares <andre.tavares@gmail.com>
RUN apt-get update
RUN apt-get install -y nginx
```

Ao criar uma imagem com o Dockerfile, ele executa os comandos descritos no arquivo, no fim faz o commit (lembra da aula 1?) e dá o nome para a nova imagem.

Com docker, a sua organização tem algumas opções:

1) As imagens de aplicações podem ser criadas a partir das imagens bases da empresa.
2) As imagens bases podem ter uma camada das aplicações de monitoramento de aplicações.
3) Pode definir imagens base com versões de tecnologia que consegue trabalhar ao disponibilizar imagens padrão. Como eu consigo alterar uma camada da imagem e incluir um upgrade de java, seria possível evoluir toda a estrutura simultaneamente.
4) Pode definir os sistemas operacionais básicos e também pode deixar a cargo do desenvolvedor a tecnologia que irá utilizar ao permitir que ele crie as imagens utilizadas em desenvolvimento/produção.

É importante que a nova imagem tenha um SO base mais enxuto possível para que tenha menos sobrecarga de IO (o que aumenta o startup do container). Por esse motivo muitos fornecedores de imagem disponibilizam as suas a partir do Alpine Linux que é bem enxuto e limpo já que é usado para aplicações embarcadas.

Outro exemplo de Dockerfile

```
FROM adoptopen-jdk/openjdk8-openj9:alpine-slim
MAINTAINER depto.empresa
ADD target/modulo-api.jar  /opt/modulo-api/
RUN echo "America/Manaus" > /etc/timezone
ENTRYPOINT ["java"]
CMD ["-jar", "/opt/modulo-api/modulo-api.jar"]
EXPOSE 8080
```

ADD incluir o arquivo no local específico da imagem.

ENTRYPOINT é o binário do java

CMD são os parâmetros do ENTRYPOINT

Quando a imagem se tornar container:

1) será executado o programa indicado no ENTRYPOINT
2) será exposta na porta 8080

### Criando imagem de ubuntu com nginx com Dockerfile

```
atbta@ANDRE:~$ mkdir ubuntu-app-nginx
atbta@ANDRE:~$ cd ubuntu-app-nginx/
atbta@ANDRE:~/ubuntu-app-nginx$ touch Dockerfile
atbta@ANDRE:~/ubuntu-app-nginx$ vim Dockerfile
```

O arquivo Dockerfile não tem extensão e a primeira letra é maiúscula

```
FROM ubuntu

MAINTAINER andre <atb.tavares@gmail.com>

RUN apt update

RUN apt install -y nginx

```

Vamos executar na linha de comandos `docker build -t nginx_app .`

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker build -t nginx_app .
[+] Building 161.8s (8/8) FINISHED
 => [internal] load build definition from Dockerfile                                               0.1ss
 => => transferring dockerfile: 141B                                                               0.0ss
 => [internal] load .dockerignore                                                                  0.1ss
 => => transferring context: 2B                                                                    0.0ss
 => [internal] load metadata for docker.io/library/ubuntu:latest                                   6.3ss
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                      0.0ss
 => [1/3] FROM docker.io/library/ubuntu@sha256:82becede498899ec668628e7cb0ad87b6e1c371             0.2ss
 => => resolve docker.io/library/ubuntu@sha256:82becede498899ec668628e7cb0ad87b6e1c371             0.0ss
 => => sha256:82becede498899ec668628e7cb0ad87b6e1c371cb8a1e597d83a47fac21d6af3 1.42kB              0.0ss
 => => sha256:1e48201ccc2ab83afc435394b3bf70af0fa0055215c1e26a5da9b50a1ae367c9 529B /              0.0ss
 => => sha256:1318b700e415001198d1bf66d260b07f67ca8a552b61b0da02b3832c778f221b 1.46kB              0.0ss
 => [2/3] RUN apt update                                                                          69.3ss
 => [3/3] RUN apt install -y nginx                                                                84.8ss
 => exporting to image                                                                             0.9s
 => => exporting layers                                                                            0.8s
 => => writing image sha256:8a3c9e7bce079574246afdae4a80103a79ebbd4d342be3af8f7cca6c45             0.0s
 => => naming to docker.io/library/nginx_app                                                       0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
atbta@ANDRE:~/ubuntu-app-nginx$

```

Esse trecho aqui são as camadas criadas

```
=> [1/3] FROM docker.io/library/ubuntu@sha256:82becede498899ec668628e7cb0ad87b6e1c371             0.2ss
=> [2/3] RUN apt update                                                                          69.3ss
=> [3/3] RUN apt install -y nginx                                                                84.8ss
```

Vamos verificar se a imagem foi criada com `docker images`

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
nginx_app    latest    8a3c9e7bce07   2 minutes ago   162MB
```

Vamos executar a imagem (criar o container)

```
atbta@ANDRE:~$ docker run -d -p 8080:80 nginx_app /usr/sbin/nginx -g "daemon off;"
b710528b482593cf92c555aadc9a8157c1e161be35454ea2558f7dce55e47490

atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                                   NAMES
b710528b4825   nginx_app   "/usr/sbin/nginx -g …"   7 minutes ago   Up 7 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp   tender_spence
```

Mas ainda precisamos informar o binário nginx e seus parâmetros.

Vamos testar o nginx

```
atbta@ANDRE:~/ubuntu-app-nginx$ curl http://localhost:8080
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
```

Até aqui tudo OK

### Portas - Usando EXPOSE no Dockerfile

Vamos incluir a linha abaixo no Dockerfile e fazer novo build

```
EXPOSE 80
```

Ao fazer o build, podemos usar o mesmo nome `nginx_app` sem problemas

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker build -t nginx_app .
[+] Building 7.2s (8/8) FINISHED
 => [internal] load build definition from Dockerfile                                                            0.1s
 => => transferring dockerfile: 150B                                                                            0.0s
 => [internal] load .dockerignore                                                                               0.1s
 => => transferring context: 2B                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                7.0s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                   0.0s
 => [1/3] FROM docker.io/library/ubuntu@sha256:82becede498899ec668628e7cb0ad87b6e1c371cb8a1e597d83a47fac21d6af3 0.0s
 => CACHED [2/3] RUN apt update                                                                                 0.0s
 => CACHED [3/3] RUN apt install -y nginx                                                                       0.0s
 => exporting to image                                                                                          0.0s
 => => exporting layers                                                                                         0.0s
 => => writing image sha256:d75673bddaa38281e2a386e0dd6dd03e4c9012242c9de3dd9ca5b6a09872fbdc                    0.0s
 => => naming to docker.io/library/nginx_app                                                                    0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
```

Todas as camadas já estavam em cache, apenas a exposição da porta foi adicionada

Lembra que já estava rodando um container com a imagem anterior?

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID   IMAGE          COMMAND                CREATED        STATUS        PORTS                                 NAMES
b710528b4825   8a3c9e7bce07   "/usr/sbin/nginx -g …" 13 minutes ago Up 13 minutes 0.0.0.0:8080->80/tcp, :::8080->80/tcp tender_spence

```

Mas agora o id da imagem em execução é 8a3c9e7bce07 e ela não está com o nome nginx_app.

Quando listamos com `docker images`temos a nova imagem com o nome nginx_app:

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED             SIZE
nginx_app    latest    d75673bddaa3   About an hour ago   162MB
<none>       <none>    8a3c9e7bce07   About an hour ago   162MB
```

Ao fazer um deploy automático usando docker é necessário fazer a limpeza das imagens devido esse processo com o `docker buid` que pode acabar enchendo o disco

1. Vamos remover o container que está em execução da imagem anterior (que está sem o expose), usando `docker rm`e o `CONTAINER ID`

```
atbta@ANDRE:~/ubuntu-app-nginx$  docker rm -f b710528b4825
b710528b4825
```

Criamos novo container com a nova imagem

```
docker run -d -p 8080:80 nginx_app /usr/sbin/nginx -g "daemon off;"
```

Conseguimos remover o parâmetro `-p 8080:80` para usarmos o `-P` que irá mapear a porta exposta pela imagem (conforme consta no Dockerfile que usamos) para uma porta aleatória

```
docker run -d -P nginx_app /usr/sbin/nginx -g "daemon off;"
```

Agora executamos a imagem criando container

```
atbta@ANDRE:~/ubuntu-app-nginx$  docker run -d -P nginx_app /usr/sbin/nginx -g "daemon off;"
681decb46ccae8674a0fe261493950d4473d0c731f3dc2c7884883ccdc7c8e4e
```

Vamos descobrir a porta para o qual o docker run mapeou. Depois testamos

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID  IMAGE     COMMAND                CREATED            STATUS            PORTS                                   NAMES
681decb46cca  nginx_app "/usr/sbin/nginx -g …" About a minute ago Up About a minute 0.0.0.0:49153->80/tcp, :::49153->80/tcp   nostalgic_brahmagupta
```

```
atbta@ANDRE:~/ubuntu-app-nginx$ curl http://localhost:49153
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

```

A página foi retornada com sucesso. O nginx está funcionando na porta 49153 do host

Também podemos usar o `docker port` para descobrir a porta

```
atbta@ANDRE:~/ubuntu-app-nginx$  docker port 681decb46cca
80/tcp -> 0.0.0.0:49153
80/tcp -> :::49153

```

É possível com recurso de balanceamento disponibilizar vários container para uma única porta 80, por exemplo. Vamos ver isso em outra aula

### Alterando o arquivo padrão do nginx de index.html

Crie o arquivo index.html no mesmo diretório do Dockerfile, com o conteúdo abaixo

```
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

Editamos o Dockerfile para incluir a linha `ADD index.html /usr/share/nginx/html`

```
FROM ubuntu

MAINTAINER andre <atb.tavares@gmail.com>

RUN apt update

RUN apt install -y nginx

ADD index.html /usr/share/nginx/html

EXPOSE 80
```

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID  IMAGE      COMMAND                 CREATED         STATUS         PORTS                                    NAMES
681decb46cca  nginx_app  "/usr/sbin/nginx -g …"  26 minutes ago  Up 26 minutes  0.0.0.0:49153->80/tcp, :::49153->80/tcp   nostalgic_brahmagupta

```

Fazemos o build

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker build -t nginx_app .
[+] Building 5.8s (10/10) FINISHED
 => [internal] load build definition from Dockerfile                                                             0.1s
 => => transferring dockerfile: 38B                                                                              0.0s
 => [internal] load .dockerignore                                                                                0.1s
 => => transferring context: 2B                                                                                  0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                 5.0s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                    0.0s
 => [1/4] FROM docker.io/library/ubuntu@sha256:82becede498899ec668628e7cb0ad87b6e1c371cb8a1e597d83a47fac21d6af3  0.0s
 => [internal] load build context                                                                                0.1s
 => => transferring context: 322B                                                                                0.0s
 => CACHED [2/4] RUN apt update                                                                                  0.0s
 => CACHED [3/4] RUN apt install -y nginx                                                                        0.0s
 => [4/4] ADD index.html /usr/share/nginx/html                                                                   0.1s
 => exporting to image                                                                                           0.2s
 => => exporting layers                                                                                          0.1s
 => => writing image sha256:a91f6af669d638d6251e99656eab4756ef5bfdc200faeb1214821aca1d010f5f                     0.0s
 => => naming to docker.io/library/nginx_app                                                                     0.0s
```

Fizemos uma nova imagem **nginx_app**, mas antes não terminamos o container. O container que estava em execução antes do build não terá sua imagem atualizada.

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED        STATUS        PORTS                                     NAMES
681decb46cca   d75673bddaa3   "/usr/sbin/nginx -g …"   18 hours ago   Up 18 hours   0.0.0.0:49153->80/tcp, :::49153->80/tcp   nostalgic_brahmagupta

atbta@ANDRE:~/ubuntu-app-nginx$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
nginx_app    latest    a91f6af669d6   20 minutes ago   162MB
<none>       <none>    8a3c9e7bce07   19 hours ago     162MB
<none>       <none>    d75673bddaa3   19 hours ago     162MB

```

Agora vamos terminar esse container e iniciar um novo

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker rm -f nostalgic_brahmagupta
nostalgic_brahmagupta
```

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker run -d -P nginx_app /usr/sbin/nginx -g "daemon off;"
5412d2f07ca540b9844f5353a3f043d13832ce9ee5c50d5e6ac45d0a79b0de5c
```

Tudo OK

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                                     NAMES
5412d2f07ca5   nginx_app   "/usr/sbin/nginx -g …"   32 seconds ago   Up 28 seconds   0.0.0.0:49154->80/tcp, :::49154->80/tcp   jolly_germain
```

Vamos testar com o curl na nova porta `49154`

```
atbta@ANDRE:~/ubuntu-app-nginx$ curl http://localhost:49154
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
```

*Você pode verificar que o html acessado não é o mesmo que subimos.* É possível que o caminho que usamos no Dockerfile não seja o caminho correto

Vamos verificar com `docker exec` para acessar o bash do container e verificar as configurações do nginx

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker exec -it  jolly_germain /bin/bash
root@5412d2f07ca5:/# cd /etc/nginx/sites-enabled
root@5412d2f07ca5:/etc/nginx/sites-enabled# ls
default
root@5412d2f07ca5:/etc/nginx/sites-enabled# cat default
##
# You should look at the following URL's in order to grasp a solid understanding
# of Nginx configuration files in order to fully unleash the power of Nginx.
# https://www.nginx.com/resources/wiki/start/
# https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/
# https://wiki.debian.org/Nginx/DirectoryStructure
#
# In most cases, administrators will remove this file from sites-enabled/ and
# leave it as reference inside of sites-available where it will continue to be
# updated by the nginx packaging team.
#
# This file will automatically load configuration files provided by other
# applications, such as Drupal or Wordpress. These applications will be made
# available underneath a path with that package name, such as /drupal8.
#
# Please see /usr/share/doc/nginx-doc/examples/ for more detailed examples.
##

# Default server configuration
#
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        # SSL configuration
        #
        # listen 443 ssl default_server;
        # listen [::]:443 ssl default_server;
        #
        # Note: You should disable gzip for SSL traffic.
        # See: https://bugs.debian.org/773332
        #
        # Read up on ssl_ciphers to ensure a secure configuration.
        # See: https://bugs.debian.org/765782
        #
        # Self signed certs generated by the ssl-cert package
        # Don't use them in a production server!
        #
        # include snippets/snakeoil.conf;

        root /var/www/html;
```

Precisamos alterar o Dockerfile e trocar o `/usr/share/nginx/html`  por `/var/www/html` 

Com a alteração já realizada, vamos criar novamente uma imagem com o nome **nginx_app**

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker build -t nginx_app .
[+] Building 4.3s (10/10) FINISHED
 => [internal] load build definition from Dockerfile                                                             0.1s
 => => transferring dockerfile: 182B                                                                             0.0s
 => [internal] load .dockerignore                                                                                0.0s
 => => transferring context: 2B                                                                                  0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                 3.7s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                    0.0s
 => [1/4] FROM docker.io/library/ubuntu@sha256:82becede498899ec668628e7cb0ad87b6e1c371cb8a1e597d83a47fac21d6af3  0.0s
 => [internal] load build context                                                                                0.0s
 => => transferring context: 32B                                                                                 0.0s
 => CACHED [2/4] RUN apt update                                                                                  0.0s
 => CACHED [3/4] RUN apt install -y nginx                                                                        0.0s
 => [4/4] ADD index.html /var/www/html                                                                           0.1s
 => exporting to image                                                                                           0.1s
 => => exporting layers                                                                                          0.1s
 => => writing image sha256:ff7fa19a02014dea8a2b96c7bc20932c8b4b3946cf6302b63efc263270783307                     0.0s
 => => naming to docker.io/library/nginx_app                                                                     0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
```

Faremos o mesmo processo de **excluir o container** anterior e **criar um novo** a partir da **nova imagem**

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED       STATUS       PORTS                                     NAMES
5412d2f07ca5   a91f6af669d6   "/usr/sbin/nginx -g …"   2 hours ago   Up 2 hours   0.0.0.0:49154->80/tcp, :::49154->80/tcp   jolly_germain

atbta@ANDRE:~/ubuntu-app-nginx$ docker rm -f 5412d2f07ca5
5412d2f07ca5

atbta@ANDRE:~/ubuntu-app-nginx$ docker run -d -P nginx_app /usr/sbin/nginx -g "daemon off;"
db8a0637e9b150bf90d4c4c13627449d1ede6474145689651c0109059232c85b

atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                                     NAMES
db8a0637e9b1   nginx_app   "/usr/sbin/nginx -g …"   8 seconds ago   Up 5 seconds   0.0.0.0:49155->80/tcp, :::49155->80/tcp   confident_williams

atbta@ANDRE:~/ubuntu-app-nginx$ curl http://localhost:49155
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

Dessa vez o html descrito no `Dockerfile` realmente foi retornado pelo `nginx`

### Próximo passo - chamada interna do nginx  no container

Para simplificar ainda mais o comando `docker run -d -P nginx_app /usr/sbin/nginx -g "daemon off;"`  podemos remover o trecho que configura  o local do binário e sua parametrização: `/usr/sbin/nginx -g "daemon off;"`

Vamos incluir no final do arquivo Dockerfile a seguinte linha

```
CMD service  nginx   start
```

Fazemos o `docker build` no terminal

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker build -t nginx_app .
[+] Building 4.4s (10/10) FINISHED
 => [internal] load build definition from Dockerfile                                                             0.1s
 => => transferring dockerfile: 208B                                                                             0.0s
 => [internal] load .dockerignore                                                                                0.1s
 => => transferring context: 2B                                                                                  0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                 4.0s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                    0.0s
 => [1/4] FROM docker.io/library/ubuntu@sha256:82becede498899ec668628e7cb0ad87b6e1c371cb8a1e597d83a47fac21d6af3  0.0s
 => [internal] load build context                                                                                0.0s
 => => transferring context: 32B                                                                                 0.0s
 => CACHED [2/4] RUN apt update                                                                                  0.0s
 => CACHED [3/4] RUN apt install -y nginx                                                                        0.0s
 => CACHED [4/4] ADD index.html /var/www/html                                                                    0.0s
 => exporting to image                                                                                           0.0s
 => => exporting layers                                                                                          0.0s
 => => writing image sha256:c8c7ae350a9798a42fbfc7e01f9d7ce4bed05816c277b13d3f28be7742fb09ca                     0.0s
 => => naming to docker.io/library/nginx_app                                                                     0.0s
```

Quando um container for criado a partir dessa imagem não precisamos mais informar os parameetros `/usr/sbin/nginx -g "daemon;"`

A cada versão nova, perceba que temos várias imagens. Então vamos excluir as imagens que não são mais necessárias

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
<none>       <none>    ff7fa19a0201   2 hours ago    162MB
nginx_app    latest    c8c7ae350a97   2 hours ago    162MB
<none>       <none>    a91f6af669d6   3 hours ago    162MB
<none>       <none>    8a3c9e7bce07   21 hours ago   162MB
<none>       <none>    d75673bddaa3   21 hours ago   162MB

```

Vamos subir um container da nova imagem

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker run -d -P nginx_app
acf18d4b67d1468b313b59aaa432efa7628731fbc0a771712fcd2bdd8f6c1ee2

atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

atbta@ANDRE:~/ubuntu-app-nginx$ docker ps -a
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS                      PORTS     NAMES
acf18d4b67d1   nginx_app   "/bin/sh -c 'service…"   18 seconds ago   Exited (0) 16 seconds ago             goofy_herschel
```

Percebemos que o container foi para o status de executado já que não incluímos o parametro `-g "daemon off;"`

Então incluímos no Dockerfile uma nova linha com `RUN echo "daemon off;" >> /etc/nginx/nginx.conf` e o arquivo fica assim

```
FROM ubuntu

MAINTAINER andre <atb.tavares@gmail.com>

RUN apt update

RUN apt install -y nginx

ADD index.html /var/www/html

RUN echo "daemon off;" >> /etc/nginx/nginx.conf

EXPOSE 80

CMD service  nginx start
```

Fazemos novo build com `docker build` 

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker build -t nginx_app .
[+] Building 9.1s (11/11) FINISHED
 => [internal] load build definition from Dockerfile                                                             0.3s
 => => transferring dockerfile: 257B                                                                             0.0s
 => [internal] load .dockerignore                                                                                0.2s
 => => transferring context: 2B                                                                                  0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                 5.9s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                    0.0s
 => [1/5] FROM docker.io/library/ubuntu@sha256:82becede498899ec668628e7cb0ad87b6e1c371cb8a1e597d83a47fac21d6af3  0.0s
 => [internal] load build context                                                                                0.0s
 => => transferring context: 32B                                                                                 0.0s
 => CACHED [2/5] RUN apt update                                                                                  0.0s
 => CACHED [3/5] RUN apt install -y nginx                                                                        0.0s
 => CACHED [4/5] ADD index.html /var/www/html                                                                    0.0s
 => [5/5] RUN echo "daemon off;" >> /etc/nginx/nginx.conf                                                        2.4s
 => exporting to image                                                                                           0.2s
 => => exporting layers                                                                                          0.1s
 => => writing image sha256:c6215e44b90bea6a16b9b3b6d88cdfc58aa32aba93dd4896052b142c7d3eced4                     0.0s
 => => naming to docker.io/library/nginx_app                                                                     0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
```

Executando o container temos o resultado dejado

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker run -d -P nginx_app
fc1abf72b5b3ba7ad3556bc9a8cd1776d67c51f1422ae4450bfca9217a5b975a

atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS         PORTS                                     NAMES
fc1abf72b5b3   nginx_app   "/bin/sh -c 'service…"   11 seconds ago   Up 9 seconds   0.0.0.0:49157->80/tcp, :::49157->80/tcp   awesome_roentgen

atbta@ANDRE:~/ubuntu-app-nginx$ curl http://localhost:49157
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

Em vez de usar o CMD no Dockerfile também podemos usar o ENTRYPOINTS. Podemos fazer a mesma função com o seguinte Dockerfile

```
FROM ubuntu

MAINTAINER andre <atb.tavares@gmail.com>

RUN apt update

RUN apt install -y nginx

ADD index.html /var/www/html

RUN echo "daemon off;" >> /etc/nginx/nginx.conf

EXPOSE 80

ENTRYPOINT ["/usr/sbin/nginx"]

CMD -g
```

Fazemos novo `docker build` e executamos, verificando que não há container em execução

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker build -t app .
[+] Building 4.3s (11/11) FINISHED
 => [internal] load build definition from Dockerfile                                                             0.1s
 => => transferring dockerfile: 38B                                                                              0.0s
 => [internal] load .dockerignore                                                                                0.1s
 => => transferring context: 2B                                                                                  0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                 3.9s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                    0.0s
 => [1/5] FROM docker.io/library/ubuntu@sha256:82becede498899ec668628e7cb0ad87b6e1c371cb8a1e597d83a47fac21d6af3  0.0s
 => [internal] load build context                                                                                0.0s
 => => transferring context: 32B                                                                                 0.0s
 => CACHED [2/5] RUN apt update                                                                                  0.0s
 => CACHED [3/5] RUN apt install -y nginx                                                                        0.0s
 => CACHED [4/5] ADD index.html /var/www/html                                                                    0.0s
 => CACHED [5/5] RUN echo "daemon off;" >> /etc/nginx/nginx.conf                                                 0.0s
 => exporting to image                                                                                           0.1s
 => => exporting layers                                                                                          0.0s
 => => writing image sha256:858ead37d86e49d1f6de872c5835cc4d72c9ef979dee74016dced41f92e069f6                     0.0s
 => => naming to docker.io/library/app                                                                           0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them

atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

atbta@ANDRE:~/ubuntu-app-nginx$ docker run -d -P app
3590b8a4338cce991d54a081eccfddece9247f87e1d1c4eba0dc1b19b58ab1ec

atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

```

Algo deu errado. Vamos ver a lista dos containers que terminaram sua execução

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS                      PORTS     NAMES
3590b8a4338c   app            "/usr/sbin/nginx /bi…"   33 seconds ago   Exited (1) 29 seconds ago             frosty_newton
acf18d4b67d1   c8c7ae350a97   "/bin/sh -c 'service…"   50 minutes ago   Exited (0) 50 minutes ago             goofy_herschel
```

Na coluna `COMMAND` parece que o comando executado não está correto. Vamos verificar com `docker logs`

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker logs 3590b8a4338c
nginx: invalid option: "/bin/sh"
```

Essa situação é originada pelo uso de entrypoints e cmds. Como se fosse um comando só, o entrypoint é concatenado com os cmds que houverem no `Dockerfile`

Isso aconteceu devido o nosso `CMD -g` no Dockerfile mapear suas entradas para `/bin/sh -c "-g"` no container, como já temos o `ENTRYPOINT` apontando para `/usr/sbin/nginx`.

Assim verificamos que o container fez o seguinte comando no ubuntu `/usr/sbin/nginx /bin/sh -c '-g'` e os parametros no nginx `/bin/sh -c '-g'` são inválidos

Vamos alterar o arquivo para a versão abaixo. E executar novamente.

```
FROM ubuntu

MAINTAINER andre <atb.tavares@gmail.com>

RUN apt update

RUN apt install -y nginx

ADD index.html /var/www/html

# usado somente quando é usado no S.O. o comando service nginx start
# RUN echo "daemon off;" >> /etc/nginx/nginx.conf

EXPOSE 80

ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]
```

Fazemos novo build

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker build -t app .
[+] Building 1.9s (9/9) FINISHED
 => [internal] load build definition from Dockerfile                                                            0.1s
 => => transferring dockerfile: 239B                                                                            0.0s
 => [internal] load .dockerignore                                                                               0.0s
 => => transferring context: 2B                                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                1.6s
 => [1/4] FROM docker.io/library/ubuntu@sha256:82becede498899ec668628e7cb0ad87b6e1c371cb8a1e597d83a47fac21d6af3 0.0s
 => [internal] load build context                                                                               0.0s
 => => transferring context: 32B                                                                                0.0s
 => CACHED [2/4] RUN apt update                                                                                 0.0s
 => CACHED [3/4] RUN apt install -y nginx                                                                       0.0s
 => CACHED [4/4] ADD index.html /var/www/html                                                                   0.0s
 => exporting to image                                                                                          0.0s
 => => exporting layers                                                                                         0.0s
 => => writing image sha256:4f34d8096ae91f83f68579714d991fed82556834ed1aea569b0d393287b509cc                    0.0s
 => => naming to docker.io/library/app                                                                          0.0s
Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
```

A executamos o container e testamos seu nginx

```
atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

atbta@ANDRE:~/ubuntu-app-nginx$ docker run -d -P app
ac065dd84a6b131cc752344767a2d190dc5511492a2ac0de0f4a8b125ddaf865

atbta@ANDRE:~/ubuntu-app-nginx$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS        PORTS                                     NAMES
ac065dd84a6b   app       "/usr/sbin/nginx -g …"   6 seconds ago   Up 1 second   0.0.0.0:49162->80/tcp, :::49162->80/tcp   optimistic_hellman

atbta@ANDRE:~/ubuntu-app-nginx$ curl http://localhost:49162
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

Tudo Ok. 

Próxima aula [003-docker.md](https://github.com/atbtavares/tutoriais/blob/main/003-docker.md)



## Comandos utilizados neste tutorial

```
docker images

docker ps

docker ps -a

docker stats

docker rm -f <container>

docker rmi <imagem>

docker rmi $(docker images -q) -f

docker build -t nginx_app .

docker run -p 8080:80 -d --name app_nginx ubuntu/app_nginx  /usr/sbin/nginx -g "daemon off;"

docker run -d -p 8080:80 nginx_app /usr/sbin/nginx -g "daemon off;"

docker run -d -P nginx_app /usr/sbin/nginx -g "daemon off;"

docker run -d -P nginx_app

curl http://localhost:49153

docker port 681decb46cc

docker exec -it  jolly_germain /bin/bash
```

