# Comandos básicos docker swarm

## Iniciando um nó *manager*

```
docker swarm init --advertise-addr <IP>
```

```
machine@system: docker swarm init --advertise-addr <IP>```
Swarm initialized: current node (a8sdf6as87df6s87f68s7d) is now a manager.

To add a worker to this swarm, run the following command:

docker swarm join --token SWMTKN-1-0hpfz298as7df98sa7f983ig6n50vierf8i-1efh6n00mwrzqavdxyso1plm7 <ip>:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

## Iniciando um nó *worker*

Copiar o comando da saída do comando ```docker swarm init``` feito no nó *manager*

```
sudo docker swarm join --token SWMTKN-1-0hpfz298as7df98sa7f983ig6n50vierf8i-1efh6n00mwrzqavdxyso1plm7 <ip>:2377
```

```
machine@system: docker swarm join --token SWMTKN-1-0hpfz298as7df98sa7f983ig6n50vierf8i-1efh6n00mwrzqavdxyso1plm7 <ip>:2377
This node joined a swarm as a worker.
```



## Recuperar a chave a partir do nó *manager*

```para recuperar a chave:
docker swarm join-token  worker
```

## Listar status dos nós worker

```
docker node ls
```

``` 
machine@system: docker node ls
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
1488lw8nkz7vzhe4tdclqwut6     system     Ready     Active                          20.10.9
5a4d254c7dwidxzk26ug7zvlf     system     Ready     Active                          20.10.9
kkmzud7lmfr5cij94sfo2az8j     system     Ready     Active                          20.10.9
tugqixwiysgx2wq7x44htkri6     system     Ready     Active                          20.10.9
xr7r8g2iv8ah4cfweilpvrhil     system     Ready     Active                          20.10.9
ypqngp7d60inz1ehvsh6cb4n6     system     Ready     Active                          20.10.9
zpyg2bi8956k83wfh6mg2jjgr *   system     Ready     Active         Leader           20.10.9
```



## Comandos utilizados neste tutorial

```

```

