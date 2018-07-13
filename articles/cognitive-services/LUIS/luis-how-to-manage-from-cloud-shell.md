---
title: Exibir o uso do LUIS no Azure Cloud Shell | Microsoft Docs
description: Saiba como obter informações de uso no Azure Cloud Shell para o LUIS.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2017
ms.author: v-geberr
ms.openlocfilehash: 2de25645e5377efdd53bcc980695804d34db5ee2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364413"
---
# <a name="manage-luis-service-from-azure-cloud-shell"></a>Gerenciar o serviço LUIS no Azure Cloud Shell
O portal do Azure permite que você use cmdlets do PowerShell para trabalhar com recursos do LUIS. 

Esses cmdlets permitem que você [crie](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) uma assinatura do LUIS, obtenha informações sobre a assinatura, incluindo o [uso](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0), e [remova](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0) a assinatura. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Conta de armazenamento e autenticação do Cloud Shell
Para usar o PowerShell no [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell) no portal do Azure, você precisa ter uma conta de armazenamento do Azure. Caso não tenha uma [conta de armazenamento](https://docs.microsoft.com/en-us/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share), você deverá criar uma. A conta de armazenamento permite salvar scripts do PowerShell no Cloud Shell.  

Você também precisa se autenticar no Azure no Cloud Shell para acessar os recursos. 

Depois que você tiver uma conta de armazenamento e for autenticado, poderá executar cmdlets do PowerShell.

## <a name="open-cloud-shell"></a>Abrir Cloud Shell
Quando você usar o Cloud Shell no portal do Azure, você sempre estará usando a versão mais atual do PowerShell. 

Use o botão **Iniciar o Cloud Shell** para abrir o Cloud Shell ou abrir um navegador com [https://shell.azure.com](https://shell.azure.com). 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>

## <a name="luis-endpoint-usage-information"></a>Informações de uso do ponto de extremidade do LUIS

O cmdlet do PowerShell 6.x, `Get-AzureRmCognitiveServicesAccountUsage`, fornece informações de uso para os Serviços Cognitivos da Microsoft, incluindo o LUIS. [Get-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) exige o nome do grupo de recursos e o nome de recurso do serviço. 

A sintaxe do comando é:

```
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

No exemplo a seguir, o nome do grupo de recursos é `luis-westus-rg` e o nome de assinatura do serviço LUIS é `luis-westus-1`. Esses dois nomes são escolhidos quando o serviço LUIS é criado. 

O cmdlet retorna informações de uso de 16 de 10.000 ocorrências do ponto de extremidade usadas em um período de 30 dias com o período terminando em 7 de junho:

```
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

Salve o comando como um arquivo do PowerShell, *.ps1, na conta de armazenamento do Azure associada ao Cloud Shell e execute-o a qualquer momento. 

![Executar o script por meio do armazenamento](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

Depois que o script é salvo na unidade de nuvem, você pode executar o script do PowerShell no Azure no Cloud Shell do aplicativo de telefone do Azure. 

![Executar o script por meio do armazenamento no aplicativo de telefone](./media/luis-how-to-manage-from-powershell/phone-app.png)