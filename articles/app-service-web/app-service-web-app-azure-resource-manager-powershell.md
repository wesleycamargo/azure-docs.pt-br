---
title: Comandos do PowerShell baseados no Azure Resource Manager para aplicativos Web do Azure | Microsoft Docs
description: Saiba como usar os novos comandos do PowerShell baseados no Azure Resource Manager para gerenciar seus aplicativos Web do Azure.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: aelnably

---
# Usando o PowerShell baseado no Azure Resource Manager para gerenciar aplicativos Web do Azure
Com o lançamento do Microsoft Azure PowerShell versão 1.0.0 foram adicionados novos comandos que concedem ao usuário a capacidade de usar comandos do PowerShell baseados no Azure Resource Manager para gerenciar aplicativos Web.

Para saber mais sobre como gerenciar grupos de recursos, consulte [Usando o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

Para saber mais sobre a lista completa de parâmetros e opções para os cmdlets do PowerShell do Azure Resource Manager de aplicativo Web, consulte a [Referência de cmdlet completa dos cmdlets do PowerShell baseados no Azure Resource Manager de aplicativo Web](https://msdn.microsoft.com/library/mt619237.aspx)

## Gerenciando Planos do Serviço de Aplicativo
### Criar um Plano do Serviço de Aplicativo
Para criar um novo Plano do Serviço de Aplicativo, use o cmdlet **AzureRmAppServicePlan novo**.

A seguir estão as descrições dos diferentes parâmetros:

* **Name**: o nome do Plano do Serviço de Aplicativo.
* **Local**: a localização do plano do serviço.
* **ResourceGroupName**: grupo de recursos que inclui o Plano do Serviço de Aplicativo recém-criado.
* **Camada**: o tipo de preço desejado (o padrão é Gratuito, outras opções são Compartilhado, Básico, Standard e Premium).
* **WorkerSize**: o tamanho de trabalhadores (o padrão é pequeno se o parâmetro de tipo foi especificado como Básico, Standard ou Premium. Outras opções são Médio e Grande.)
* **NumberofWorkers**: o número de funções de trabalho no Plano do Serviço de Aplicativo ( o valor padrão é 1). 

Exemplo para usar este cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### Criar um Plano do Serviço de Aplicativo em um Ambiente de Serviço de Aplicativo
Para criar um novo plano do serviço de aplicativo em um ASE (ambiente do serviço de aplicativo), o mesmo comando **New-AzureRmAppServicePlan** pode ser usado com parâmetros extra para especificar o nome do ASE e o nome do grupo de recursos ao qual o ASE pertence.

Exemplo para usar este cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Para saber mais sobre o ambiente do serviço de aplicativo, consulte [Introdução ao Ambiente do Serviço de Aplicativo](app-service-app-service-environment-intro.md)

### Listar os Planos do Serviço de Aplicativo existentes
Para listar os planos do serviço de aplicativo existentes, use o cmdlet **Get-AzureRmAppServicePlan**.

Para listar todos os planos do serviço de aplicativo na sua assinatura, use:

    Get-AzureRmAppServicePlan

Para listar todos os Planos de Serviço de Aplicativo em um grupo de recursos específico, use:

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

Para obter um Plano do Serviço de Aplicativo, use:

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### Configurar um Plano do Serviço de Aplicativo existente
Para alterar as configurações para um plano do serviço de aplicativo existente, use o cmdlet **Set-AzureRmAppServicePlan**. Você pode alterar o tipo, o tamanho das funções de trabalho e o número de funções de trabalho

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### Dimensionando um Plano do Serviço de Aplicativo
Para ajustar a escala de um Plano do Serviço de Aplicativo existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### Alterando o tamanho do trabalho de um Plano do Serviço de Aplicativo
Para alterar o tamanho das funções de trabalho em um Plano do Serviço de Aplicativo existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### Alterando o tipo de um Plano do Serviço de Aplicativo
Para alterar o tipo de um Plano do Serviço de Aplicativo existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### Excluir um Plano do Serviço de Aplicativo existente
Para excluir um plano do serviço de aplicativo existente, todos os aplicativos Web atribuídos precisam ser movidos ou excluídos primeiro e, em seguida, usando o cmdlet **Remove-AzureRmAppServicePlan**, você pode excluir o plano do serviço de aplicativo.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## Gerenciando Aplicativos Web do Serviço de Aplicativo
### Criar um novo aplicativo Web
Para criar um novo aplicativo Web, use o cmdlet **New-AzureRmWebApp**.

A seguir estão as descrições dos diferentes parâmetros:

* **Name**: o nome do aplicativo Web.
* **AppServicePlan**: o nome do plano de serviço usado para hospedar o aplicativo Web.
* **ResourceGroupName**: o grupo de recursos que hospeda o plano do serviço de Aplicativo.
* **Location**: a localização do aplicativo Web.

Exemplo para usar este cmdlet:

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### Criar um novo aplicativo Web em um ambiente de Serviço de Aplicativo
Para criar um novo aplicativo Web em um ASE (Ambiente do Serviço de Aplicativo), o mesmo comando **New-AzureRmWebApp** pode ser usado com parâmetros extra para especificar o nome do ASE e o nome do grupo de recursos ao qual o ASE pertence.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Para saber mais sobre o ambiente do serviço de aplicativo, consulte [Introdução ao Ambiente do Serviço de Aplicativo](app-service-app-service-environment-intro.md)

### Excluir um aplicativo Web existente
Para excluir um aplicativo Web existente, você pode usar o cmdlet **Remove-AzureRmWebApp**, você precisa especificar o nome do aplicativo Web e o nome do grupo de recursos.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### Listar os aplicativos Web
Para listar os aplicativos Web existentes, use o cmdlet **Get-AzureRmWebApp**.

Para listar todos os aplicativos Web em sua assinatura, use:

    Get-AzureRmWebApp

Para listar todos os aplicativos Web em um grupo de recursos específico, use:

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

Para obter um aplicativo Web específico, use:

    Get-AzureRmWebApp -Name ContosoWebApp

### Configurar um aplicativo Web existente
Para alterar as definições e configurações para um aplicativo Web existente, use o cmdlet **Set-AzureRmWebApp**. Para obter uma lista completa de parâmetros, consulte o [link de referência de Cmdlet](https://msdn.microsoft.com/library/mt652487.aspx)

Exemplo (1): use este cmdlet para alterar cadeias de conexão

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Exemplo (2): adicione o exemplo para configurações de aplicativo

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Exemplo (3): defina o aplicativo Web para executar no modo de 64 bits

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### Alterar o estado de um aplicativo Web existente
#### Reiniciar um aplicativo Web
Para reiniciar um aplicativo Web, você deve especificar o nome e grupo de recursos do aplicativo Web.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### Parar um aplicativo Web
Para parar um aplicativo Web, você deve especificar o nome e grupo de recursos do aplicativo Web.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### Iniciar um aplicativo Web
Para iniciar um aplicativo Web, você deve especificar o nome e grupo de recursos do aplicativo Web.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### Gerenciar perfis de publicação de aplicativos Web
Cada aplicativo Web tem um perfil de publicação que pode ser usado para publicar seus aplicativos, várias operações podem ser executadas em perfis de publicação.

#### Obter o perfil de publicação
Para obter o perfil de publicação para um aplicativo Web, use:

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Observe que isso retornará o perfil de publicação para a linha de comando, bem como gerará a saída do perfil de publicação em um arquivo de texto.

#### Redefinir o perfil de publicação
Para redefinir a senha de publicação do FTP e a implantação da Web de um aplicativo Web, use:

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### Gerenciar certificados de aplicativo Web
Para saber mais sobre como gerenciar certificados de aplicativo Web, consulte [Associação de certificados SSL usando o PowerShell](app-service-web-app-powershell-ssl-binding.md)

### Próximas etapas
* Para saber sobre o suporte do Azure Resource Manager ao PowerShell, consulte [Usando o Azure PowerShell com o Azure Resource Manager.](../powershell-azure-resource-manager.md)
* Para saber mais sobre os Ambientes do Serviço de Aplicativo, consulte [Introdução ao Ambiente do Serviço de Aplicativo.](app-service-app-service-environment-intro.md)
* Para saber mais sobre como gerenciar certificados SSL do Serviço de Aplicativo usando o PowerShell, consulte [Associação de certificados SSL usando o PowerShell.](app-service-web-app-powershell-ssl-binding.md)
* Para saber mais sobre a lista completa de cmdlets PowerShell baseados no Azure Resource Manager para aplicativos Web do Azure, consulte [Referência de Cmdlets do Azure dos Cmdlets do PowerShell do Azure Resource Manager de Aplicativos Web.](https://msdn.microsoft.com/library/mt619237.aspx)

<!---HONumber=AcomDC_0615_2016-->