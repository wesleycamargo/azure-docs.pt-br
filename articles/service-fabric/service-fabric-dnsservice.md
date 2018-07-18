---
title: Serviço DNS do Azure Service Fabric | Microsoft Docs
description: Use o serviço DNS do Service Fabric para descobrir microsserviços no cluster.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 656aed1f1fbd3294c4318520058ace480fd2219c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204987"
---
# <a name="dns-service-in-azure-service-fabric"></a>Serviço DNS no Azure Service Fabric
O Serviço DNS é um serviço do sistema opcional que pode ser habilitado no cluster para descobrir outros serviços usando o protocolo DNS. 

Muitos serviços, especialmente serviços em contêineres, podem ter um nome de URL existente e ter a capacidade de resolvê-los usando o protocolo DNS padrão (em vez do protocolo do Serviço de Nomeação) é desejável, especialmente em cenários “lift-and-shift”. O serviço DNS permite mapear nomes DNS para um nome de serviço e, portanto, resolver endereços IP do ponto de extremidade. 

O serviço DNS mapeia nomes DNS para nomes de serviço, que por sua vez, são resolvidos pelo serviço de nomenclatura para retornar o ponto de extremidade de serviço. O nome DNS do serviço é fornecido no momento da criação.

![pontos de extremidade de serviço](./media/service-fabric-dnsservice/dns.png)

As portas dinâmicas não são compatível com o serviço DNS. Para resolver serviços expostos em portas dinâmicas, use o [serviço de proxy reverter](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Habilitando o serviço DNS
Ao criar cria um cluster usando o portal, o serviço DNS é ativado por padrão na caixa de seleção do **serviço Incluir DNS** no menu **Configuração de Cluster**:

![Habilitar o serviço DNS por meio do portal][2]

Se você não estiver usando o portal para criar o cluster ou se estiver atualizando um cluster existente, precisará habilitar o serviço DNS em um modelo:

- Para implantar um novo cluster, use os [modelos de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) ou crie seu próprio modelo do gerenciador de recursos. 
- Para atualizar um cluster existente, você pode navegar para o grupo de recursos do cluster no portal e clicar em **script de automação** para trabalhar com um modelo que reflete o estado atual do cluster e outros recursos do grupo. Para saber mais, consulte[Exportar o modelo do grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group).

Depois de ter um modelo, habilite o serviço DNS com as seguintes etapas:

1. Verifique se `apiversion` está definido como `2017-07-01-preview` ou posterior para o `Microsoft.ServiceFabric/clusters` recurso, caso contrário, atualize-o conforme mostrado no trecho a seguir:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora, habilite o serviço DNS adicionando a seção `addonFeatures` a seguir após a seção `fabricSettings`, conforme mostrado neste trecho: 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. Depois de atualizar o modelo de cluster com as alterações anteriores, aplique-as e permita a conclusão da atualização. Quando a atualização for concluída, o serviço do sistema DNS começa a ser executado em seu cluster. O nome do serviço é `fabric:/System/DnsService` e você pode encontrar na seção do serviço **Sistema** no explorador do Microsoft Azure Service Fabric. 


## <a name="setting-the-dns-name-for-your-service"></a>Configurando o nome DNS para o serviço
Assim que o serviço DNS estiver em execução no cluster, você pode definir um nome DNS para os serviços de forma declarativa para serviços padrão no `ApplicationManifest.xml` ou por meio de comandos do PowerShell.

O nome DNS para seu serviço pode ser resolvido em todo o cluster. É altamente recomendável que você use um esquema de nomenclatura de `<ServiceDnsName>.<AppInstanceName>`; por exemplo, `service1.application1`. Isso garante a exclusividade do nome DNS em todo o cluster. Se um aplicativo for implantado usando a composição do docker, automaticamente são atribuídos nomes DNS usando esse esquema de nomenclatura.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Definindo o nome DNS de um serviço padrão no ApplicationManifest.xml
Abra o projeto no Visual Studio ou em seu editor favorito e abra o arquivo `ApplicationManifest.xml`. Acesse a seção de serviços padrão e, para cada serviço, adicione o atributo `ServiceDnsName`. O exemplo a seguir mostra como definir o nome DNS do serviço como `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
Depois que o aplicativo for implantado, a instância de serviço no Service Fabric Explorer mostrará o nome DNS dessa instância, conforme mostrado na figura a seguir: 

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
Se você implantar mais de um serviço, poderá encontrar os pontos de extremidade de outros serviços com os quais se comunicar usando um nome DNS. O serviço DNS só é aplicável aos serviços sem monitoração de estado, já que o protocolo DNS não pode se comunicar com os serviços com monitoração de estado. Para serviços com estado, use o [serviço proxy reverter ](./service-fabric-reverseproxy.md) interno para chamadas http ara chamar uma partição de serviço particular. As portas dinâmicas não são compatível com o serviço DNS. Você pode usar o proxy reverter para resolver os serviços que usam portas dinâmicas.

O código a seguir mostra como chamar outro serviço, que é simplesmente uma chamada http regular onde você pode fornecer a porta e qualquer caminho opcional como parte da URL.

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
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
