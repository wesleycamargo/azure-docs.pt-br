---
title: Proxy Inverso do Service Fabric | Microsoft Docs
description: "Usar o proxy inverso do Service Fabric para comunicação com microsserviços dentro e fora do cluster"
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 01/04/2017
ms.author: bharatn
translationtype: Human Translation
ms.sourcegitcommit: c738b9d6461da032f216b8a51c69204066d5cfd3
ms.openlocfilehash: 9487209a8e5d976d56da50b8c70e69950d0ad129


---
# <a name="service-fabric-reverse-proxy"></a>Proxy Inverso do Service Fabric
O proxy inverso do Service Fabric é uma malha de serviço integrada ao proxy inverso que permite a abordagem de microsserviços no cluster da malha de serviço que expõe pontos de extremidade HTTP.

## <a name="microservices-communication-model"></a>Modelo de comunicação de microsserviços
Normalmente, os microsserviços no Service Fabric executado em um subconjunto de VMs do cluster e podem ser movidos de uma VM para outras por vários motivos. Assim, os pontos de extremidade para microsserviços podem ser alterados dinamicamente. O padrão típico para se comunicar com o microsserviço é o loop de resolução abaixo,

1. Resolva a localização do serviço inicialmente por meio do Serviço de Nomenclatura.
2. Conecte-se ao serviço.
3. Determine a causa de falhas de conexão e resolva novamente a localização do serviço quando necessário.

