---
title: Início Rápido - Criar um servidor do Azure Analysis Services usando PowerShell | Microsoft Docs
description: Saiba como criar a um servidor do Azure Analysis Services usando PowerShell
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9149f15d0503b9a39ac67d9c3f6fc1ddde7e03bd
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952759"
---
# <a name="quickstart-create-a-server---powershell"></a>Início Rápido: Criar um servidor - PowerShell

Este início rápido descreve como usar o PowerShell em uma linha de comando para criar um servidor do Azure Analysis Services em sua assinatura do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure**: visite a [Avaliação Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
- **Azure Active Directory**: sua assinatura deve estar associada a um locatário do Azure Active Directory, e você precisa ter uma conta nesse diretório. Para obter mais informações, confira [Autenticação e permissões de usuário](analysis-services-manage-users.md).
- **Versão de módulo do Azure PowerShell 4.0 ou posterior**. Para saber qual é a versão, execute ` Get-Module -ListAvailable AzureRM`. Para instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="import-azurermanalysisservices-module"></a>Importar o módulo AzureRm.AnalysisServices

Para criar um servidor em sua assinatura, use o módulo de componente [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Carregar o módulo AzureRm.AnalysisServices em sua sessão do PowerShell.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Entre em sua assinatura do Azure usando o comando [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). Siga as instruções na tela.

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. Quando você cria o servidor, precisa especificar um grupo de recursos em sua assinatura. Se você ainda não tiver um grupo de recursos, crie um novo usando o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo a seguir cria um grupo de recursos chamado `myResourceGroup` na região Oeste dos EUA.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Criar um servidor

Crie um novo servidor usando o comando [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). O exemplo a seguir cria um servidor chamado myServer em myResourceGroup, na região Oeste dos EUA, na camada D1 (grátis) e especifica philipc@adventureworks.com como administrador do servidor.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Limpar recursos

Você pode remover o servidor de sua assinatura usando o comando [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). Se você ainda for seguir outros guias de início rápido e tutoriais desta coleção, não remova o servidor. O exemplo a seguir remove o servidor criado na etapa anterior.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar um servidor em sua assinatura do Azure usando o PowerShell. Agora que você tem um servidor, pode ajudar a protegê-lo configurando um firewall do servidor (opcional). Você também pode adicionar um modelo de dados de amostra básico ao servidor diretamente do portal. Ter um modelo de exemplo é útil para saber mais sobre como configurar funções do modelo de banco de dados e testar conexões de cliente. Para saber mais, continue para o tutorial para adicionar um modelo de exemplo.

> [!div class="nextstepaction"]
> [Início Rápido: Configurar firewall do servidor - Portal](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Tutorial: Adicionar um modelo de exemplo ao seu servidor](analysis-services-create-sample-model.md)