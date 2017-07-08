---
title: Comandos do PowerShell baseados no Azure Resource Manager para aplicativos Web do Azure | Microsoft Docs
description: Saiba como usar os novos comandos do PowerShell baseados no Azure Resource Manager para gerenciar seus aplicativos Web do Azure.
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 4231fbba-61e5-4f92-b958-531c066fb87f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8d574f051a327ba0409e6f25a5886af673d3d5e0
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017


---
# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>Usando o PowerShell baseado no Azure Resource Manager para gerenciar aplicativos Web do Azure
> [!div class="op_single_selector"]
> * [CLI do Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [PowerShell do Azure](app-service-web-app-azure-resource-manager-powershell.md)
> 
> 

Com o Microsoft Azure PowerShell versão 1.0.0 foram adicionados novos comandos que concedem ao usuário a capacidade de usar comandos do PowerShell baseados no Azure Resource Manager para gerenciar aplicativos Web.

Para saber mais sobre como gerenciar grupos de recursos, consulte [Usando o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md). 

Para saber mais sobre a lista completa de parâmetros e opções para os cmdlets do PowerShell, veja a [Referência de cmdlet completa dos cmdlets do PowerShell baseados no Azure Resource Manager de aplicativo Web](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>Gerenciando Planos do Serviço de Aplicativo
### <a name="create-an-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo
Para criar um plano do serviço de aplicativo, use o cmdlet **AzureRmAppServicePlan novo** .

A seguir estão as descrições dos diferentes parâmetros:

* **Name**: o nome do Plano do Serviço de Aplicativo.
* **Local**: a localização do plano do serviço.
* **ResourceGroupName**: grupo de recursos que inclui o Plano do Serviço de Aplicativo recém-criado.
* **Camada**: o tipo de preço desejado (o padrão é Gratuito, outras opções são Compartilhado, Básico, Standard e Premium).
* **WorkerSize**: o tamanho de trabalhadores (o padrão é pequeno se o parâmetro de tipo foi especificado como Básico, Standard ou Premium. Outras opções são Médio e Grande.)
* **NumberofWorkers**: o número de funções de trabalho no Plano do Serviço de Aplicativo ( o valor padrão é 1). 

Exemplo para usar este cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>Criar um Plano do Serviço de Aplicativo em um Ambiente de Serviço de Aplicativo
Para criar um plano do serviço de aplicativo em um ASE (ambiente do serviço de aplicativo), use o mesmo comando **New-AzureRmAppServicePlan** com parâmetros extra para especificar o nome do ASE e o nome do grupo de recursos.

Exemplo para usar este cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Para saber mais sobre o ambiente do serviço de aplicativo, consulte [Introdução ao Ambiente do Serviço de Aplicativo](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>Listar os Planos do Serviço de Aplicativo existentes
Para listar os planos do serviço de aplicativo existentes, use o cmdlet **Get-AzureRmAppServicePlan** .

Para listar todos os planos do serviço de aplicativo na sua assinatura, use: 

    Get-AzureRmAppServicePlan

Para listar todos os Planos de Serviço de Aplicativo em um grupo de recursos específico, use:

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

Para obter um Plano do Serviço de Aplicativo, use:

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>Configurar um Plano do Serviço de Aplicativo existente
Para alterar as configurações para um plano do serviço de aplicativo existente, use o cmdlet **Set-AzureRmAppServicePlan** . Você pode alterar o tipo, o tamanho das funções de trabalho e o número de funções de trabalho 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>Dimensionando um Plano do Serviço de Aplicativo
Para ajustar a escala de um Plano do Serviço de Aplicativo existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>Alterando o tamanho do trabalho de um Plano do Serviço de Aplicativo
Para alterar o tamanho das funções de trabalho em um Plano do Serviço de Aplicativo existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>Alterando o tipo de um Plano do Serviço de Aplicativo
Para alterar o tipo de um Plano do Serviço de Aplicativo existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>Excluir um Plano do Serviço de Aplicativo existente
Para excluir um plano do serviço de aplicativo existente, todos os aplicativos Web atribuídos precisam ser movidos ou excluídos primeiro. Em seguida, usando o cmdlet **Remove-AzureRmAppServicePlan**, você pode excluir o plano do serviço de aplicativo.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>Gerenciando Aplicativos Web do Serviço de Aplicativo
### <a name="create-a-web-app"></a>Criar um aplicativo Web
Para criar um aplicativo Web, use o cmdlet **New-AzureRmWebApp** .

A seguir estão as descrições dos diferentes parâmetros:

* **Name**: o nome do aplicativo Web.
* **AppServicePlan**: o nome do plano de serviço usado para hospedar o aplicativo Web.
* **ResourceGroupName**: o grupo de recursos que hospeda o plano do serviço de Aplicativo.
* **Location**: a localização do aplicativo Web.

Exemplo para usar este cmdlet:

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>Criar um aplicativo Web em um Ambiente de Serviço de Aplicativo
Para criar um aplicativo Web em um Ambiente do Serviço de Aplicativo (ASE). Use o mesmo comando **New-AzureRmWebApp** com parâmetros extras para especificar o nome do ASE e o nome do grupo de recursos ao qual o ASE pertence.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Para saber mais sobre o ambiente do serviço de aplicativo, consulte [Introdução ao Ambiente do Serviço de Aplicativo](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>Excluir um aplicativo Web existente
Para excluir um aplicativo Web existente, você pode usar o cmdlet **Remove-AzureRmWebApp** , você precisa especificar o nome do aplicativo Web e o nome do grupo de recursos.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>Listar os aplicativos Web
Para listar os aplicativos Web existentes, use o cmdlet **Get-AzureRmWebApp** .

Para listar todos os aplicativos Web em sua assinatura, use:

    Get-AzureRmWebApp

Para listar todos os aplicativos Web em um grupo de recursos específico, use:

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

Para obter um aplicativo Web específico, use:

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>Configurar um aplicativo Web existente
Para alterar as definições e configurações para um aplicativo Web existente, use o cmdlet **Set-AzureRmWebApp** . Para obter uma lista completa de parâmetros, consulte o [link de referência de Cmdlet](https://msdn.microsoft.com/library/mt652487.aspx)

Exemplo (1): use este cmdlet para alterar cadeias de conexão

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Exemplo (2): adicionar ou alterar as configurações do aplicativo

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Exemplo (3): defina o aplicativo Web para executar no modo de 64 bits

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>Alterar o estado de um aplicativo Web existente
#### <a name="restart-a-web-app"></a>Reiniciar um aplicativo Web
Para reiniciar um aplicativo Web, você deve especificar o nome e grupo de recursos do aplicativo Web.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Parar um aplicativo Web
Para parar um aplicativo Web, você deve especificar o nome e grupo de recursos do aplicativo Web.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Iniciar um aplicativo Web
Para iniciar um aplicativo Web, você deve especificar o nome e grupo de recursos do aplicativo Web.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gerenciar perfis de publicação de aplicativos Web
Cada aplicativo Web tem um perfil de publicação que pode ser usado para publicar seus aplicativos, várias operações podem ser executadas em perfis de publicação.

#### <a name="get-publishing-profile"></a>Obter o perfil de publicação
Para obter o perfil de publicação para um aplicativo Web, use:

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Esse comando retorna o perfil de publicação para a linha de comando, bem como gerará a saída do perfil de publicação em um arquivo de texto.

#### <a name="reset-publishing-profile"></a>Redefinir o perfil de publicação
Para redefinir a senha de publicação do FTP e a implantação da Web de um aplicativo Web, use:

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>Gerenciar certificados de aplicativo Web
Para saber mais sobre como gerenciar certificados de aplicativo Web, consulte [Associação de certificados SSL usando o PowerShell](app-service-web-app-powershell-ssl-binding.md)

### <a name="next-steps"></a>Próximas etapas
* Para saber sobre o suporte do Azure Resource Manager ao PowerShell, consulte [Usando o Azure PowerShell com o Azure Resource Manager.](../powershell-azure-resource-manager.md)
* Para saber mais sobre os Ambientes do Serviço de Aplicativo, consulte [Introdução ao Ambiente do Serviço de Aplicativo.](app-service-app-service-environment-intro.md)
* Para saber mais sobre como gerenciar certificados SSL do Serviço de Aplicativo usando o PowerShell, consulte [Associação de certificados SSL usando o PowerShell.](app-service-web-app-powershell-ssl-binding.md)
* Para saber mais sobre a lista completa de cmdlets PowerShell baseados no Azure Resource Manager para aplicativos Web do Azure, consulte [Referência de Cmdlets do Azure dos Cmdlets do PowerShell do Azure Resource Manager de Aplicativos Web.](https://msdn.microsoft.com/library/mt619237.aspx)
* * Para saber mais sobre o gerenciamento do Serviço de Aplicativo usando a CLI, veja [Using Azure Resource Manager-Based XPlat CLI para aplicativo Web do Azure (Usando a CLI XPlat baseada no Azure Resource Manager para o Aplicativo Web do Azure).](app-service-web-app-azure-resource-manager-xplat-cli.md)


