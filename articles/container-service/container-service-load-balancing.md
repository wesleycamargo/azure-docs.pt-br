<properties
   pageTitle="Balanceamento de carga de um cluster do Serviço de Contêiner do Azure | Microsoft Azure"
   description="Balanceamento de carga de um cluster do Serviço de Contêiner do Azure."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contêineres, microsserviços, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="rogardle"/>

# Balanceamento de carga de um cluster do Serviço de Contêiner do Azure

Neste artigo, vamos definir um front-end da Web, que pode ser escalado verticalmente para oferecer serviços por trás do Azure Load Balancer.

## Pré-requisitos

[Implantar uma instância do Serviço de Contêiner do Azure](container-service-deployment.md) com o tipo de orquestrador DCOS e [garantir que o cliente possa se conectar ao cluster](container-service-connect.md). Execute as etapas a seguir.
[AZURE.INCLUDE [instalar a CLI (interface de linha de comando) do DC/OS](../../includes/container-service-install-dcos-cli-include.md)]


## Balanceamento de carga

Há duas camadas de balanceamento de carga em um cluster do Serviço de Contêiner: o Azure Load Balancer para os pontos de entrada públicos (aqueles que serão acessados pelos usuários finais) e o marathon-lb (Balanceador de Carga do Marathon) subjacente que roteia solicitações de entrada para atender às solicitações de instâncias de contêiner. À medida que dimensionarmos os contêineres que fornecem o serviço, o marathon-lb se adapta dinamicamente.

## Balanceador de Carga do Marathon

O Balanceador de Carga do Marathon reconfigura dinamicamente baseado nos contêineres que você implantou. Ela é também é resiliente à perda de um contêiner ou de um agente; se isso ocorrer, o Mesos simplesmente reiniciará o contêiner em outro lugar e reconfigurará o Balanceador de Carga do Marathon.

Para instalar o Balanceador de Carga do Marathon, execute o seguinte comando do computador cliente:

```bash
dcos package install marathon-lb
```

Agora que temos o pacote marathon-lb, podemos implantar um servidor Web simples usando a seguinte configuração:


```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "tutum/hello-world",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

As partes principais são:
  * Defina o valor de HAProxy\_0\_VHOST como o FQDN do balanceador de carga para seus agentes. Isso está no formato `<acsName>agents.<region>.cloudapp.azure.com`. Por exemplo, se você tivesse criado um cluster do Serviço de Contêiner com o nome `myacs`, na região `West US`, o FQDN seria: `myacsagents.westus.cloudapp.azure.com`. Você também pode encontrar isso procurando pelo balanceador de carga com "agente" no nome ao examinar os recursos no grupo de recursos criado para seu serviço de contêiner no [Portal do Azure](https://portal.azure.com).
  * Defina o servicePort para uma porta > = 10.000. Isso identifica o serviço que está sendo executado no contêiner; o marathon-lb o utiliza para identificar os serviços que deve balancear.
  * Defina o rótulo HAPROXY\_GROUP como "externo".
  * Defina hostPort como 0. Isso significa que o Marathon alocará arbitrariamente uma porta disponível.

Copie esse JSON em um arquivo chamado `hello-web.json` e use-o para implantar um contêiner:

```bash
dcos marathon app add hello-web.json
```

## Balanceador de carga do Azure

Por padrão, o Azure Load Balancer expõe as portas 80, 8080 e 443. Se você estiver usando uma dessas três portas (como ocorre no exemplo acima), não haverá mais nada a fazer. Você deve conseguir atingir o FQDN do balanceador de carga do agente, e cada vez que atualizar, encontrará um dos três servidores Web em round-robin. Se, no entanto, você usar uma porta diferente, será necessário adicionar uma regra de round robin e uma investigação ao balanceador de carga do Azure à porta usada. Você pode fazer isso na [CLI do Azure](../xplat-cli-azure-resource-manager.md) com os comandos `azure lb rule create` e `azure lb probe create`.


## Cenários adicionais

Você pode ter um cenário onde usa domínios diferentes para expor serviços diferentes. Por exemplo:

meudominio1.com -> Azure LB:80 -> marathon-lb:10001 -> meuconteiner1:33292 meudomínio2.com -> Azure LB:80 -> marathon-lb:10002 -> meuconteiner2:22321

Para fazer isso, confira os [hosts virtuais](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), que fornecem uma maneira de associar os domínios a caminhos específicos do marathon-lb.

Como alternativa, você pode expor portas diferentes e remapeá-las para o serviço correto por trás do marathon-lb. Por exemplo:

Azure lb:80 -> marathon-lb:10001 -> meuconteiner:233423 Azure lb:8080 -> marathon-lb:1002 -> meuconteiner2:33432


## Próximas etapas

Confira esta [postagem de blog sobre balanceamento de carga](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/) para saber mais sobre o Balanceador de Carga do Marathon.

<!---HONumber=AcomDC_0622_2016-->