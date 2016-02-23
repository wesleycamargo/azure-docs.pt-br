<properties
   pageTitle="Gerenciamento de contêiner do ACS com a API REST| Microsoft Azure"
   description="Implante contêineres para um serviço de cluster do Serviço de Contêiner do Azure usando a API REST do Marathon."
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
   ms.date="02/16/2016"
   ms.author="nepeters"/>
   
# Gerenciamento de contêiner com a API REST

O Mesos fornece um ambiente de implantação e dimensionamento de carga de trabalho clusterizada e, ao mesmo tempo, abstrai o hardware subjacente. Sobre o Mesos, estruturas gerenciam o agendamento e a execução da carga de trabalho de computação. Embora haja estruturas disponíveis para várias cargas de trabalho populares, este documento fornecerá detalhes sobre a criação e o dimensionamento de implantações de contêiner com o Marathon. Antes de trabalhar com os exemplos, você precisará de um cluster Mesos configurado no ACS e de conectividade remota para esse cluster. Para obter mais informações sobre esses itens, confira os artigos a seguir.

- [Como implantar um cluster do Serviço de Contêiner do Azure](./container-service-deployment.md) 
- [Como se conectar a um Cluster ACS](./container-service-connect.md)


Após configurar o túnel SSH, você poderá acessar as APIs REST relacionadas ao Mesos usando `http://localhost:LOCAL_PORT`. Nos exemplos a seguir, presumimos que você está usando um túnel na porta 80; por exemplo, `http://localhost/marathon/v2` será o ponto de extremidade para a API do Marathon. Para obter mais informações sobre as várias APIs disponíveis, confira a documentação da Mesosphere para a [API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) e a [API Chronos](https://mesos.github.io/chronos/docs/api.html) e a documentação da Apache para a [API do Agendador Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/)

## Coletar informações do Mesos e do Marathon

Antes de implantar contêineres no cluster Mesos, colete algumas informações sobre esse cluster, como o nome e o status atual dos agentes Mesos. Para fazer isso, consulte o ponto de extremidade `master/slaves` em um mestre do Mesos. Se tudo correr bem, você verá uma lista de agentes Mesos e várias propriedades para cada um deles.

```bash
curl http://localhost/master/slaves
```

Agora, use o ponto de extremidade `/apps` do Marathon para verificação e as implantações atuais do Marathon para o cluster Mesos. Se esse for um cluster novo, você verá uma matriz vazia para os aplicativos.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Como implantar um contêiner do Docker

Os contêineres do Docker são implantados por meio do Marathon usando um arquivo json que descreve a implementação planejada. O exemplo a seguir implantará o contêiner nginx, associando a porta 80 do agente Mesos à porta 80 do contêiner.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Para implantar um contêiner do Docker, crie seu próprio arquivo json ou use o exemplo fornecido aqui - [Demonstração do ACS do Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json), e armazene-o em um local acessível. Depois, execute o comando a seguir, especificando o nome do arquivo json, para implantar o contêiner.

```
curl -X POST http://localhost/marathon/v2/groups -d @marathon.json -H "Content-type: application/json"
```

A saída será semelhante ao seguinte:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Agora, se você consultar o Marathon para a execução do aplicativo, esse novo aplicativo será exibido na saída.

```
curl localhost/marathon/v2/apps
```

## Dimensionar um contêiner do Docker

A API do Marathon também pode ser usada para expandir ou reduzir as implantações de aplicativos. No exemplo anterior, uma instância de um aplicativo foi implantada. Vamos expandir isso para três instâncias. Para fazer isso, crie um arquivo json com o texto json a seguir e armazene-o em um local acessível.

```json
{ "instances": 3 }
```

Execute o comando a seguir para expandir o aplicativo.

> Observação: o URI será http://localhost/marathon/v2/apps/ e, em seguida, a ID do aplicativo para dimensionamento. Se você usar o exemplo de nginx fornecido aqui, o URI será http://localhost/v2/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por fim, consulte o ponto de extremidade do Marathon para a instância do aplicativo. Você observará que agora há três.

```
curl localhost/marathon/v2/apps
```

## PowerShell da API REST do Marathon

Essa mesma ação pode ser executada usando o PowerShell em um sistema Windows. Este exercício rápido concluirá tarefas semelhantes às do exercício anterior, desta vez usando comandos do PowerShell.

Para obter informações sobre o cluster Mesos, como nomes e status de agentes, execute o comando a seguir.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Os contêineres do Docker são implantados por meio do Marathon usando um arquivo json que descreve a implementação planejada. O exemplo a seguir implantará o contêiner nginx, associando a porta 80 do agente Mesos à porta 80 do contêiner.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Crie seu próprio arquivo json ou use o exemplo fornecido aqui - [Demonstração do ACS do Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json), e armazene-o em um local acessível. Depois, execute o comando a seguir, especificando o nome do arquivo json, para implantar o contêiner.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

A API do Marathon também pode ser usada para expandir ou reduzir as implantações de aplicativos. No exemplo anterior, uma instância de um aplicativo foi implantada. Vamos expandir isso para três instâncias. Para fazer isso, crie um arquivo json com o texto json a seguir e armazene-o em um local acessível.

```json
{ "instances": 3 }
```

Execute o comando a seguir para expandir o aplicativo.

> Observação: o URI será http://loclahost/marathon/v2/apps/ e, em seguida, a ID do aplicativo para dimensionamento. Se você usar o exemplo de nginx fornecido aqui, o URI será http://localhost/v2/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

<!---HONumber=AcomDC_0218_2016-->