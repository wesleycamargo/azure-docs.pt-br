---
title: Proxy reverso do Azure Service Fabric | Microsoft Docs
description: "Usar o proxy reverso do Service Fabric para comunicação com microsserviços de dentro e fora do cluster."
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
ms.date: 04/07/2017
ms.author: bharatn
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 121bf91a2476a079c0737187aef8791be0b4b250
ms.lasthandoff: 04/07/2017


---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Proxy reverso no Azure Service Fabric
O proxy reverso que está incorporado ao Azure Service Fabric endereça microsserviços no cluster do Service Fabric que expõe pontos de extremidade HTTP.

## <a name="microservices-communication-model"></a>Modelo de comunicação de microsserviços
Normalmente, os microsserviços no Service Fabric são executados em um subconjunto de máquinas virtuais no cluster e podem ser movidos de uma máquina virtual para outra por vários motivos. Assim, os pontos de extremidade para microsserviços podem ser alterados dinamicamente. O padrão típico para se comunicar com o microsserviço é o loop de resolução a seguir:

1. Resolva a localização do serviço inicialmente por meio do serviço de nomenclatura.
2. Conecte-se ao serviço.
3. Determine a causa de falhas de conexão e resolva novamente a localização do serviço quando necessário.

Esse processo geralmente envolve a quebra automática de bibliotecas de comunicação de cliente em um loop de repetição que implementa as políticas de repetição e resolução de serviço.
Para obter mais informações, consulte [Conectar-se a serviços e se comunicar com eles](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Comunicar-se usando o proxy reverso
O proxy reverso do Service Fabric é executado em todos os nós no cluster. Ele executa o processo de resolução de todo o serviço em nome do cliente e, em seguida, encaminha a solicitação do cliente. Portanto, clientes em execução no cluster podem simplesmente usar bibliotecas de comunicação HTTP do lado do cliente para se comunicar com o serviço de destino por meio do proxy reverso executado localmente no mesmo nó.

![Comunicação interna][1]

## <a name="reaching-microservices-from-outside-the-cluster"></a>Alcançar microsserviços de fora do cluster
O modelo de comunicação externa padrão para microsserviços é um modelo de aceitação em que cada serviço não pode ser acessado diretamente de clientes externos. O [Azure Load Balancer](../load-balancer/load-balancer-overview.md), que é um limite de rede entre microsserviços e clientes externos, executa a conversão de endereços de rede e encaminha as solicitações externas para pontos de extremidade de IP:porta internos. Para tornar o ponto de extremidade do microsserviço diretamente acessível para clientes externos, você deve primeiro configurar o Load Balancer para encaminhar o tráfego para cada porta usada pelo serviço no cluster. Além disso, a maioria dos microsserviços, especialmente microsserviços com estado, não estão em todos os nós do cluster. Os microsserviços podem mover-se entre os nós no failover. Nesses casos, o Load Balancer não pode determinar efetivamente o local do nó de destino das réplicas para o qual encaminhar tráfego.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Acessar os microsserviços por meio do proxy reverso do Service Fabric de fora do cluster
Em vez de configurar a porta de um serviço individual no Load Balancer, você pode configurar apenas a porta do proxy reverso no Load Balancer. Isso permite que clientes fora do cluster alcancem serviços dentro do cluster por meio do proxy reverso sem configurações adicionais.

![Comunicação externa][0]

> [!WARNING]
> Quando você configura a porta do proxy reverso no Load Balancer, todos os microsserviços no cluster que expõem um ponto de extremidade HTTP se tornam endereçáveis da parte externa do cluster.
>
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Formato de URI para endereçamento de serviços por meio do proxy reverso
O proxy reverso usa um formato de URI (Uniform Resource Identifier) específico para identificar a partição de serviço para a qual a solicitação de entrada deve ser encaminhada:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** o proxy inverso pode ser configurado para aceitar o tráfego HTTP ou HTTPS. Para tráfego HTTPS, a terminação de protocolo SSL ocorre no proxy reverso. O proxy reverso usa HTTP para encaminhar solicitações para serviços no cluster.

    Observe que atualmente, não há suporte para serviços HTTPS.
* **FQDN (nome de domínio totalmente qualificado) do cluster | IP interno:** para clientes externos, o proxy reverso pode ser configurado para que seja acessível por meio do domínio do cluster, por exemplo, mycluster.eastus.cloudapp.azure.com. Por padrão, o proxy reverso é executado em todos os nós. Para o tráfego interno, o proxy reverso pode ser alcançado no localhost ou em qualquer IP de nó interno (por exemplo, 10.0.0.1).
* **Porta:** é a porta que foi especificada para o proxy inverso, por exemplo, 19008.
* **ServiceInstanceName:** esse é o nome totalmente qualificado da instância de serviço implantado que você está tentando alcançar sem o esquema "fabric:/". Por exemplo, para alcançar o serviço *fabric:/myapp/myservice/*, você usaria *myapp/myservice*.
* **Caminho de sufixo:** esse é o caminho de URL real, por exemplo, *myapi/values/add/3*, para o serviço ao qual você deseja se conectar.
* **PartitionKey:** para um serviço particionado, esta é a chave de partição computada da partição que você deseja alcançar. Observe que isso *não* é o GUID da ID da partição. Esse parâmetro não é necessário para serviços usando o esquema de partição de singleton.
* **PartitionKind:** o esquema de partição de serviço. Isso pode ser 'Int64Range' ou 'Named'. Esse parâmetro não é necessário para serviços usando o esquema de partição de singleton.
* **ListenerName** Os pontos de extremidade do serviço estão no formato {"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}}. Quando o serviço expõe vários pontos de extremidade, isso identifica o ponto de extremidade ao qual solicitação do cliente deve ser encaminhada. Isso poderá ser omitido se o serviço tiver somente um ouvinte.
* **TargetReplicaSelector** Especifica como a réplica de destino ou a instância deve ser selecionada.
  * Quando o serviço de destino tem monitoração de estado, TargetReplicaSelector pode ser um dos seguintes: 'PrimaryReplica', 'RandomSecondaryReplica' ou 'RandomReplica'. Quando esse parâmetro não é especificado, o padrão é 'PrimaryReplica'.
  * Quando o serviço de destino não tem monitoração de estado, o proxy reverso escolhe uma instância aleatória da partição de serviço para encaminhar a solicitação a ela.
* **Tempo limite:** especifica o tempo limite da solicitação HTTP criada pelo proxy inverso para o serviço em nome da solicitação do cliente. O valor padrão é de 60 segundos. Este é um parâmetro opcional.

### <a name="example-usage"></a>Exemplo de uso
Por exemplo, vamos pegar o serviço *fabric:/MyApp/MyService* que abre um ouvinte HTTP na URL a seguir:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

A seguir estão os recursos para o serviço:

* `/index.html`
* `/api/users/<userId>`

Se o serviço usar o esquema de particionamento de singleton, os parâmetros de cadeia de consulta *PartitionKey* e *PartitionKind* não serão necessários e o serviço poderá ser acessado pelo uso do gateway da seguinte maneira:

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
O Gateway de Aplicativo do Azure tenta resolver um endereço de serviço novamente e repetir a solicitação quando um serviço não pode ser alcançado. Este é um dos principais benefícios do Gateway de Aplicativo, pois o código do cliente não precisa implementar sua própria solução de serviço e resolver o loop.

Geralmente, quando um serviço não pode ser acessado, a instância de serviço ou a réplica foi movida para um nó diferente como parte de seu ciclo de vida normal. Quando isso acontece, o Gateway de Aplicativo pode receber um erro de conexão de rede que indica que um ponto de extremidade não está aberto no endereço resolvido originalmente.

No entanto, réplicas ou instâncias de serviço podem compartilhar um processo de host e uma porta quando hospedadas por um servidor Web baseado em http.sys, incluindo:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [WebListener de Núcleo ASP.NET](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Nessa situação, é provável que o servidor Web esteja disponível no processo de host e respondendo às solicitações, mas a instância de serviço resolvido ou a réplica não está mais disponível no host. Nesse caso, o gateway receberá uma resposta HTTP 404 do servidor Web. Como resultado, um HTTP 404 tem dois significados distintos:

- Caso n. 1: o endereço do serviço está correto, mas o recurso solicitado pelo usuário não existe.
- Caso n. 2: o endereço do serviço está incorreto e o recurso solicitado pelo usuário talvez exista em um nó diferente.

No primeiro caso, ele é um HTTP 404 normal, que é considerado um erro de usuário. No entanto, no segundo caso, o usuário solicitou um recurso que existe. O Gateway de Aplicativo não pôde localizá-lo porque o serviço em si foi movido. O Gateway de Aplicativo precisa resolver o endereço novamente e repetir a solicitação.

O Gateway de Aplicativo, portanto, precisa de uma maneira para distinguir entre esses dois casos. Para fazer essa distinção, uma dica do servidor é necessária.

* Por padrão, o Gateway de Aplicativo assume caso nº 2 e tenta resolver mais uma vez e emitir novamente a solicitação.
* Para indicar o caso n. 1 para o Gateway de Aplicativo, o serviço deve retornar o seguinte cabeçalho de resposta HTTP:

  `X-ServiceFabric : ResourceNotFound`

Esse cabeçalho de resposta HTTP indica uma situação de HTTP 404 normal em que o recurso solicitado não existir e o Gateway de Aplicativo não tentará resolver novamente o endereço do serviço.

## <a name="setup-and-configuration"></a>Instalação e configuração
O proxy reverso do Service Fabric pode ser habilitado para o cluster por meio do [modelo do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Consulte [Configurar proxy reverso HTTPS em um cluster seguro](https://github.com/ChackDan/Service-Fabric/tree/master/ARM Templates/ReverseProxySecureSample#configure-https-reverse-proxy-in-a-secure-cluster) para amostras de modelo do Azure Resource Manager para configurar o proxy reverso de segurança com um certificado e o tratamento da substituição de certificados.

Primeiro, você obtém o modelo para o cluster que deseja implantar. Você pode usar os modelos de exemplo ou criar um modelo do Resource Manager personalizado. Em seguida, você pode habilitar o proxy reverso usando as seguintes etapas:

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
2. Especifique a porta para cada um dos objetos nodetype no **Cluster** [seção Tipo de recurso](../azure-resource-manager/resource-group-authoring-templates.md).

    A porta é identificada pelo nome do parâmetro, reverseProxyEndpointPort.

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
3. Para resolver o proxy inverso de fora do cluster do Azure, configure as regras do Azure Load Balancer para a porta que você especificou na etapa 1.

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
4. Para configurar certificados SSL na porta de proxy Inverso, adicione o certificado à propriedade ***reverseProxyCertificate*** na [seção Tipo de recurso](../resource-group-authoring-templates.md) do **Cluster**.

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

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Suporte um certificado de proxy reverso diferente do certificado de cluster
 Se o certificado de proxy reverso é diferente do certificado que protege o cluster, o certificado especificado anteriormente deve ser instalado na máquina virtual e adicionado à ACL (lista de controle de acesso) para que o Service Fabric possa acessá-lo. Isso pode ser feito na [seção de Tipo de recurso](../resource-group-authoring-templates.md) **virtualMachineScaleSets**. Para instalação, adicione o certificado ao osProfile. A seção de extensão do modelo pode atualizar o certificado na ACL.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Ao usar certificados que são diferentes do certificado do cluster para habilitar o proxy reverso em um cluster existente, instale o certificado de proxy reverso e atualize a ACL no cluster antes de habilitar o proxy reverso. Conclua a implantação do [modelo do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) usando as configurações mencionadas acima antes de iniciar uma implantação para habilitar o proxy reverso usando as etapas 1 a 4.

## <a name="next-steps"></a>Próximas etapas
* Confira um exemplo de comunicação HTTP entre serviços em um [projeto de exemplo no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Comunicação remota de serviço com os Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API Web que usa o OWIN nos Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicação WCF usando os Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png

