---
title: Gerenciamento de contêiner do Serviço de Contêiner do Azure por meio da API REST | Microsoft Docs
description: Implante contêineres em cluster Mesos do Serviço de Contêiner do Azure usando a API REST do Marathon.
services: container-service
documentationcenter: ''
author: neilpeterson
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Docker, Contêineres, Microsserviços, Mesos, Azure

ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: nepeters

---
# Gerenciamento de contêiner por meio da API REST
O DC/OS fornece um ambiente de implantação e dimensionamento de cargas de trabalho clusterizadas e, ao mesmo tempo, abstrai o hardware subjacente. Sobre o DC/OS, há uma estrutura que gerencia o agendamento e a execução das cargas de trabalho de computação.

Embora haja estruturas disponíveis para várias cargas de trabalho populares, este documento descreve como você pode criar e dimensionar implantações de contêiner usando o Marathon. Antes de trabalhar nos exemplos, você precisará de um cluster DC/OS configurado no Serviço de Contêiner do Azure. Você também precisa ter conectividade remota com esse cluster. Para saber mais sobre esses itens, confira os artigos a seguir:

* [Como implantar um cluster do Serviço de Contêiner do Azure](container-service-deployment.md)
* [Conexão a um cluster do Serviço de Contêiner do Azure](container-service-connect.md)

Depois que você estiver conectado ao cluster do Serviço de Contêiner do Azure, poderá acessar o DC/OS e as APIs REST relacionadas por meio de http://localhost:local-port. Os exemplos neste documento pressupõem que você crie um túnel na porta 80. Por exemplo, o ponto de extremidade Marathon pode ser acessado na `http://localhost/marathon/v2/`. Para saber mais sobre as várias APIs, confira a documentação da Mesosphere para a [API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) e a [API Chronos](https://mesos.github.io/chronos/docs/api.html) e a documentação do Apache para a [API do Agendador do Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/)

## Coletar informações do DC/OS e do Marathon
Antes de implantar contêineres no cluster DC/OS, colete algumas informações sobre esse cluster, como os nomes e o status atual dos agentes DC/OS. Para fazer isso, confira o ponto de extremidade `master/slaves` da API REST DC/OS. Se tudo correr bem, você verá uma lista de agentes DC/OS e várias propriedades para cada um deles.

```bash
curl http://localhost/mesos/master/slaves
```

Agora, use o ponto de extremidade `/apps` do Marathon para verificar as implantações atuais do Marathon para o cluster DC/OS. Se esse for um cluster novo, você verá uma matriz vazia para os aplicativos.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Implantar um contêiner formatado pelo Docker
Você implanta os contêineres formatados pelo Docker por meio do Marathon usando um arquivo JSON que descreve a implementação planejada. O exemplo a seguir implantará o contêiner Nginx, associando a porta 80 do agente DC/OS à porta 80 do contêiner. Observe também que a propriedade 'acceptedResourceRoles' está definida como 'slave\_public'. Isso implantará o contêiner em um agente no conjunto de escalas de agentes voltados para o público.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
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

Para implantar um contêiner formatado pelo Docker, crie seu próprio arquivo JSON ou use o exemplo fornecido na [demonstração do Serviço de Contêiner do Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Armazene-o em um local acessível. Em seguida, para implantar o contêiner, execute o comando a seguir. Especifique o nome do arquivo JSON.

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

A saída será semelhante ao seguinte:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Agora, se você consultar o Marathon em busca de aplicativos, esse novo aplicativo será exibido na saída.

```
curl localhost/marathon/v2/apps
```

## Dimensionar seus contêineres
Você também pode usar a API do Marathon para expandir ou reduzir horizontalmente as implantações de aplicativos. No exemplo anterior, você implantou uma instância de um aplicativo. Vamos expandir essa saída para três instâncias de um aplicativo. Para fazer isso, crie um arquivo JSON usando o texto JSON a seguir e armazene-o em um local acessível.

```json
{ "instances": 3 }
```

Execute o comando a seguir para escalar horizontalmente o aplicativo.

> [!NOTE]
> O URI será http://localhost/marathon/v2/apps/ e então a ID do aplicativo para o dimensionamento. Se você usar o exemplo do Nginx fornecido aqui, o URI será http://localhost/marathon/v2/apps/nginx.
> 
> 

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por fim, consulte o ponto de extremidade do Marathon para aplicativos. Você verá que agora há três contêineres do Nginx.

```
curl localhost/marathon/v2/apps
```

## Usar o PowerShell neste exercício: interação da API REST com o PowerShell
Você pode executar essas mesmas ações usando comandos do PowerShell em um sistema Windows.

Para obter informações sobre o cluster DC/OS, como nomes e status de agentes, execute o comando a seguir.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Você implanta os contêineres formatados pelo Docker por meio do Marathon usando um arquivo JSON que descreve a implementação planejada. O exemplo a seguir implantará o contêiner Nginx, associando a porta 80 do agente DC/OS à porta 80 do contêiner.

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

Crie seu próprio arquivo JSON ou use o exemplo fornecido na [demonstração do Serviço de Contêiner do Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Armazene-o em um local acessível. Em seguida, para implantar o contêiner, execute o comando a seguir. Especifique o nome do arquivo JSON.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Você também pode usar a API do Marathon para expandir ou reduzir horizontalmente as implantações de aplicativos. No exemplo anterior, você implantou uma instância de um aplicativo. Vamos expandir essa saída para três instâncias de um aplicativo. Para fazer isso, crie um arquivo JSON usando o texto JSON a seguir e armazene-o em um local acessível.

```json
{ "instances": 3 }
```

Execute o comando a seguir para escalar horizontalmente o aplicativo.

> [!NOTE]
> O URI será http://localhost/marathon/v2/apps/ e então a ID do aplicativo para o dimensionamento. Se você estiver usando o exemplo do Nginx fornecido aqui, o URI será http://localhost/marathon/v2/apps/nginx.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## Próximas etapas
* [Leia mais sobre os pontos de extremidade HTTP Mesos](http://mesos.apache.org/documentation/latest/endpoints/).
* [Leia mais sobre a API REST do Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html).

<!---HONumber=AcomDC_0914_2016-->