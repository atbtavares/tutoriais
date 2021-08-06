```bash
comandos utilizados

docker info   # informações da instalação do docker
docker vesion # versão da engine

```

Vamos implementar um container que imprima na tela "Hello!"

```bash
docker run ubuntu /bin/echo Hello!
```

Como não tem imagem do ubuntu na máquina local ela é baixada antes da execução do  comando

Nesse comando, após baixar o ubuntu é acessado o binário /bin/echo e executado para o argumento "Hello!"

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e8e8ead1-e82e-476e-8069-3960fafa94a6/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e8e8ead1-e82e-476e-8069-3960fafa94a6/Untitled.png)

Podemos usar o comando docker images para verificar que o passo de baixar a imagem do ubuntu no comando anterior foi realmente executado:

```bash
docker images
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/aa000bdc-02e0-439a-8220-52f73c12bd04/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/aa000bdc-02e0-439a-8220-52f73c12bd04/Untitled.png)

É baixado do ubuntu somente o que é necessário para executar o comando solicitado pela aplicação.

Quando executamos novamente a imagem já está presente e não é mais baixada:

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2164ff60-74c1-426d-abc9-1ce98c34e6fa/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2164ff60-74c1-426d-abc9-1ce98c34e6fa/Untitled.png)

O container com ubuntu foi criado, o comando bash echo foi executado e seu resultado foi para a sua saída padrão. Para verificar que o comando terminou de executar podemos usar o comando 

```bash
docker ps -a
```

O parametro -a mostra os containers que terminaram a sua execução

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fe0021c6-694d-4109-8f56-f61db8a19e27/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fe0021c6-694d-4109-8f56-f61db8a19e27/Untitled.png)
