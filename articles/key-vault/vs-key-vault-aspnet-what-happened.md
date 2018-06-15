---
title: As alterações feitas em um projeto ASP.NET quando você conecta o Azure Key Vault | Microsoft Docs
description: Descreve o que acontece ao seu projeto do ASP.NET quando você se conecta ao Key Vault usando os serviços conectados do Visual Studio.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a43f893c7ee87ffb02179c06ea5786715547e93a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781555"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>O que aconteceu ao meu projeto do ASP.NET (serviço conectado Key Vault do Visual Studio)?

> [!div class="op_single_selector"]
> - [Introdução](vs-key-vault-aspnet-get-started.md)
> - [O que aconteceu](vs-key-vault-aspnet-what-happened.md)

Este artigo identifica as alterações exatas feitas em um projeto ASP.NET ao adicionar o [serviço conectado Key Vault usando o Visual Studio](vs-key-vault-add-connected-service.md).

Para obter informações sobre como trabalhar com o serviço conectado, consulte [Introdução](vs-key-vault-aspnet-get-started.md).

## <a name="added-references"></a>Referências adicionadas

Afeta as referências de *.NET do arquivo de projeto e `packages.config` (referências de NuGet).

| type | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>Arquivos adicionados

- ConnectedService.json adicionado, que registra algumas informações sobre o provedor, a versão de um link para a documentação do Serviço Conectado.

## <a name="project-file-changes"></a>Alterações de arquivo de projeto

- Adicione o ItemGroup dos Serviços Conectados e o arquivo ConnectedServices.json.
- Referências aos assemblies .NET descritos na seção [Referências adicionadas](#added-references).

## <a name="webconfig-or-appconfig-changes"></a>alterações de web.config ou app.config

- Adicionadas as seguintes entradas de configuração:

    ```xml
    <appSettings>
       <add key="vaultName" value="<your Key Vault name>" />
       <add key="vaultUri" value="<the URI to your Key Vault in Azure>" />
    </appSettings>
    ```

## <a name="changes-on-azure"></a>Alterações no Azure

- Criação de um grupo de recursos (ou uso de um existente).
- Criação de um Key Vault no grupo de recursos especificado.

