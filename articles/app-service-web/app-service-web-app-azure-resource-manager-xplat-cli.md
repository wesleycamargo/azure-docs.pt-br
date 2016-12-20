---
title: Ferramentas de linha de comando de plataforma cruzada com base no Azure Resource Manager para Aplicativos Web do Azure | Microsoft Docs
description: Saiba como usar as novas Ferramentas de linha de comando de plataforma cruzada com base no Azure Resource Manager para gerenciar Aplicativos Web do Azure.
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: d415b195-4262-416f-b59f-7e1aef200054
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 11a9e0a67b71e7ce4adf9713c825511ade4ff7d7
ms.openlocfilehash: 36a6863990e3c4cb87bc4f3a1207ab552c836c20


---
# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-web-app"></a>Como usar a CLI XPlat com base no Azure Resource Manager para aplicativos Web do Azure
> [!div class="op_single_selector"]
> * [CLI do Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [PowerShell do Azure](app-service-web-app-azure-resource-manager-powershell.md)
> 
> 

Com o lançamento da versão 0.10.5 das Ferramentas de Linha de Comando de plataforma cruzada do Microsoft Azure, foram adicionados novos comandos. Esses comandos permitem que o usuário utilize comandos do PowerShell baseados no Azure Resource Manager para gerenciar Aplicativos Web.

Para saber mais sobre como gerenciar Grupos de Recursos, veja [Usar a CLI do Azure para gerenciar recursos e grupos de recursos do Azure](../xplat-cli-azure-resource-manager.md). 

## <a name="managing-app-service-plans"></a>Gerenciando Planos do Serviço de Aplicativo
### <a name="create-an-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo
Para criar um plano do serviço de aplicativo, use o comando **azure appserviceplan create**.

A seguir estão as descrições dos diferentes parâmetros:

* **--resource-group**: grupo de recursos que inclui o plano do serviço de aplicativo recém-criado.
* **--name**: o nome do plano do serviço de aplicativo.
* **--location**: a localização do plano do serviço de aplicativo.
* **–tier**: o sku de preços desejado (as opções são: F1 (Gratuito). D1 (Compartilhado). B1 (Básico Breve), B2 (Básico Médio) e B3 (Básico Prolongado). S1 (Standard Breve), S2 (Standard Médio) e S3 (Standard Prolongado). P1 (Premium Breve), P2 (Premium Médio) e P3 (Premium Prolongado)).
* **--instances**: o número de trabalhos no plano do serviço de aplicativo (o valor padrão é 1).

Exemplo para usar este cmdlet:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

#### <a name="create-a-linux-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Linux

Usando o mesmo comando **azure appserviceplan create** com o parâmetro extra **--islinux true**. Observe as restrições e regiões descritas em [Introdução ao Serviço de Aplicativo no Linux](app-service-linux-intro.md)

### <a name="list-existing-app-service-plans"></a>Listar os Planos do Serviço de Aplicativo existentes
Para listar os planos do serviço de aplicativo existentes, use o comando **azure appserviceplan list**.

Para listar todos os Planos de Serviço de Aplicativo em um grupo de recursos específico, use:

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

Para obter um planos do serviço de aplicativo existente, use o comando **azure appserviceplan show**:

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>Configurar um Plano do Serviço de Aplicativo existente
Para alterar as configurações para um plano do serviço de aplicativo existente, use o comando **azure appserviceplan config**. Você pode alterar o sku e o número de trabalhos 

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>Dimensionando um Plano do Serviço de Aplicativo
Para ajustar a escala de um Plano do Serviço de Aplicativo existente, use:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>Como alterar o SKU de um Plano do Serviço de Aplicativo
Para alterar o SKU de um Plano do Serviço de Aplicativo existente, use:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>Excluir um Plano do Serviço de Aplicativo existente
Para excluir um plano do serviço de aplicativo existente, todos os aplicativos Web atribuídos precisam ser movidos ou excluídos primeiro. Em seguida, com o comando **azure webapp delete**, você pode excluir o plano do serviço de aplicativo.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-web-apps"></a>Gerenciando Aplicativos Web do Serviço de Aplicativo
### <a name="create-a-web-app"></a>Criar um aplicativo Web
Para criar um aplicativo Web, use o comando **azure webapp create**.

A seguir estão as descrições dos diferentes parâmetros:

* **--name**: o nome do aplicativo Web.
* **--plan**: o nome do plano do serviço usado para hospedar o aplicativo Web.
* **--resource-group**: o grupo de recursos que hospeda o plano do serviço de aplicativo.
* **--location**: a localização do aplicativo Web.

Exemplo para usar este cmdlet:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-web-app"></a>Excluir um aplicativo Web existente
Para excluir um aplicativo Web existente, use o comando **azure webapp delete**, você precisa especificar o nome do aplicativo Web e o nome do grupo de recursos.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-web-apps"></a>Listar os aplicativos Web
Para listar os aplicativos Web existentes, use o comando **azure webapp list**.

Para listar todos os aplicativos Web em um grupo de recursos específico, use:

    azure webapp list --resource-group ContosoAzureResourceGroup

Para obter um aplicativo Web específico, use o comando **azure webapp show**.

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-web-app"></a>Configurar um aplicativo Web existente
Para alterar as definições e configurações de um aplicativo Web existente, use o comando **azure webapp config set**.

Exemplo (1): alterar a versão php de um aplicativo Web 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

Exemplo (2): adicionar ou alterar as configurações do aplicativo

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

Para saber que outras configurações podem ser alteradas, use o comando **azure webapp config set -h**.

### <a name="change-the-state-of-an-existing-web-app"></a>Alterar o estado de um aplicativo Web existente
#### <a name="restart-a-web-app"></a>Reiniciar um aplicativo Web
Para reiniciar um aplicativo Web, você deve especificar o nome e grupo de recursos do aplicativo Web.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Parar um aplicativo Web
Para parar um aplicativo Web, você deve especificar o nome e grupo de recursos do aplicativo Web.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Iniciar um aplicativo Web
Para iniciar um aplicativo Web, você deve especificar o nome e grupo de recursos do aplicativo Web.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gerenciar perfis de publicação de aplicativos Web
Cada aplicativo Web tem um perfil de publicação que pode ser usado para publicar seus aplicativos.

#### <a name="get-publishing-profile"></a>Obter o perfil de publicação
Para obter o perfil de publicação para um aplicativo Web, use:

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

Esse comando ecoa o nome de perfil de publicação e a senha para a linha de comando.

### <a name="manage-web-app-hostnames"></a>Gerenciar nomes de host de aplicativo Web
Para gerenciar associações de nome de host para seu aplicativo Web, use o comando **azure webapp config hostnames**  

#### <a name="list-hostname-bindings"></a>Listar associações de nome de host
Para obter as associações de nome de host atuais de um aplicativo Web, use:

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>Adicionar associações de nome de host
Para adicionar associações de nome de host de um aplicativo Web, use:

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>Excluir associações de nome de host
Para excluir associações de nome de host, use:

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

### <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre o suporte da CLI do Azure Resource Manager, veja [Usar a CLI do Azure para gerenciar recursos e grupos de recursos do Azure](../xplat-cli-azure-resource-manager.md)
* Para saber mais sobre como gerenciar o Serviço de Aplicativo usando o PowerShell, veja [Como usar o PowerShell baseado no Azure Resource Manager para gerenciar Aplicativos Web do Azure.](app-service-web-app-azure-resource-manager-powershell.md)
* Para saber mais sobre o Serviço de Aplicativo do Azure no Linux, veja [Introdução ao Serviço de Aplicativo no Linux.](app-service-linux-intro.md)



<!--HONumber=Nov16_HO3-->


