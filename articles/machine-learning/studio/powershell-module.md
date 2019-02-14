---
title: Módulos do PowerShell para o Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Use o PowerShell para criar e gerenciar os workspaces do Azure Machine Learning Studio, os experimentos, os serviços Web e muito mais.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=haining, previous-author=hning86
ms.date: 01/25/2019
ms.openlocfilehash: b6080e6e81b6cedce4d08cf20e082c87e5cecade
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489811"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Módulo do PowerShell para o Azure Machine Learning Studio

Usando os módulos do PowerShell, você pode gerenciar programaticamente seus recursos e ativos do Studio como workspaces, conjuntos de dados e serviços Web.

Você pode interagir com recursos do Studio usando três módulos do PowerShell:

* [Az do Azure PowerShell](#az-rm) lançado em 2018, inclui todas as funcionalidades do AzureRM, embora com nomes de cmdlet diferentes
* [AzureRM](#az-rm) lançado em 2016
* [PowerShell do Azure Machine Learning clássico](#classic) lançado em 2016

Embora esses módulos tenham algumas semelhanças, cada um é projetado para cenários específicos. Este artigo descreve as diferenças entre os módulos do PowerShell e ajuda você a decidir quais escolher.

## <a name="choosing-modules"></a> Escolhendo módulos

Escolher entre os módulos do PowerShell disponíveis depende do tipo de recursos que você está gerenciando.

Verifique a [tabela de suporte](#support-table) abaixo para ver quais recursos são compatíveis com cada módulo. Uma vez que a implantação clássica do PowerShell pode ser instalada em paralelo com o Az ou o AzureRM, você pode instalar dois módulos e abranger todos os tipos de recursos (clássicos com Az ou clássicos com o AzureRM)

No entanto, não é recomendável ter o Az e o AzureRM instalados ao mesmo tempo. Para decidir entre o Az e o AzureRM, a Microsoft recomenda o Az para todas as implantações futuras. Use o AzureRm somente se houver circunstâncias especiais em seu ambiente que precisam dele.

Para saber mais sobre as diferenças entre o Az e o AzureRM, bem como nosso caminho de migração fornecido, confira nossa [introdução ao AZ do Azure PowerShell.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

## <a name="az-rm"></a> Az e AzureRM do Azure PowerShell

O Az e o AzureRM gerenciam soluções implantadas usando o modelo de implantação do **Azure Resource Manager**. Esses recursos incluem workspaces do Studio e novos serviços Web do Studio. Para gerenciar recursos implantados usando o modelo de implantação clássico, você deve usar o módulo clássico do PowerShell. Se você quiser saber mais sobre os modelos de implantação, confira o artigo [Azure Resource Manager versus implantação clássica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

O Az agora é o módulo do PowerShell indicado para interagir com o Azure e inclui toda a funcionalidade anterior do AzureRM. O AzureRM continuará a receber as correções de bugs, mas não receberá nenhum novo recurso ou cmdlet. Embora haja um caminho de atualização do AzureRM, se você tiver problemas com o Az ao trabalhar com o Studio, relate o problema e faça o fallback para usar o AzureRM.

Para começar a trabalhar com o Az, siga as [instruções de instalação do Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> PowerShell clássico

O [módulo clássico do PowerShell](https://aka.ms/amlps) do Studio permite que você gerencie os recursos implantados usando o **modelo de implantação clássico**. Esses recursos incluem ativos do usuário do Studio, serviços Web clássicos e pontos de extremidade de serviço Web clássicos.

No entanto, a Microsoft recomenda que você use o modelo de implantação do Resource Manager para todos os novos recursos para simplificar a implantação e o gerenciamento de recursos. Se você quiser saber mais sobre os modelos de implantação, confira o artigo [Azure Resource Manager versus implantação clássica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Para começar a usar o PowerShell clássico, baixe o [pacote de versão](https://github.com/hning86/azuremlps/releases) do GitHub e siga as [instruções de instalação](https://github.com/hning86/azuremlps/blob/master/README.md). As instruções explicam como desbloquear a DLL baixada/descompactada e importá-la para o ambiente do PowerShell.

## <a name="support-table"></a> Tabela de compatibilidade do PowerShell

 **Workspaces do Studio** | **Az** |  **AzureRM** | **Implantação clássica do PowerShell** |
| --- | --- | --- | --- | --- |
| Criar/excluir workspaces | [Modelos do Gerenciador de Recursos](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [Modelos do Gerenciador de Recursos](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Gerenciar os usuários do workspace |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gerenciar os planos de compromisso | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | [New-AzureRmMlCommitmentPlan](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlcommitmentplan) |
|||
| **Serviços Web** | **Az** | **AzureRM** | **Implantação clássica do PowerShell** |
| Gerenciar serviços Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> (Novos serviços Web) | [New-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlwebservice) <br> (Novos serviços Web) |[New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> (serviços Web clássicos) |
| Gerenciar pontos de extremidade/chaves |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> (Novos serviços Web) | [Get-AzureRmMlWebServiceKeys](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservicekeys) <br> (Novos serviços Web) | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> (serviços Web clássicos) |
|||
| **Ativos do usuário** | **Az** | **AzureRM** | **Implantação clássica do PowerShell** |
| Gerenciar modelos de conjuntos de dados/treinados |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gerenciar experimentos |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gerenciar módulos personalizados |  |  | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Próximas etapas
Siga esses links para encontrar a documentação completa dos módulos do PowerShell:
* [AzureRM](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/#machine_learning)
* [Implantação clássica do PowerShell](https://aka.ms/amlps)
* [Az do Azure PowerShell](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
