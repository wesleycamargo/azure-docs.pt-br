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

Neste artigo, vamos configurar um front-end da Web que pode ser escalado verticalmente para oferecer serviços por trás do balanceamento de carga do Azure.


## Pré-requisitos

[Implantar uma instância do Serviço de Contêiner do Azure](container-service-deployment.md) com o tipo de orquestrador DCOS, [garantir que o cliente possa se conectar ao cluster](container-service-connect.md) e[AZURE.INCLUDE [instalar a CLI do DC/OS](../../includes/container-service-install-dcos-cli-include.md)].


## Balanceamento de carga

Há duas camadas de balanceamento de carga em um cluster do Serviço de Contêiner: o balanceamento de carga do Azure para os pontos de entrada públicos (aqueles que serão acessados pelos usuários finais) e o marathon-lb subjacente que roteia solicitações de entrada para atender às solicitações de instâncias de contêiner. À medida que dimensionarmos os contêineres que fornecem o serviço, o marathon-lb se adaptará dinamicamente.

## Balanceamento de carga do Marathon 

A solução de balanceamento de carga do Marathon reconfigura a si mesma com base nos contêineres implantados. Também é resiliente à perda de um contêiner ou de um agente; se isso ocorrer, o Mesos simplesmente reiniciará o contêiner em outro lugar e reconfigurará o balanceamento de carga do Marathon.

Para instalar o balanceamento de carga do Marathon, execute o seguinte comando do computador cliente:

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
  * Defina o valor de HAProxy\_0\_VHOST como o FQDN do balanceador de carga para seus agentes. Isso tem o formato `<acsName>agents.<region>.cloudapp.azure.com`. Por exemplo, se eu tiver criado um cluster do Serviço de Contêiner com nome `myacs` na região `West US`, o FQDN seria: `myacsagents.westus.cloudapp.azure.com`. Você também pode encontrar isso procurando pelo balanceador de carga com "agent" no nome ao examinar os recursos no grupo de recursos criado para seu serviço de contêiner no [Portal do Azure](https://portal.azure.com).
  * Defina o servicePort para uma porta > = 10.000. Isso identifica o serviço que está sendo executado no contêiner; o marathon-lb o utiliza para identificar os serviços que deve balancear.
  * Defina o rótulo HAPROXY\_GROUP como "externo".
  * Defina hostPort como 0. Fazendo isso, esse marathon alocará arbitrariamente uma porta disponível.

Copie esse JSON em um arquivo chamado `hello-web.json` e use-o para implantar um contêiner:

```bash
dcos marathon app add hello-web.json 
``` 

## Balanceamento de carga do Azure 

Por padrão, o balanceamento de carga do Azure expõe as portas 80, 8080 e 443. Se você estiver usando essas três portas (como ocorre no exemplo acima), não será necessário fazer nada: você conseguirá acessar o FQDN do agente do balanceamento de carga, e cada vez que atualizar, acessará um dos três servidores Web em um estilo round robin. Se, no entanto, você usar uma porta diferente, será necessário adicionar uma regra de round robin e uma investigação ao balanceamento de carga do Azure à porta usada. Isso pode ser feito da [CLI XPLAT do Azure](../xplat-cli-azure-resource-manager.md) com os comandos `azure lb rule create` e `azure lb probe create`.


## Cenários adicionais

Você pode ter um cenário onde usa domínios diferentes para expor serviços diferentes. Por exemplo:

meudominio1.com -> Azure LB:80 -> marathon-lb:10001 -> meuconteiner1:33292 meudomínio2.com -> Azure LB:80 -> marathon-lb:10002 -> meuconteiner2:22321

Para fazer isso, confira [Hosts virtuais](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), que fornecem uma maneira de associar os domínios a caminhos específicos do marathon-lb.

Como alternativa, você pode expor portas diferentes e remapeá-las para o serviço correto por trás do marathon-lb. Por exemplo:

Azure lb:80 -> marathon-lb:10001 -> meuconteiner:233423 Azure lb:8080 -> marathon-lb:1002 -> meuconteiner2:33432
 

## Próximas etapas

Confira [esta postagem de blog](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/) para saber mais sobre o balanceamento de carga do Marathon.

<!---HONumber=AcomDC_0525_2016-->