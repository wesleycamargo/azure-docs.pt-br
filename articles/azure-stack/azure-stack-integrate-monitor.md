---
title: "Integrar a solução de monitoramento externa a pilha do Azure | Microsoft Docs"
description: "Aprenda a integrar o Azure pilha com uma solução de monitoramento externa em seu data center."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 3435ada40afb9f1c6e57be64d1b9086d0cdaefd9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrar a solução de monitoramento externa a pilha do Azure

Para o monitoramento externo da infraestrutura de pilha do Azure, você precisa monitorar o software de pilha do Azure, os computadores físicos e os comutadores de rede física. Cada uma dessas áreas oferece um método para recuperar as informações de integridade e o alerta:

- O software de pilha do Azure oferece uma API baseada em REST para recuperar a integridade e os alertas. (Com o uso de tecnologias definidas pelo software, como espaços de armazenamento diretos, alertas e a integridade de armazenamento fazem parte de monitoramento de software.).
- Computadores físicos podem disponibilizar integridade e informações de alerta por meio de controladores de gerenciamento da placa-base (BMC).
- Dispositivos de rede física podem disponibilizar integridade e informações de alerta por meio do protocolo SNMP.

Cada solução de pilha do Azure é fornecido com um host de ciclo de vida de hardware. Esse host executa o software de monitoramento do fornecedor de hardware do fabricante de equipamento Original (OEM) para os servidores físicos e os dispositivos de rede. Se desejar, você pode ignorar essas soluções de monitoramento e integra diretamente com soluções de monitoramento existentes em seu data center.

> [!IMPORTANT]
> Solução de monitoramento externa que você usar deve ser sem agente. Você não pode instalar agentes de terceiros dentro de componentes da pilha do Azure.

O diagrama a seguir mostra o fluxo do tráfego entre um sistema de pilha do Azure integradas, o host de ciclo de vida de hardware, uma solução de monitoramento e um sistema de coleta de tíquetes/dados externos.

