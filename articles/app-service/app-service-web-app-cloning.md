---
title: Clonagem de Aplicativo Web usando o PowerShell
description: Saiba como clonar seus Aplicativos Web em novos Aplicativos Web usando o Azure PowerShell.
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.openlocfilehash: 30817a1a6a8079e7a896305ab0b59e48fad4d644
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Clonagem de aplicativo do Serviço de Aplicativo do Azure usando o Azure PowerShell
Com o lançamento do Microsoft Azure PowerShell versão 1.1.0, uma nova opção foi adicionada ao `New-AzureRMWebApp` que lhe permite clonar um aplicativo Web existente como um aplicativo recém-criado em uma região diferente ou na mesma região. Essa opção permitirá que os clientes implantem vários aplicativos em diferentes regiões de forma rápida e fácil.

A clonagem de aplicativo atualmente só tem suporte para planos de serviço de aplicativos de camada Premium. O novo recurso usa as mesmas limitações que o recurso de Backup dos Aplicativos Web; veja [Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Clonagem de um aplicativo existente
Cenário: um aplicativo Web existente na região Centro-Sul dos EUA, você deseja clonar o conteúdo como um novo aplicativo Web na região Centro-Norte dos EUA. Isso pode ser feito usando a versão do cmdlet do PowerShell do Azure Resource Manager para criar um novo aplicativo Web com a opção `-SourceWebApp`.

Se soubermos o nome do grupo de recursos que contém o aplicativo Web de origem, poderemos usar o seguinte comando do PowerShell para obter as informações do aplicativo Web de origem (nesse caso, o nome é `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Para criar um novo plano do Serviço de Aplicativo, podemos usar comando `New-AzureRmAppServicePlan` como no exemplo a seguir

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Usando o comando `New-AzureRmWebApp`, você pode criar o novo aplicativo Web na região Centro-Norte dos EUA e associá-lo a uma plano de serviço de aplicativo de camada premium existente. Além disso, você pode usar o mesmo grupo de recursos do aplicativo Web de origem ou definir um novo grupo de recursos, como mostrado no comando a seguir:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Para clonar um aplicativo Web existente, incluindo todos os slots de implantação associados, você precisa usar o parâmetro `IncludeSourceWebAppSlots`. O comando do PowerShell a seguir demonstra o uso desse parâmetro com o comando `New-AzureRmWebApp`:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Para clonar um aplicativo Web existente na mesma região, você precisa criar um novo grupo de recursos e um novo plano do serviço de aplicativo na mesma região e usar o comando do PowerShell a seguir para clonar o aplicativo Web

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonagem de um aplicativo existente como um Ambiente do Serviço de Aplicativo
Cenário: um aplicativo Web existente na região Centro-Sul dos EUA, e você desejaria clonar o conteúdo como um novo aplicativo Web em um ASE (Ambiente de Serviço de Aplicativo) existente.

Se soubermos o nome do grupo de recursos que contém o aplicativo Web de origem, poderemos usar o seguinte comando do PowerShell para obter as informações do aplicativo Web de origem (nesse caso, o nome é `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Sabendo o nome do ASE e o nome do grupo de recursos ao qual o ASE pertence, você pode criar o novo aplicativo Web no ASE existente, conforme mostrado no comando a seguir:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

O parâmetro `Location` é necessário devido ao legado, mas é ignorado quando você criar o aplicativo em um ASE. 

## <a name="cloning-an-existing-app-slot"></a>Clonagem de um slot de aplicativo existente
Cenário: você deseja clonar um slot do aplicativo Web existente como a um novo aplicativo Web ou um novo slot de aplicativo Web. O novo aplicativo Web pode estar na mesma região que o slot original do aplicativo Web ou em uma região diferente.

Se soubermos o nome do grupo de recursos que contém o aplicativo Web de origem, você pode usar o seguinte comando do PowerShell para obter as informações de slot do aplicativo Web de origem (nesse caso, o nome é `source-webappslot`) associado ao aplicativo Web `source-webapp`:

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot
```

O comando a seguir demonstra como criar um clone do aplicativo Web de origem como um novo aplicativo Web:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Configuração do Gerenciador de Tráfego durante a clonagem de um aplicativo
Criar aplicativos web de várias regiões e configurar o Azure Traffic Manager para rotear o tráfego para todos esses aplicativos Web é um cenário importante para garantir que os aplicativos de clientes sejam altamente disponíveis. Ao clonar um aplicativo Web existente, você tem a opção de conectar ambos os aplicativos Web a um perfil novo ou existente do gerenciador de tráfego. Somente a versão do Azure Resource Manager de gerenciador de tráfego recebe suporte.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Criando um novo perfil do Gerenciador de Tráfego durante a clonagem de um aplicativo
Cenário: você deseja clonar um aplicativo Web em outra região enquanto configura um perfil do gerenciador de tráfego do Azure Resource Manager que inclui ambos os aplicativos Web. O comando a seguir demonstra como criar um clone do aplicativo Web de origem como um novo aplicativo Web ao configurar um novo perfil do Gerenciador de Tráfego:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Adicionando novos aplicativos Web clonados a um perfil existente do Gerenciador de Tráfego
Cenário: você já tem um perfil do gerenciador de tráfego do Azure Resource Manager ao qual deseje adicionar ambos os aplicativos Web como pontos de extremidade. Para fazer isso, primeiro é necessário montar o ID existente de perfil do gerenciador de tráfego. É necessário a ID de assinatura, o nome do grupo de recursos e o nome de perfil do gerenciador de tráfego existente.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Depois de obter a ID do gerenciador de tráfego, o comando a seguir demonstra como criar um clone do aplicativo Web de origem como um novo aplicativo Web enquanto eles são adicionados a um perfil existente do Gerenciador de Tráfego:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Restrições atuais
Este recurso está atualmente em versão prévia e novos recursos são adicionados ao longo do tempo. Aqui estão as restrições conhecidas sobre a versão atual da clonagem de apicativos:

* As configurações de escala automática não são clonadas
* As configurações de agendamento de backup não são clonadas.
* As configurações da rede virtual não são clonadas
* O Application Insights não está automaticamente configurado no aplicativo Web de destino
* As configurações de Autenticação Fácil não são clonadas
* A extensão Kudu não é clonada
* As regras de TiP não são clonadas
* O conteúdo do banco de dados não é clonado
* Os endereços IP de saída são alterados em caso de clonagem para uma unidade de escala diferente

### <a name="references"></a>Referências
* [Clonagem de aplicativo Web](app-service-web-app-cloning.md)
* [Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-backup.md)
* [Suporte do Azure Resource Manager para Visualização do Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introdução ao ambiente de Serviço de Aplicativo](environment/intro.md)
* [Usando o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)

