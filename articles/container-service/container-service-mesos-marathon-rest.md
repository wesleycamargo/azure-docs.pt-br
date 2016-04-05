<properties
   pageTitle="Gerenciamento de contêiner do ACS com a API REST| Microsoft Azure"
   description="Implante contêineres em cluster Mesos do Serviço de Contêiner do Azure usando a API REST do Marathon."
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

O Mesos fornece um ambiente de implantação e dimensionamento de carga de trabalho clusterizada e, ao mesmo tempo, abstrai o hardware subjacente. Sobre o Mesos, estruturas gerenciam o agendamento e a execução da carga de trabalho de computação. Embora haja estruturas disponíveis para várias cargas de trabalho populares, este documento fornecerá detalhes sobre a criação e o dimensionamento de implantações de contêiner com o Marathon.

Antes de trabalhar com os exemplos, você precisará de um cluster Mesos configurado no ACS e de conectividade remota para esse cluster. Para saber mais sobre esses itens, confira os artigos a seguir.

- [Como implantar um cluster do Serviço de Contêiner do Azure](./container-service-deployment.md) 
- [Como se conectar a um Cluster ACS](./container-service-connect.md)


Uma vez conectado ao cluster ACS, o Mesos e as APIs REST relacionadas podem ser acessados por meio do http://localhost:local-port. Os exemplos neste documento pressupõem que você crie um túnel na porta 80. Por exemplo, o ponto de extremidade Marathon pode ser acessado na `http://localhost/marathon/v2/`. Para saber mais sobre as várias APIs, confira a documentação da Mesosphere para a [API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) e a [API Chronos](https://mesos.github.io/chronos/docs/api.html) e a documentação do Apache para a [API do Agendador do Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/)

## Coletar informações do Mesos e do Marathon

Antes de implantar contêineres no cluster Mesos, colete algumas informações sobre esse cluster, como os nomes e o status atual dos agentes do Mesos. Para fazer isso, confira o ponto de extremidade `master/slaves` da API REST Mesos. Se tudo correr bem, você verá uma lista de agentes Mesos e várias propriedades para cada um deles.

```bash
curl http://localhost/mesos/master/slaves
```

Agora, use o ponto de extremidade `/apps` do Marathon para verificar as implantações atuais do Marathon para o cluster Mesos. Se esse for um cluster novo, você verá uma matriz vazia para os aplicativos.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Implantação de um contêiner formatado do Docker

Os contêineres formatados do Docker são implantados por meio do Marathon usando um arquivo json que descreve a implementação planejada. O exemplo a seguir implantará o contêiner nginx, associando a porta 80 do agente Mesos à porta 80 do contêiner.

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

Para implantar um contêiner formatado do Docker, crie seu próprio arquivo json ou use o exemplo fornecido aqui - [Demonstração do ACS do Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json) e armazene-o em um local acessível. Depois, execute o comando a seguir, especificando o nome do arquivo json, para implantar o contêiner.

```
curl -X POST http://localhost/marathon/v2/groups -d @marathon.json -H "Content-type: application/json"
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

A API do Marathon também pode ser usada para expandir ou reduzir as implantações de aplicativos. No exemplo anterior, uma instância de um aplicativo foi implantada. Vamos expandir isso para três instâncias. Para fazer isso, crie um arquivo json com o texto json a seguir e armazene-o em um local acessível.

```json
{ "instances": 3 }
```

Execute o comando a seguir para expandir o aplicativo.

> Observação: o URI será http://localhost/marathon/v2/apps/ e a ID do aplicativo para o dimensionamento. Se você usar o exemplo de nginx fornecido aqui, o URI será http://localhost/v2/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por fim, consulte o ponto de extremidade Marathon para aplicativos, você observará que agora há três contêineres nginx.

```
curl localhost/marathon/v2/apps
```

## Interação da API REST do Marathon com o PowerShell

Essa mesma ação pode ser executada usando o PowerShell em um sistema Windows. Este exercício rápido concluirá tarefas semelhantes às do exercício anterior, desta vez usando comandos do PowerShell.

Para obter informações sobre o cluster Mesos, como nomes e status de agentes, execute o comando a seguir.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Os contêineres no formato do Docker são implantados por meio do Marathon usando um arquivo json que descreve a implementação planejada. O exemplo a seguir implantará o contêiner nginx, associando a porta 80 do agente Mesos à porta 80 do contêiner.

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

Crie seu próprio arquivo json ou use o exemplo fornecido aqui - [Demonstração do ACS do Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json) e armazene-o em um local acessível. Depois, execute o comando a seguir, especificando o nome do arquivo json, para implantar o contêiner.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

A API do Marathon também pode ser usada para expandir ou reduzir as implantações de aplicativos. No exemplo anterior, uma instância de um aplicativo foi implantada. Vamos expandir isso para três instâncias. Para fazer isso, crie um arquivo json com o texto json a seguir e armazene-o em um local acessível.

```json
{ "instances": 3 }
```

Execute o comando a seguir para expandir o aplicativo.

> Observação: o URI será http://loclahost/marathon/v2/apps/ e a ID do aplicativo para o dimensionamento. Se você usar o exemplo de nginx fornecido aqui, o URI será http://localhost/v2/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

<!---HONumber=AcomDC_0330_2016-->