# Comandos básicos portainer e docker

## Ajustando 

```
touch docker-compose.yml
```

```
services:
  portainer:
    image: portainer/portainer-ce
    ports:
      - '8080:8000'
      - '9000:9000'
    volumes:
      - /var/run/docker.sock
      - ./portainer:/data
```

```
docker-compose up -d
```

```
docker-compose ps
```

Testamos no navegador e criamos um usuário admin