![Diagrama mostrando o tráfego entre a pilha do Azure, monitoramento e solução de registro.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

Este artigo explica como integrar a pilha do Azure com soluções de monitoramento externas, como o System Center Operations Manager e Nagios. Ele também inclui como trabalhar com alertas programaticamente usando o PowerShell ou por meio de chamadas da API REST.

## <a name="integrate-with-operations-manager"></a>Integrar com o Operations Manager

Você pode usar o Operations Manager para monitorar externo da pilha do Azure. O pacote de gerenciamento do System Center para a pilha do Microsoft Azure permite monitorar várias implantações de pilha do Azure com uma única instância do Operations Manager. O pacote de gerenciamento usa o provedor de recursos de integridade e o provedor de recursos de atualização APIs REST para se comunicar com a pilha do Azure. Se você planeja ignorar OEM software que está em execução no host de ciclo de vida do hardware de monitoramento, você pode instalar pacotes de gerenciamento do fornecedor para monitorar servidores físicos. Você também pode usar a descoberta de dispositivo de rede do Operations Manager para monitorar os comutadores de rede.

O pacote de gerenciamento para a pilha do Azure fornece os seguintes recursos:

- Você pode gerenciar várias implantações de pilha do Azure
- Não há suporte para o Azure Active Directory (AD do Azure) e os serviços de Federação do Active Directory (AD FS)
- Você pode recuperar e fechar alertas
- Há uma integridade e um painel de capacidade
- Inclui a detecção de modo de manutenção automática para quando o patch e atualização (P & U) está em andamento
- Inclui tarefas de atualização forçada para implantação e região
- Você pode adicionar informações personalizadas a uma região
- Oferece suporte à notificação e o relatório

Você pode baixar o pacote de gerenciamento do System Center para a pilha do Microsoft Azure e os respectivos [guia do usuário](https://www.microsoft.com/en-us/download/details.aspx?id=55184), ou diretamente do Operations Manager.

Para uma solução de emissão de tíquetes, você pode integrar o Operations Manager com o System Center Service Manager. O conector de produto integrado permite a comunicação bidirecional que permite que você fechar um alerta na pilha do Azure e Operations Manager depois de resolver uma solicitação de serviço no Service Manager.

O diagrama a seguir mostra a integração da pilha do Azure com uma implantação existente do System Center. Você pode automatizar o Service Manager ainda mais com o System Center Orchestrator ou Service Management Automation (SMA) para executar operações na pilha do Azure.

![Diagrama mostrando a integração com o OM, o Service Manager e o SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrar com Nagios

Um plug-in de monitoramento de Nagios foi desenvolvido junto com as soluções de Cloudbase de parceiro, que está disponível sob a licença de software livre permissivo – MIT (Massachusetts Institute of Technology).

O plug-in é gravado em Python e utiliza o provedor de recursos de integridade API REST. Ele oferece a funcionalidade básica para recuperar e fechar alertas na pilha do Azure. Como o pacote de gerenciamento do System Center, ele permite que você adicionar várias implantações de pilha do Azure e enviar notificações.

O plug-in funciona com Nagios Enterprise e Nagios Core. Você pode baixá-lo [aqui](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). O site de download também inclui detalhes de instalação e configuração.

### <a name="plugin-parameters"></a>Parâmetros de plug-in

Configure o arquivo de plug-in "Azurestack_plugin.py" com os seguintes parâmetros:

| Parâmetro | DESCRIÇÃO | Exemplo |
|---------|---------|---------|
| *arm_endpoint* | Ponto de extremidade do Azure Resource Manager (administrador) |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Ponto de extremidade do Azure Resource Manager (administrador)  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | ID de assinatura de Admin | Recuperar por meio do portal do administrador ou o PowerShell |
| *User_name* | O nome de usuário do operador assinatura | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Senha de assinatura do operador | MyPassWord |
| *Client_id* | Cliente | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Nome da região de pilha do Azure | local |
|  |  |

* O GUID do PowerShell que é fornecido é universal. Você pode usá-lo para cada implantação.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Use o PowerShell para monitorar a integridade e alertas

Se você não estiver usando o Operations Manager, Nagios ou uma solução baseada em Nagios, você pode usar o PowerShell para habilitar uma ampla gama de soluções para integrar com o Azure pilha de monitoramento.
 
1. Para usar o PowerShell, certifique-se de que você tenha [PowerShell instalado e configurado](azure-stack-powershell-configure-quickstart.md) para um ambiente de operador de pilha do Azure. Instale o PowerShell em um computador local que pode acessar o ponto de extremidade do Gerenciador de recursos (administrador) (https://adminmanagement. [ região]. [External_FQDN]).

2. Execute os comandos a seguir para se conectar ao ambiente de pilha do Azure como um operador de pilha do Azure:

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. Altere o diretório onde você instalou o [ferramentas do Azure pilha](https://github.com/Azure/AzureStack-Tools) como parte da instalação do PowerShell, por exemplo, c:\azurestack-tools-master. Em seguida, altere para o diretório de infraestrutura e execute o seguinte comando para importar o módulo de infraestrutura:

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. Use os comandos, como os exemplos a seguir para trabalhar com alertas:
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>Use a API REST para monitorar a integridade e alertas

Você pode usar as chamadas da API REST para receber alertas, feche os alertas e obter a integridade de provedores de recursos.

### <a name="get-alert"></a>Obter um alerta

**Solicitação**

A solicitação obtém todos os alertas ativos e fechados para a assinatura do provedor padrão. Não há nenhum corpo de solicitação.


|Método  |URI da solicitação  |
|---------|---------|
|GET     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01"      |
|     |         |

**Argumentos**

|Argumento  |DESCRIÇÃO  |
|---------|---------|
|armendpoint     |  O Gerenciador de recursos do Azure ponto de extremidade de seu ambiente de pilha do Azure, na https://adminmanagement formato. {RegionName}. {FQDN externo}. Por exemplo, se o FQDN externo *azurestack.external* e o nome da região é *local*, em seguida, o ponto de extremidade do Gerenciador de recursos é https://adminmanagement.local.azurestack.external.       |
|subid     |   ID da assinatura do usuário que está fazendo a chamada. Você pode usar essa API para consultar somente com um usuário que tem permissão para a assinatura do provedor padrão.      |
|RegionName     |    O nome da região da implantação da pilha do Azure.     |
|api-version     |  Versão do protocolo usado para fazer essa solicitação. Você deve usar 2016-05-01.      |
|     |         |

**Resposta**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**Detalhes da resposta**


|  Argumento  |DESCRIÇÃO  |
|---------|---------|
|*ID*     |      ID exclusiva do alerta.   |
|*name*     |     Nome interno do alerta.   |
|*tipo*     |     Definição de recurso.    |
|*local*     |       Nome da região.     |
|*marcas*     |   Marcas de recurso.     |
|*closedtimestamp*    |  Hora UTC quando o alerta foi fechado.    |
|*createdtimestamp*     |     Hora UTC em que o alerta foi criado.   |
|*description*     |    Descrição do alerta.     |
|*faultid*     | Componente afetado.        |
|*alertid*     |  ID exclusiva do alerta.       |
|*faulttypeid*     |  Tipo exclusivo do componente com falha.       |
|*lastupdatedtimestamp*     |   Hora UTC quando informações de alerta foi atualizadas.    |
|*healthstate*     | Status de integridade geral.        |
|*name*     |   Nome do alerta específico.      |
|*fabricname*     |    Nome de malha registrado do componente com defeito.   |
|*description*     |  Descrição do componente de malha registrado.   |
|*servicetype*     |   Tipo do serviço de malha registrado.   |
|*correção*     |   Etapas de solução recomendada.    |
|*tipo*     |   Tipo de alerta.    |
|*resourceRegistrationid*    |     ID do recurso afetado registrado.    |
|*resourceProviderRegistrationID*   |    ID do provedor de recursos registrados do componente afetado.  |
|*serviceregistrationid*     |    ID do serviço registrado.   |
|*severity*     |     Severidade do alerta.  |
|*state*     |    Estado de alerta.   |
|*title*     |    Título do alerta.   |
|*impactedresourceid*     |     ID do recurso afetado.    |
|*ImpactedresourceDisplayName*     |     Nome do recurso afetado.  |
|*closedByUserAlias*     |   Usuário que fechou o alerta.      |

### <a name="close-alert"></a>Fechar alerta

**Solicitação**

A solicitação fecha um alerta por sua ID exclusiva.

|Método    |URI da solicitação  |
|---------|---------|
|PUT     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01"    |

**Argumentos**


|Argumento  |DESCRIÇÃO  |
|---------|---------|
|*armendpoint*     |   Ponto de extremidade do Gerenciador de recursos de seu ambiente de pilha do Azure, na https://adminmanagement formato. {RegionName}. {FQDN externo}. Por exemplo, se o FQDN externo *azurestack.external* e o nome da região é *local*, em seguida, o ponto de extremidade do Gerenciador de recursos é https://adminmanagement.local.azurestack.external.      |
|*subid*     |    ID da assinatura do usuário que está fazendo a chamada. Você pode usar essa API para consultar somente com um usuário que tem permissão para a assinatura do provedor padrão.     |
|*RegionName*     |   O nome da região da implantação da pilha do Azure.      |
|*api-version*     |    Versão do protocolo usado para fazer essa solicitação. Você deve usar 2016-05-01.     |
|*alertid*     |    ID exclusiva do alerta.     |

**Corpo**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**Resposta**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**Detalhes da resposta**


|  Argumento  |DESCRIÇÃO  |
|---------|---------|
|*ID*     |      ID exclusiva do alerta.   |
|*name*     |     Nome interno do alerta.   |
|*tipo*     |     Definição de recurso.    |
|*local*     |       Nome da região.     |
|*marcas*     |   Marcas de recurso.     |
|*closedtimestamp*    |  Hora UTC quando o alerta foi fechado.    |
|*createdtimestamp*     |     Hora UTC em que o alerta foi criado.   |
|*Descrição*     |    Descrição do alerta.     |
|*faultid*     | Componente afetado.        |
|*alertid*     |  ID exclusiva do alerta.       |
|*faulttypeid*     |  Tipo exclusivo do componente com falha.       |
|*lastupdatedtimestamp*     |   Hora UTC quando informações de alerta foi atualizadas.    |
|*healthstate*     | Status de integridade geral.        |
|*name*     |   Nome do alerta específico.      |
|*fabricname*     |    Nome de malha registrado do componente com defeito.   |
|*description*     |  Descrição do componente de malha registrado.   |
|*servicetype*     |   Tipo do serviço de malha registrado.   |
|*correção*     |   Etapas de solução recomendada.    |
|*tipo*     |   Tipo de alerta.    |
|*resourceRegistrationid*    |     ID do recurso afetado registrado.    |
|*resourceProviderRegistrationID*   |    ID do provedor de recursos registrados do componente afetado.  |
|*serviceregistrationid*     |    ID do serviço registrado.   |
|*severity*     |     Severidade do alerta.  |
|*state*     |    Estado de alerta.   |
|*title*     |    Título do alerta.   |
|*impactedresourceid*     |     ID do recurso afetado.    |
|*ImpactedresourceDisplayName*     |     Nome do recurso afetado.  |
|*closedByUserAlias*     |   Usuário que fechou o alerta.      |

### <a name="get-resource-provider-health"></a>Obter integridade do provedor de recursos

**Solicitação**

A solicitação obtém status de integridade para todos os provedores de recursos registrados.


|Método  |URI da solicitação  |
|---------|---------|
|GET    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01"   |


**Argumentos**


|Argumentos  |DESCRIÇÃO  |
|---------|---------|
|*armendpoint*     |    O ponto de extremidade do Gerenciador de recursos de seu ambiente de pilha do Azure, na https://adminmanagement formato. {RegionName}. {FQDN externo}. Por exemplo, se o FQDN externo é azurestack.external e o nome da região é local, o ponto de extremidade do Gerenciador de recursos é https://adminmanagement.local.azurestack.external.     |
|*subid*     |     ID da assinatura do usuário que está fazendo a chamada. Você pode usar essa API para consultar somente com um usuário que tem permissão para a assinatura do provedor padrão.    |
|*RegionName*     |     O nome da região da implantação da pilha do Azure.    |
|*api-version*     |   Versão do protocolo usado para fazer essa solicitação. Você deve usar 2016-05-01.      |


**Resposta**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**Detalhes da resposta**


|Argumento  |DESCRIÇÃO  |
|---------|---------|
|*Id*     |   ID exclusiva do alerta.      |
|*name*     |  Nome interno do alerta.       |
|*tipo*     |  Definição de recurso.       |
|*local*     |  Nome da região.       |
|*marcas*     |     Marcas de recurso.    |
|*registrationId*     |   Registro exclusivo para o provedor de recursos.      |
|*displayName*     |Nome de exibição do provedor de recursos.        |
|*namespace*     |   Implementa o namespace da API do provedor de recursos.       |
|*routePrefix*     |    URI para interagir com o provedor de recursos.     |
|*serviceLocation*     |   Este provedor de recursos está registrado com região.      |
|*infraURI*     |   URI do provedor de recursos listado como uma função de infraestrutura.      |
|*alertSummary*     |   Resumo do alerta de aviso e crítico associado com o provedor de recursos.      |
|*healthState*     |    Estado de integridade do provedor de recursos.     |


### <a name="get-resource-health"></a>Obter integridade do recurso

A solicitação obtém status de integridade para um provedor de recursos registrado específico.

**Solicitação**

|Método  |URI da solicitação  |
|---------|---------|
|GET     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01"    |

**Argumentos**

|Argumentos  |DESCRIÇÃO  |
|---------|---------|
|*armendpoint*     |    O ponto de extremidade do Gerenciador de recursos de seu ambiente de pilha do Azure, na https://adminmanagement formato. {RegionName}. {FQDN externo}. Por exemplo, se o FQDN externo é azurestack.external e o nome da região é local, o ponto de extremidade do Gerenciador de recursos é https://adminmanagement.local.azurestack.external.     |
|*subid*     |ID da assinatura do usuário que está fazendo a chamada. Você pode usar essa API para consultar somente com um usuário que tem permissão para a assinatura do provedor padrão.         |
|*RegionName*     |  O nome da região da implantação da pilha do Azure.       |
|*api-version*     |  Versão do protocolo usado para fazer essa solicitação. Você deve usar 2016-05-01.       |
|*RegistrationID* |ID de registro para um provedor de recursos específicos. |

**Resposta**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**Detalhes da resposta**

|Argumento  |DESCRIÇÃO  |
|---------|---------|
|*Id*     |   ID exclusiva do alerta.      |
|*name*     |  Nome interno do alerta.       |
|*tipo*     |  Definição de recurso.       |
|*local*     |  Nome da região.       |
|*marcas*     |     Marcas de recurso.    |
|*registrationId*     |   Registro exclusivo para o provedor de recursos.      |
|*resourceType*     |Tipo de recurso.        |
|*resourceName*     |   Nome do recurso.   |
|*usageMetrics*     |    Métrica de uso de recurso.     |
|*resourceLocation*     |   Nome da região onde implantado.      |
|*resourceURI*     |   URI do recurso.   |
|*alertSummary*     |   Resumo de críticas e alertas de aviso, status de integridade.     |

## <a name="learn-more"></a>Saiba mais

Para obter informações sobre monitoramento de integridade interna, consulte [monitorar a integridade e alertas na pilha do Azure](azure-stack-monitor-health.md).


## <a name="next-steps"></a>Próximas etapas

[Integração de segurança](azure-stack-integrate-security.md)