Esse processo geralmente envolve a quebra automática de bibliotecas de comunicação de cliente em um loop de repetição que implementa as políticas de repetição e resolução de serviço.
Para obter mais informações sobre esse tópico, confira o artigo sobre a [comunicação com os serviços](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-via-sf-reverse-proxy"></a>Comunicando-se pelo proxy inverso do SF
O proxy inverno do Service Fabric é executado em todos os nós no cluster. Ele executa o processo de resolução de todo o serviço em nome do cliente e, em seguida, encaminha a solicitação do cliente. Portanto, clientes em execução no cluster podem simplesmente usar bibliotecas de comunicação do cliente HTTP para se comunicar com o serviço de destino por meio do proxy inverso do SF executando localmente no mesmo nó.

![Comunicação interna][1]

## <a name="reaching-microservices-from-outside-the-cluster"></a>Alcançar Microsserviços de fora do cluster
O modelo de comunicação externa padrão para microsserviços é **aceitação**, em que cada serviço, por padrão, não pode ser acessado diretamente de clientes externos. O [Azure Load Balancer](../load-balancer/load-balancer-overview.md) é um limite de rede entre microsserviços e clientes externos, que executa a conversão de endereços de rede e encaminha as solicitações externas para pontos de extremidade de **IP: porta** internos. Para tornar o ponto de extremidade do microsserviço diretamente acessível para clientes externos, o Azure Load Balancer primeiro deve ser configurado para encaminhar o tráfego para cada porta usada pelo serviço do cluster. Além disso, a maioria dos microsserviços (especialmente microsserviços com estado) não vivem em todos os nós do cluster e podem se mover entre nós de failover, assim, nesses casos, o Azure Load Balancer não pode determinar efetivamente o nó de destino em que as réplicas estão localizadas para encaminhar o tráfego.

### <a name="reaching-microservices-via-the-sf-reverse-proxy-from-outside-the-cluster"></a>Acessar os Microsserviços por meio do proxy inverso do SF de fora do cluster
Em vez de configurar portas de serviço individuais no Azure Load Balancer, apenas a porta de proxy inverso do SF pode ser configurada nele. Isso permite que clientes fora do cluster alcancem serviços dentro do cluster por meio do proxy reverso sem configurações adicionais.

![Comunicação externa][0]

> [!WARNING]
> Configurar a porta do proxy reverso no balanceador de carga faz com que todos os microsserviços no cluster que expõem um ponto de extremidade HTTP se tornem endereçáveis da parte externa do cluster.
> 
> 

## <a name="uri-format-for-addressing-services-via-the-reverse-proxy"></a>Formato de URI para abordar serviços por meio do proxy inverso
O proxy Inverso usa um formato de URI específico para identificar para qual partição de serviço a solicitação de entrada deve ser encaminhada:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

* **http (s):** o proxy inverso pode ser configurado para aceitar o tráfego HTTP ou HTTPS. No caso do tráfego HTTPS, a terminação SSL ocorre no proxy reverso. Solicitações que são encaminhadas pelo proxy inverso para serviços no cluster são por http. **Atualmente, não há suporte para serviços HTTPS.**
* **FQDN do cluster | IP interno:** para clientes externos, o proxy reverso pode ser configurado para que seja acessível por meio do domínio do cluster (por exemplo, mycluster.eastus.cloudapp.azure.com). Por padrão, o proxy reverso é executado em cada nó, então para o tráfego interno, ele pode ser acessado no host local ou em qualquer nó interno de IP (por exemplo, 10.0.0.1).
* **Porta:** a porta que foi especificada para o proxy inverso. Por exemplo, 19008.
* **ServiceInstanceName:** esse é o nome da instância de serviço implantado totalmente qualificado do serviço que você está tentando acessar do esquema "fabric:/". Por exemplo, para alcançar o serviço *fabric:/myapp/myservice/*, você usaria *myapp/myservice*.
* **Caminho de sufixo:** esse é o caminho de URL real para o serviço que você deseja se conectar. Por exemplo, *myapi/values/add/3*
* **PartitionKey:** para um serviço particionado, esta é a chave de partição computada da partição que você deseja acessar. Observe que isso *não* é o GUID da ID da partição. Esse parâmetro não é necessário para serviços usando o esquema de partição de singleton.
* **PartitionKind:** o esquema de partição de serviço. Isso pode ser 'Int64Range' ou 'Named'. Esse parâmetro não é necessário para serviços usando o esquema de partição de singleton.
* **Tempo limite:** especifica o tempo limite da solicitação http criada pelo proxy inverso para o serviço em nome da solicitação do cliente. O valor padrão é 60 segundos. Este é um parâmetro opcional.

### <a name="example-usage"></a>Exemplo de uso
Por exemplo, vamos pegar o serviço **fabric:/MyApp/MyService** que abre um ouvinte HTTP na URL a seguir:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Com os seguintes recursos:

* `/index.html`
* `/api/users/<userId>`

Se o serviço usar o esquema de particionamento de singleton, os parâmetros de cadeia de consulta *PartitionKey* e *PartitionKind* não serão necessários e o serviço poderá ser acessado pelo gateway da seguinte maneira:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
* Internamente: `http://localhost:19008/MyApp/MyService`

Se o serviço usar o esquema de particionamento Int64 Uniforme, os parâmetros de cadeia de caracteres de consulta *PartitionKey* e *PartitionKind* deverão ser usados para acessar uma partição do serviço:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Para acessar os recursos expostos pelo serviço, basta coloca o caminho do recurso após o nome do serviço na URL:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

O gateway, em seguida, encaminhará essas solicitações para a URL do serviço:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Tratamento especial para os serviços de compartilhamento de porta
O Application Gateway tenta resolver um endereço de serviço novamente e repetir a solicitação quando um serviço não pode ser alcançado. Este é um dos principais benefícios do gateway, pois o código do cliente não precisa implementar sua própria solução de serviço e resolver o loop.

Geralmente, quando um serviço não pode ser acessado, isso significa que a instância de serviço ou a réplica foi movida para um nó diferente como parte de seu ciclo de vida normal. Quando isso acontece, o gateway pode receber um erro de conexão de rede que indica que um ponto de extremidade não está aberto no endereço resolvido originalmente.

No entanto, réplicas ou instâncias de serviço podem compartilhar um processo de host e uma porta quando hospedadas por um servidor Web baseado em http.sys, incluindo:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [WebListener de Núcleo ASP.NET](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Nessa situação, é provável que o servidor Web esteja disponível no processo de host e respondendo às solicitações, mas a instância de serviço resolvido ou a réplica não está mais disponível no host. Nesse caso, o gateway receberá uma resposta HTTP 404 do servidor Web. Como resultado, um HTTP 404 tem dois significados distintos:

1. O endereço do serviço está correto, mas o recurso solicitado pelo usuário não existe.
2. O endereço do serviço está incorreto e o recurso solicitado pelo usuário, na verdade, pode existir em um nó diferente.

No primeiro caso, ele é um HTTP 404 normal, que é considerado um erro de usuário. No entanto, no segundo caso, o usuário solicitou um recurso que existe, mas o gateway não pôde localizá-lo porque o serviço em si foi movido, caso em que o gateway precisa resolver o endereço mais uma vez e tentar novamente.

O gateway, portanto, precisa de uma maneira para distinguir entre esses dois casos. Para fazer essa distinção, uma dica do servidor é necessária.

* Por padrão, o Application Gateway assume caso nº 2 e tenta resolver mais uma vez e emitir novamente a solicitação.
* Para indicar o caso nº 1 para o Application Gateway, o serviço deve retornar o seguinte cabeçalho de resposta HTTP:

`X-ServiceFabric : ResourceNotFound`

Esse cabeçalho de resposta HTTP indica uma situação de HTTP 404 normal em que o recurso solicitado não existir e o gateway não tentará resolver o endereço do serviço novamente.

## <a name="setup-and-configuration"></a>Instalação e configuração
O proxy inverso do Service Fabric pode ser habilitado para o cluster por meio do [modelo do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Depois de obter o modelo do cluster que você deseja implantar (de modelos de exemplo ou criando um modelo do Resource Manager personalizado), o proxy reverso poderá ser habilitado no modelo por meio das seguintes etapas.

1. Defina uma porta para o proxy inverso na [seção Parâmetros](../azure-resource-manager/resource-group-authoring-templates.md) do modelo.
   
    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Especifique a porta para cada um dos objetos nodetype no **Cluster** [seção Tipo de recurso](../azure-resource-manager/resource-group-authoring-templates.md)
   
    Para a apiVersion anterior a “01/09/2016”, a porta é identificada pelo nome do parâmetro ***httpApplicationGatewayEndpointPort***
   
    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
   
    Para a apiVersion de “01/09/2016” ou posterior, a porta é identificada pelo nome do parâmetro ***reverseProxyEndpointPort***
   
    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Para resolver o proxy inverso de fora do cluster do Azure, configure as **regras do Azure Load Balancer** para a porta especificada na etapa 1.
   
    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Para configurar certificados SSL na porta de proxy Inverso, adicione o certificado à propriedade httpApplicationGatewayCertificate no **Cluster** [seção Tipo de recurso](../azure-resource-manager/resource-group-authoring-templates.md)
   
    Para a apiVersion anterior a “01/09/2016”, o certificado é identificado pelo nome do parâmetro ***httpApplicationGatewayCertificate***
   
    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```
    Para a apiVersion de “01/09/2016” ou posterior, o certificado é identificado pelo nome do parâmetro ***reverseProxyCertificate***
   
    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## <a name="next-steps"></a>Próximas etapas
* Confira um exemplo de comunicação HTTP entre serviços em um [projeto de exemplo no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).
* [Comunicação remota de serviço com os Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API Web que usa o OWIN nos Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicação WCF usando os Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png



<!--HONumber=Feb17_HO3-->


