---
title: Visão geral das marcas de serviço do Firewall do Azure
description: Este artigo é uma visão geral das marcas de serviço do Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/21/2019
ms.author: victorh
ms.openlocfilehash: 9ecc5d3779ac6632f4a5c05914cbb0e756c79e91
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56458213"
---
# <a name="azure-firewall-service-tags"></a>Marcas de serviço do Firewall do Azure

Uma marca de serviço representa um grupo de prefixos de endereço IP para ajudar a minimizar a complexidade da criação de regra de segurança. Você não pode criar sua própria marca de serviço ou especificar quais endereços IP estão incluídos em uma marca. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

As marcas de serviço de Firewall do Azure podem ser usadas no campo de destino de regras de rede. Você pode usá-los no lugar de endereços IP específicos.

> [!NOTE]
> As marcas de serviço estão sendo adicionadas às regiões de forma incremental e estarão disponíveis em todas as regiões em breve.

## <a name="supported-service-tags"></a>Marcas de serviço com suporte

As marcas de serviço a seguir estão disponíveis para uso nas regras de rede do Firewall do Azure:

* **AzureCloud** (somente Resource Manager): Essa tag denota o espaço de endereços IP para o Azure, incluindo todos os [endereços IP públicos do datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Se você especificar *AzureCloud* para o valor, o tráfego será permitido ou negado para endereços IP públicos do Azure. Se só deseja permitir o acesso ao AzureCloud em uma [região](https://azure.microsoft.com/regions) específica, é possível especificar a região. Por exemplo, se você quiser permitir o acesso somente para o Azure AzureCloud na região Leste dos EUA, poderá especificar *AzureCloud.EastUS* como uma marca de serviço. 
* **AzureTrafficManager** (somente Resource Manager): Essa tag denota o espaço de endereços IP para os endereços IP de investigação do Gerenciador de Tráfego do Azure. Saiba mais sobre os endereços IPs de investigação no Gerenciador de Tráfego nas [Perguntas frequentes sobre o Gerenciador de Tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). 
* **Storage** (somente Resource Manager): Essa tag denota o espaço de endereços IP para o serviço Armazenamento do Azure. Se você especificar *Armazenamento* como valor, o tráfego será permitido ou negado para o armazenamento. Se deseja permitir o acesso ao armazenamento em uma determinada [região](https://azure.microsoft.com/regions), você pode especificar a região. Por exemplo, se você quiser permitir o acesso somente para o Armazenamento do Azure na região Leste dos EUA, poderá especificar *Storage.EastUS* como uma marca de serviço. A marca representa o serviço, mas não as instâncias específicas do serviço. Por exemplo, a marca representa o serviço Armazenamento do Azure, mas não uma conta do Armazenamento do Azure específica.
* **SQL** (somente Resource Manager): Essa tag indica os prefixos de endereço dos serviços Banco de Dados SQL do Azure e SQL Data Warehouse do Azure. Se você especificar *Sql* como valor, o tráfego será permitido ou negado para o Sql. Se só deseja permitir o acesso ao Sql em uma [região](https://azure.microsoft.com/regions) específica, é possível especificar a região. Por exemplo, se você quiser permitir o acesso somente para o Banco de Dados SQL do Azure na região Leste dos EUA, poderá especificar *Sql.EastUS* como uma marca de serviço. A marca representa o serviço, mas não as instâncias específicas do serviço. Por exemplo, a marca representa o serviço Banco de Dados SQL do Azure, mas não um banco de dados ou servidor SQL específico.
* **AzureCosmosDB** (somente Resource Manager): Essa tag indica os prefixos de endereço dos serviços do Azure Cosmos DB. Se você especificar *AzureCosmosDB* para o valor, o tráfego será permitido ou negado para o AzureCosmosDB. Se quiser somente permitir o acesso ao AzureCosmosDB em uma determinada [região](https://azure.microsoft.com/regions), especifique a região usando o formato AzureCosmosDB.[nome da região].
* **AzureKeyVault** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço do Azure Key Vault. Se você especificar *AzureKeyVault* para o valor, o tráfego será permitido ou negado para o AzureKeyVault. Se quiser somente permitir o acesso ao AzureKeyVault em uma determinada [região](https://azure.microsoft.com/regions), especifique a região usando o formato AzureKeyVault.[nome da região].
* **EventHub** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço do Hub de Eventos do Azure. Se você especificar *EventHub* para o valor, o tráfego será permitido ou negado para o EventHub. Se quiser somente permitir o acesso ao EventHub em uma determinada [região](https://azure.microsoft.com/regions), especifique a região usando o formato EventHub.[nome da região]. 
* **ServiceBus** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço do Barramento de Serviço do Azure. Se você especificar *ServiceBus* para o valor, o tráfego será permitido ou negado para o ServiceBus. Se quiser somente permitir o acesso ao ServiceBus em uma determinada [região](https://azure.microsoft.com/regions), especifique a região usando o formato ServiceBus.[nome da região].
* **MicrosoftContainerRegistry** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço do Registro de Contêiner da Microsoft. Se você especificar *MicrosoftContainerRegistry* para o valor, o tráfego será permitido ou negado para MicrosoftContainerRegistry. Se quiser somente permitir o acesso ao MicrosoftContainerRegistry em uma determinada [região](https://azure.microsoft.com/regions), poderá especificar a região usando o formato MicrosoftContainerRegistry.[nome da região].
* **AzureContainerRegistry** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço do Registro de Contêiner do Azure. Se você especificar *AzureContainerRegistry* para o valor, o tráfego será permitido ou negado para AzureContainerRegistry. Se quiser somente permitir o acesso ao AzureContainerRegistry em uma determinada [região](https://azure.microsoft.com/regions), poderá especificar a região usando o formato AzureContainerRegistry.[nome da região]. 
* **AppService** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço AppService do Azure. Se você especificar *AppService* para o valor, o tráfego será permitido ou negado para o AppService. Se quiser somente permitir o acesso ao AppService em uma determinada [região](https://azure.microsoft.com/regions), especifique a região usando o formato AppService.[nome da região]. 
* **AppServiceManagement** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço de Gerenciamento do Serviço de Aplicativo do Azure. Se você especificar *AppServiceManagement* para o valor, o tráfego será permitido ou negado para AppServiceManagement. 
* **ApiManagement** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço de Gerenciamento de API do Azure. Se você especificar *ApiManagement* para o valor, o tráfego será permitido ou negado para ApiManagement.  
* **AzureConnectors** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço de Conectores do Azure. Se você especificar *AzureConnectors* para o valor, o tráfego será permitido ou negado para o AzureConnectors. Se quiser somente permitir o acesso ao AzureConnectors em uma determinada [região](https://azure.microsoft.com/regions), especifique a região usando o formato AzureConnectors.[nome da região].
* **AzureDataLake** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço do Azure Data Lake. Se você especificar *AzureDataLake* para o valor, o tráfego será permitido ou negado para AzureDataLake.
* **AzureActiveDirectory** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço do AzureActiveDirectory. Se você especificar *AzureActiveDirectory* para o valor, o tráfego será permitido ou negado para AzureActiveDirectory.
* **AzureMonitor** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço do AzureMonitor. Se você especificar *AzureMonitor* para o valor, o tráfego será permitido ou negado para o AzureMonitor.
* **ServiceFabric** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço ServiceFabric. Se você especificar *ServiceFabric* para o valor, o tráfego será permitido ou negado para o ServiceFabric.
* **AzureMachineLearning** (somente Resource Manager): Essa tag indica os prefixos de endereço do serviço AzureMachineLearning. Se você especificar *AzureMachineLearning* para o valor, o tráfego será permitido ou negado para o AzureMachineLearning.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as regras do Firewall do Azure, confira [Lógica de processamento de regra do Firewall do Azure](rule-processing.md).