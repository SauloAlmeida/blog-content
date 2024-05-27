---
author:  Saulo Almeida
pubDatetime: 2024-05-27T03:00:00Z
modDatetime: 2024-05-2T03:00:00Z
title: Explorando Docker
slug: explorando-docker
featured: true
draft: false
tags:
  - docker
  - development
  - devops
description: Uma plataforma para desenvolvimento, entrega e execução de aplicações.
---

## O que é?

É uma solução altamente flexível para o desenvolvimento, entrega e execução de aplicações. Com Docker, você pode criar, implantar e gerenciar suas aplicações de forma eficiente e escalável.

#### Docker Client (CLI)
É a camada de interação dos usuários, onde são executados dos comandos. Através dele que é possível de comunicar com o dockerd, vulgo Docker Daemon.
#### Docker Daemon
Escuta o Docker Client, através de requisições de API e gerencia os objetos do Docker, como imagens, containers, networks e volumes.
#### Registry
É o repositório de imagens docker.

## 📦Containers
É uma instância de uma imagem Docker. Pode criar, iniciar, parar, mover, ou deletar o container usando o Docker CLI.
## 🛠️Comandos

- `docker build`: Usada para criar uma imagem docker a partir de um Dockerfile
    - Exemplo: `docker build -t <docker_image_name>:<tag> . `
	* Exemplo real: `docker build -f "./api/Dockerfile" --force -rm -t api:latest  --build-arg "BUILD_CONFIGURATION=Release" .`
	
- `docker run`: Usada para executar uma imagem docker em um container;
    - Exemplo: `docker run -d(detached/daemon) -p(configure ports) <machine_port>:<container_port> <image_name>`
    - Exemplo para conectar com o entrypoint (o processo principal com bash): `docker container run -it ubuntu`
    
- `docker exec`: Usada para executar um comando dentro de um container existente e informando o comando;
    - Exemplo: `docker exec -it <container_name> <command_name>`
    - Exemplo real: `docker exec -it nginx bash` (Pois o entrypoint do nginx não é um bash)

- `docker attach`: Usado para se conectar ao container em execução;
	- Exemplo: `docker container attach <container_id> || <container_name>`

* `docker tag`: Usado para associar a imagem gerada com o endereço do repositório;
	* Exemplo real: `docker image tag <container_id> || <container_name:tag> ghcr.io/sauloalmeida/saulo.api:latest`

- `docker ps`: Usada para listar containers em execução;
    - Exemplo: `docker ps`
    
- `docker stop`: Usada para parar um container;
    - Exemplo: `docker stop <container_name>`
    
- `docker start`: Usada para iniciar um container;
    - Exemplo: `docker start <container_name>

- `docker restart`: Usado para reiniciar um container; 
	-  Exemplo: `docker restart <container_name>

- `docker inspect`: Usado para saber os detalhes do container; 
	-  Exemplo: `docker inspect <container_name>

- `docker pull`: Usada para baixar uma imagem do Docker Hub;
    - Exemplo: `docker pull <image_name>
    
- `docker push`: Usada para enviar uma imagem para o Docker Hub;
    - Exemplo: `docker push <container_registry/image_name:tag>` 
    
