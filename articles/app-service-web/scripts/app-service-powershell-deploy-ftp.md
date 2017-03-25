---
title: Exemplo de Script do Azure PowerShell - Carregar arquivos em um aplicativo Web usando FTP | Microsoft Docs
description: Exemplo de Script do Azure PowerShell - Carregar arquivos em um aplicativo Web usando FTP
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 5f3bf8087fa6601ae410d6db907471fd6faa2742
ms.lasthandoff: 03/10/2017

---

# <a name="upload-files-to-a-web-app-using-ftp"></a>Carregar arquivos para um aplicativo Web usando FTP

Este script de exemplo faz o seguinte usando o Azure PowerShell: 

* Crie um aplicativo Web no Serviço de Aplicativo do Azure na região Europa Ocidental do Azure.
* Obter informações de conexão de FTP do aplicativo Web
* Implantar o código do aplicativo Web usando FTP (via [WebClient.UploadFile()](https://msdn.microsoft.com/library/ms144229.aspx)).

## <a name="prerequisites"></a>Pré-requisitos

* Execute `Login-AzureRmAccount` para efetuar logon no Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[principal](../../../powershell_scripts/app-service/deploy-ftp/deploy-ftp.ps1?highlight=1 "Carregar arquivos em um aplicativo Web usando FTP")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Após a execução do exemplo de script, o comando a seguir pode ser usado para remover o Grupo de Recursos, o aplicativo Web e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | Cria um Plano do Serviço de Aplicativo. |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Cria um aplicativo web. |
| [Get-AzureRmWebAppPublishingProfile](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/get-azurermwebapppublishingprofile) | Obtenha perfis de publicação do aplicativo Web. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o módulo do Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Exemplos adicionais do Azure PowerShell para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos do Azure PowerShell](../app-service-powershell-samples.md).

