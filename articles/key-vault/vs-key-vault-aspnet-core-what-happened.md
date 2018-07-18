---
title: As alterações feitas em um projeto ASP.NET Core quando você conecta o Azure Key Vault | Microsoft Docs
description: Descreve o que acontece ao seu projeto do ASP.NET Core quando você se conecta ao Key Vault usando os serviços conectados do Visual Studio.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 8b6c590344db2997c1a987da14cabba76cdca83b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>O que aconteceu ao meu projeto do ASP.NET Core (serviço conectado Key Vault do Visual Studio)?

> [!div class="op_single_selector"]
> - [Introdução](vs-key-vault-aspnet-core-get-started.md)
> - [O que aconteceu](vs-key-vault-aspnet-core-what-happened.md)

Este artigo identifica as alterações exatas feitas em um projeto ASP.NET ao adicionar o [serviço conectado Key Vault usando o Visual Studio](vs-key-vault-add-connected-service.md).

Para obter informações sobre como trabalhar com o serviço conectado, consulte [Introdução](vs-key-vault-aspnet-core-get-started.md).

## <a name="added-references"></a>Referências adicionadas

Afeta as referências *.NET do arquivo de projeto) e referências de pacte NuGet.

| type | Referência |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>Arquivos adicionados

- ConnectedService.json adicionado, que registra algumas informações sobre o provedor, a versão de um link para a documentação do Serviço Conectado.

## <a name="project-file-changes"></a>Alterações de arquivo de projeto

- Adicione o ItemGroup dos Serviços Conectados e o arquivo ConnectedServices.json.

## <a name="launchsettingsjson-changes"></a>launchsettings.json muda

- Adição das seguintes entradas de variável de ambiente ao perfil do IIS Express e ao perfil que coincide com o nome do seu projeto da Web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Alterações no Azure

- Criação de um grupo de recursos (ou uso de um existente).
- Criação de um Key Vault no grupo de recursos especificado.