- `docker rm`: Usada para remover um container;
    - Exemplo: `docker rm <container_name>`
    - Exemplo real (quando está em execução): `docker container rm -f <container_id or container_name>` `

- `docker rmi`: Usada para remover uma imagem;
    - Exemplo: `docker rmi <image_name>`
    
- `docker container logs`: Usado para monitorar a saída do container.
	- Exemplo: `docker container logs <container_id or container_name>`

* `docker container stats`: Verificar a utilização de recursos do container.
	* Exemplo: `docker container stats <container_id or container_name>`

* `docker container update`: Atualiza os recursos do container.
	* Exemplo: `docker container update --cpus 0.4 <container_id or container_name>`
	
* `docker container ps -a -q`: Irá mostrar todos os ids dos containers, devido ao parâmetro `-q`. 

* `docker container rm`: Remove os containers.
	* Exemplo real: `docker container rm -f $(docker container ps -a -q)`
##### Parâmetros
``-d``: Irá executar em modo daemon, o terminal não irá ficar preso, não irá rodar em primeiro plano.
``-it``: Interatividade ficará conectado no bash, caso seja o entrypoint do Dockerfile da imagem.
``-v``: Irá conectar um volume ao container, exemplo: -v my_volume:/path/in/container.
`-m`: Irá definir a quantidade memória para o container: `docker run -d -m 128M nginx`
`-cups`: Irá definir a quantidade de CORES: `docker run -d --cpus 0.5 nginx`
## 🌐Network
É a forma como containers se comunicam entre si, possibilitando troca de dados.
#### Tipos
- ``Bridge`` (Padrão do Docker): Criando um IP único para cada container dentro do mesmo host. Essa rede permite comunicação entre containers, mas NÃO com o host.

- ``Host``: Atribui aos containers o mesmo IP do Host, facilitando a comunicação entre o container e o host, mas torna o container visível para toda a rede.

- ``Overlay``: Perfeita para ambientes multi-host, conecta containers em diferentes hosts, criando uma rede virtual unificada, boa para microsserviços.

- ``None``: O container ficará isolado, sem nenhuma comunicação com outros containers ou com o mundo externo.

## 📂Volumes
Os volumes são utilizados para compartilhar arquivos e diretórios entre containers.
#### Bind
Quando é feito a vinculação de um diretório para um volume.
* `docker container run -it --mount type=bind,src=/minha/pasta,dst=/container/pasta <nome_da_imagem>`
	* No Windows + WSL, como está sendo executado de forma virtualizada, será preciso informar o diretório raiz até a pasta, ex:  `C:\Users\...\volume\minha\pasta`
### Casos de Uso Práticos dos Volumes:
- **Armazenamento de Logs:** Guarde logs de forma persistente em um volume para análise posterior e depuração simplificada.
- **Dados de Aplicação:** Mantenha os dados da sua aplicação, incluindo bancos de dados e arquivos de configuração, em volumes para garantir persistência e acesso contínuo.
- **Cache:** Aproveite volumes para armazenar dados em cache, melhorando o desempenho da sua aplicação e reduzindo o tempo de resposta.

##### Comandos
* `docker volume create <nome_do_volume>`: Irá criar um volume.
* `docker volume inspect <nome_do_volume>`: Irá retornar as especificações do volume.
	* WSL Windows: A pasta dos volumes fica nesse diretório: `\\wsl.localhost\docker-desktop-data\data\docker\volumes`
* `-v`: Vincular ao container, ex: `docker run -it --name app-backup -p 8989:5000 -e FOLDER=app-backup -v /etc/backup:/container/backup app-backup.api:latest`
* `docker volume prune`: Irá remover todos os volumes que não estiver sendo utilizado.
##### Parâmetros
``,ro``: Apenas permissão de leitura, ex: `docker container run -it --mount type=bind,src=/minha/pasta,dst=/container/pasta,ro <nome_da_imagem>`
###### Desafios
* Criar uma rotina de backup flexível, apenas com Dockerfile + AWS CLI.
## 📋Dockerfile

Manual de instruções para criação de uma imagem do container.
- **Define o ambiente do container:** SO, pacotes, ferramentas, etc.
- **Instala dependências:** Bibliotecas, frameworks, etc.
- **Configura a aplicação:** Variáveis de ambiente, arquivos de configuração, etc.
- **Define o comando de execução:** O que o container executará ao iniciar.

**Benefícios:**
- **Padronização:** Garante que a imagem seja sempre criada da mesma forma.
- **Reprodutibilidade:** Permite criar imagens em diferentes ambientes com o mesmo resultado.
- **Automatização:** Facilita a criação e o gerenciamento de imagens em larga escala.

```dockerfile
FROM ubuntu:latest
RUN apt-get update && apt-get install -y nginx
COPY index.html /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
<small>Cria uma imagem com o Nginx instalado e configurado para servir um arquivo HTML na porta 80.</small>

### Muiltistage

```dockerfile
FROM golang AS build

WORKDIR /app
ADD . /app
RUN go build -o main

FROM alpine
WORKDIR /app-alpine
COPY --from=build /app/main /app-alpine

ENTRYPOINT ./main
```

É usado para que a imagem tenha um tamanho reduzido, no próximo `FROM` é que será utilizado para executar o código do `build`.
#### Parâmetros
``-t``: Nome da imagem + a versão, a tag. `docker build -t app-saulo:1.0.0 .`

`COPY`: Irá copiar arquivos do diretório do Dockerfile e irá colocar para dentro da imagem.

`ADD`:  Mesma função do `COPY`, porém mais completo. Ele pode: adicionar o conteúdo de um tar e adicionar através de link.

`CMD`: Comando que será executado quando o container subir, através do `docker run`. Porém, o mesmo pode ser sobrescrito com `docker run <image> ls`, e, com isso, irá executar o ls.
* Define um comando padrão que pode ser facilmente sobrescrito.

`ENTRYPOINT`: Comando que será executado quando o container subir, mas que pode ter argumentos adicionados no `docker run`.
* Define um comando que será sempre executado, com a possibilidade de adicionar argumentos no momento da execução.
* Exemplo: `ENTRYPOINT ["echo"]` -> `docker run <image> Hello, World!` -> ele executará `echo Hello, World!`.

## Desenvolvimento
Para fins de estudo, use o comando abaixo para executar um docker dentro de um docker (docker-in-docker).
```bash
docker run --privileged --name my-docker -d docker
docker exec -it my-docker /bin/sh
```