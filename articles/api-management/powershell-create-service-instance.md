---
title: "Criar uma instância de Gerenciamento de API do Azure usando o PowerShell | Microsoft Docs"
description: "Siga as etapas deste tutorial para criar uma nova instância de Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: juliako
manager: cflower
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: d33a37002f1e3a2684d4b93682dbd48327f3312b
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Criar uma nova instância do serviço de Gerenciamento de API do Azure

O APIM (Gerenciamento de API) do Azure ajuda as organizações a publicar APIs para parceiros externos e desenvolvedores internos a fim de desbloquear o potencial de seus dados e serviços. O Gerenciamento de API fornece as competências essenciais para garantir um programa de API de sucesso através do envolvimento do desenvolvedor, ideias de negócios, análises, segurança e proteção. O APIM permite que você crie e gerencie gateways de API modernos para serviços de back-end hospedados em qualquer lugar. Para obter mais informações, confira o tópico [Visão geral](api-management-key-concepts.md).

Este início rápido descreve as etapas para criar uma nova instância de Gerenciamento de API usando scripts do PowerShell. O início rápido mostra como usar o **Azure Cloud Shell** que você pode executar do Portal do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em http://portal.azure.com.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 3.6 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.


## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>Criar um serviço de Gerenciamento de API

Essa é uma operação de execução longa e pode levar até 15 minutos.

```azurepowershell-interactive
New-AzureRmApiManagement -ResourceGroupName "myResourceGroup" -Location "West US" -Name "apim-name" -Organization "myOrganization" -AdminEmail "myEmail" -Sku "Developer"
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Importar e publicar sua primeira API](import-and-publish.md)
