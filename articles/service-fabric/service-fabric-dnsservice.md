---
title: "Serviço DNS do Azure Service Fabric | Microsoft Docs"
description: "Use o serviço DNS do Service Fabric para descobrir microsserviços no cluster."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e0f6a3a91207b73320d60a498d635262ef730d89
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="dns-service-in-azure-service-fabric"></a>Serviço DNS no Azure Service Fabric
O Serviço DNS é um serviço do sistema opcional que pode ser habilitado no cluster para descobrir outros serviços usando o protocolo DNS.

Muitos serviços, especialmente serviços em contêineres, podem ter um nome de URL existente e ter a capacidade de resolvê-los usando o protocolo DNS padrão (em vez do protocolo do Serviço de Nomeação) é muito conveniente, especialmente em cenários que usam o método lift-and-shift para aplicativos. O serviço DNS permite mapear nomes DNS para um nome de serviço e, portanto, resolver endereços IP do ponto de extremidade. 

Conforme mostrado no diagrama a seguir, o serviço DNS, em execução no cluster do Service Fabric, mapeia nomes DNS para nomes de serviço que são então resolvidos pelo Serviço de Nomeação para retornar os endereços de ponto de extremidade aos quais se conectar. O nome DNS do serviço é fornecido no momento da criação. 

![pontos de extremidade de serviço][0]

## <a name="enabling-the-dns-service"></a>Habilitando o serviço DNS
Primeiro, você precisa habilitar o serviço DNS no cluster. Obtenha o modelo para o cluster que você deseja implantar. Use os [modelos de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) ou crie um modelo do Resource Manager. Habilite o serviço DNS com as seguintes etapas:

1. Primeiro, verifique se a `apiversion` está definida como `2017-07-01-preview` para o recurso `Microsoft.ServiceFabric/clusters`, conforme mostrado no trecho a seguir. Se ela for diferente, você precisará atualizar a `apiVersion` para o valor `2017-07-01-preview`

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora, habilite o serviço DNS adicionando a seção `addonFeatures` a seguir após a seção `fabricSettings`, conforme mostrado abaixo

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. Depois de atualizar o modelo de cluster com essas alterações, aplique-as e permita que a atualização seja concluída. Depois de concluído, você verá o serviço do sistema DNS em execução no cluster, que é chamado `fabric:/System/DnsService` na seção de serviço do sistema no Service Fabric Explorer. 

## <a name="setting-the-dns-name-for-your-service"></a>Configurando o nome DNS para o serviço
Agora que o serviço DNS está em execução no cluster, você pode definir um nome DNS para os serviços de forma declarativa para serviços padrão no `ApplicationManifest.xml` ou por meio do PowerShell.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Definindo o nome DNS de um serviço padrão no ApplicationManifest.xml
Abra o projeto no Visual Studio ou em seu editor favorito e abra o arquivo `ApplicationManifest.xml`. Acesse a seção de serviços padrão e, para cada serviço, adicione o atributo `ServiceDnsName`. O exemplo a seguir mostra como definir o nome DNS do serviço como `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
Agora implante o aplicativo. Depois que o aplicativo for implantado, navegue para a instância de serviço no Service Fabric Explorer e você poderá ver o nome DNS dessa instância, conforme mostrado abaixo. 

![pontos de extremidade de serviço][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Configurando o nome DNS de um serviço usando o PowerShell
Defina o nome DNS de um serviço ao criá-lo usando o `New-ServiceFabricService` PowerShell. O exemplo a seguir cria um novo serviço sem estado com o nome DNS `service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>Usando o DNS nos serviços
Se você implantar mais de um serviço, poderá encontrar os pontos de extremidade de outros serviços com os quais se comunicar usando um nome DNS. Isso se aplica somente aos serviços sem monitoração de estado, desde o protocolo DNS não reconhece quem se comunicará com os serviços com estado. Para serviços com estado, use o proxy reverso interno para chamadas HTTP para chamar uma partição de serviço específica.

O código a seguir mostra como chamar outro serviço, que é apenas uma chamada HTTP comum. Observe que você precisará fornecer a porta e o caminho opcional como parte da URL.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a comunicação de serviço no cluster com [Conectar e comunicar-se com serviços](service-fabric-connect-and-communicate-with-services.md)

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG

