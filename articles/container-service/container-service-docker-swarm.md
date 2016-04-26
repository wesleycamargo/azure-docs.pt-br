<properties
   pageTitle="Gerenciamento de contêineres do Serviço de Contêiner do Azure com Docker Swarm | Microsoft Azure"
   description="Implantar contêineres em um Docker Swarm no Serviço de Contêiner do Azure"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contêineres, Microsserviços, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/13/2016"
   ms.author="nepeters"/>

# Gerenciamento de contêiner com Docker Swarm

O Docker Swarm fornece um ambiente para implantação de cargas de trabalho em contêineres de em um conjunto de pool de hosts do Docker. O Docker Swarm usa a API nativa do Docker, e o fluxo de trabalho para gerenciar contêineres em um Docker Swarm é quase idêntico ao que seria usado em um host de contêiner único. Este documento fornecerá exemplos simples de implantação de cargas de trabalho em contêineres, em uma instância do Serviço de Contêiner do Azure do Docker Swarm. Para obter documentação mais detalhada sobre o Docker Swarm, confira [Docker Swarm em Docker.com](https://docs.docker.com/swarm/).

Pré-requisitos para os exercícios deste documento:

[Criar um Cluster Swarm no Serviço de Contêiner do Azure](./container-service-deployment.md)

[Conectar-se ao Cluster Swarm no Serviço de Contêiner do Azure](./container-service-connect.md)

## Implantar um novo contêiner

Para criar um novo contêiner no Docker Swarm, use o comando `docker run`. Este exemplo cria um contêiner por meio da imagem `yeasy/simple-web`.


```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Depois que o contêiner tiver sido criado, use `docker ps` para retornar informações sobre o contêiner. Observe aqui que é listado o agente de Swarm que hospeda o contêiner.


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

O aplicativo em execução nesse contêiner agora pode ser acessado por meio do nome DNS público do balanceador de carga do agente de Swarm. Essas informações podem ser encontradas no Portal do Azure.


![](media/real-visit.jpg)

## Implantar vários contêineres

Como vários contêineres são iniciados no cluster do Docker Swarm, o comando `docker ps` pode ser usado para ver em quais hosts os contêineres estão em execução. Neste exemplo, três contêineres são espalhados uniformemente entre os três agentes de Swarm.


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## Implantar contêineres usando Docker Compose

O recurso Docker Compose pode ser usada para automatizar a implantação e a configuração de vários contêineres. Para isso, verifique se um túnel SSH foi criado e se a variável DOCKER\_HOST foi definida.

Crie um arquivo docker-compose.yml em seu sistema local. Um exemplo pode ser encontrado [aqui](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml).

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Execute `docker-compose up -d` para iniciar as implantações de contêineres.


```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Por fim, a lista de contêineres de execução pode ser retornada, refletindo os que foram implantados com Docker Compose.


```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

## Próximas etapas:

[Leia mais sobre o Docker Swarm](https://docs.docker.com/swarm/).

<!---HONumber=AcomDC_0420_2016-->