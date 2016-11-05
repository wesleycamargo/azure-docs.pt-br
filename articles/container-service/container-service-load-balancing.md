---
title: Balancear a carga de contêineres em um cluster do Serviço de Contêiner do Azure | Microsoft Docs
description: Balancear a carga entre vários contêineres em um cluster do Serviço de Contêiner do Azure.
services: container-service
documentationcenter: ''
author: rgardler
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Contêineres, microsserviços, DC/OS, Azure

ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2016
ms.author: rogardle

---
# <a name="load-balance-containers-in-an-azure-container-service-cluster"></a>Balancear a carga de contêineres em um cluster do Serviço de Contêiner do Azure
Neste artigo, vamos explorar como criar um balanceador de carga interno em um Serviço de Contêiner do Azure gerenciado por DC/OS usando o Marathon-LB. Isso permitirá que você dimensione horizontalmente seus aplicativos. Também ajudará você a tirar proveito dos clusters de agentes públicos e privados, colocando seus balanceadores de carga no cluster público e seus contêineres de aplicativo no cluster privado.

## <a name="prerequisites"></a>Pré-requisitos
[Implantar uma instância do Serviço de Contêiner do Azure](container-service-deployment.md) com o tipo de orquestrador DC/OS e [garantir que o cliente possa se conectar ao cluster](container-service-connect.md). 

## <a name="load-balancing"></a>Balanceamento de carga
Há duas camadas de balanceamento de carga no cluster do Serviço de Contêiner que criaremos: 

1. O Azure Load Balancer fornece pontos de entrada públicos (aqueles que os usuários finais acessarão). Isso é fornecido automaticamente pelo Serviço de Contêiner do Azure e, por padrão, é configurado para exibir as portas 80, 443 e 8080.
2. O Balanceador de Carga do Marathon (marathon-lb) encaminha as solicitações de entrada para as instâncias do contêiner que atendem essas solicitações. Como dimensionamos os contêineres fornecendo nosso serviço Web, o marathon-lb se adapta dinamicamente. Esse balanceador de carga não é fornecido por padrão em seu Serviço de Contêiner, mas é muito fácil de instalar.

## <a name="marathon-load-balancer"></a>Balanceador de Carga do Marathon
O Balanceador de Carga do Marathon reconfigura dinamicamente baseado nos contêineres que você implantou. Também é resistente à perda de um contêiner ou um agente - se isso ocorrer, o Apache Mesos simplesmente reiniciará o contêiner em outro lugar e o marathon-lb se adaptará.

Para instalar o Balanceador de Carga do Marathon, você pode usar a IU da Web do DC/SO ou a linha de comando.

### <a name="install-marathon-lb-using-dc/os-web-ui"></a>Instalar o Marathon-LB usando a IU da Web do DC/SO
1. Clicar em 'Universo'
2. Pesquisar 'Marathon-LB'
3. Clicar em ‘Instalar’

![Instalando o marathon-lb por meio da Interface da Web do DC/SO](./media/dcos/marathon-lb-install.png)

### <a name="install-marathon-lb-using-the-dc/os-cli"></a>Instalar o Marathon-LB usando a CLI do DC/SO
Depois de instalar a CLI do DC/SO e assegurar que você pode conectar o cluster, execute o seguinte comando a partir do computador cliente:

```bash
dcos package install marathon-lb
```

Este comando instala automaticamente o balanceador de carga no cluster de agentes público.

## <a name="deploy-a-load-balanced-web-application"></a>Implantar um Aplicativo Web com Carga Balanceada
Agora que temos o pacote marathon-lb, podemos implantar um contêiner de aplicativo para o qual desejamos balancear a carga. Para este exemplo, implantaremos um servidor Web simples usando a seguinte configuração:

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
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

* Defina o valor de `HAProxy_0_VHOST` para o FQDN do balanceador de carga de seus agentes. Isso está no formato `<acsName>agents.<region>.cloudapp.azure.com`. Por exemplo, se você criasse um cluster do Serviço de Contêiner com o nome `myacs`, na região `West US`, o FQDN seria `myacsagents.westus.cloudapp.azure.com`. Você também pode encontrar isso procurando o balanceador de carga com "agente" no nome ao examinar os recursos no grupo de recursos criado para o Serviço de Contêiner no [Portal do Azure](https://portal.azure.com).
* Defina o servicePort para uma porta > = 10.000. Isso identifica o serviço que está sendo executado no contêiner; o marathon-lb o utiliza para identificar os serviços que deve balancear.
* Defina o rótulo `HAPROXY_GROUP` como "externo".
* Defina `hostPort` como 0. Isso significa que o Marathon alocará arbitrariamente uma porta disponível.
* Defina `instances` como o número de instâncias que você deseja criar. Sempre é possível dimensionar isso posteriormente.

Vale a pena saber que, por padrão, o Marathon implantará o cluster privado, e isso significa que a implantação acima só poderá ser acessada por meio do balanceador de carga, que geralmente é o comportamento desejado.

### <a name="deploy-using-the-dc/os-web-ui"></a>Implantar usando a IU da Web do DC/SO
1. Visite a página do Marathon em http://localhost/marathon (depois de configurar seu [túnel SSH](container-service-connect.md) e clique em `Create Appliction`
2. No diálogo `New Application`, clique em `JSON Mode` no canto superior direito
3. Colar o JSON acima no editor
4. Clique em `Create Appliction`

### <a name="deploy-using-the-dc/os-cli"></a>Implantar usando a CLI do DC/SO
Para implantar esse aplicativo com a CLI do DC/SO, basta copiar o JSON acima para um arquivo chamado `hello-web.json` e executar:

```bash
dcos marathon app add hello-web.json
```

## <a name="azure-load-balancer"></a>Azure Load Balancer
Por padrão, o Azure Load Balancer expõe as portas 80, 8080 e 443. Se você estiver usando uma dessas três portas (como ocorre no exemplo acima), não haverá mais nada a fazer. Você deve conseguir atingir o FQDN do balanceador de carga do agente, e cada vez que atualizar, encontrará um dos três servidores Web em round-robin. Se, no entanto, você usar uma porta diferente, será necessário adicionar uma regra de round robin e uma investigação ao balanceador de carga do Azure à porta usada. Você pode fazer isso na [CLI do Azure](../xplat-cli-azure-resource-manager.md) com os comandos `azure network lb rule create` e `azure network lb probe create`. Você também pode fazer isso usando o Portal do Azure.

## <a name="additional-scenarios"></a>Cenários adicionais
Você pode ter um cenário onde usa domínios diferentes para expor serviços diferentes. Por exemplo:

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292  
mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

Para tanto, confira os [hosts virtuais](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), que fornecem uma maneira de associar os domínios aos caminhos específicos do marathon-lb.

Como alternativa, você pode expor portas diferentes e remapeá-las para o serviço correto por trás do marathon-lb. Por exemplo:

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432

## <a name="next-steps"></a>Próximas etapas
Veja a documentação do DC/SO para saber mais sobre o [marathon-lb](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/).

<!--HONumber=Oct16_HO2-->


