---
title: Usando perfis de versão de API com o PowerShell no Azure Stack | Microsoft Docs
description: Saiba mais sobre como usar perfis de versão de API com o PowerShell no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 87052b39524ae7e3a789cada4ef9720f080726a6
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985474"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Use perfis de versão da API do PowerShell no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Perfis de versão de API fornecem uma maneira de gerenciar as diferenças de versão entre o Azure e o Azure Stack. Um perfil de versão de API é um conjunto de módulos do PowerShell do AzureRM com versões específicas de API. Cada plataforma de nuvem tem um conjunto de perfis de versão de API com suporte. Por exemplo, Azure Stack dá suporte a uma versão de perfil com data específica, como **2018-03-01-hybrid**, e o Azure dá suporte a **mais recente** perfil da versão de API. Quando você instala um perfil, são instalados os módulos do AzureRM PowerShell que correspondem ao perfil especificado.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Instalar o módulo do PowerShell necessário para usar perfis de versão da API

O **AzureRM.Bootstrapper** módulo que está disponível por meio da Galeria do PowerShell fornece cmdlets do PowerShell que são necessárias para trabalhar com perfis de versão da API. Use o cmdlet a seguir para instalar o módulo AzureRM.Bootstrapper:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="azure-stack-version-and-profile-versions"></a>A versão do Azure Stack e versões de perfil

A tabela a seguir lista a versão necessária de perfil de API e o moniker de módulo do PowerShell administrador usada para versões recentes do Azure Stack. Se você estiver usando este artigo com uma versão antes de 1808, atualize seu perfil de versão e o identificador de origem com o valor correto.

| N º de versão | Perfil da versão de API | PS moniker de módulo de administração |
| --- | --- | --- |
| 1808 ou posterior | 2018-03-01-hybrid | 1.5.0 |
| 1804 ou posterior | 2017-03-09-perfil | 1.4.0 |
| Versões anteriores 1804 | 2017-03-09-perfil | 1.2.11 |

> [!Note]  
> Para atualizar a partir de 1.2.11 versão, consulte a [guia de migração](https://aka.ms/azpsh130migration).

## <a name="install-a-profile"></a>Instalar um perfil

Use o **Install-AzureRmProfile** cmdlet com o **2018-03-01-hybrid** perfil da versão de API para instalar os módulos AzureRM exigidos pelo Azure Stack. Os módulos de operador do Azure Stack não são instalados com esse perfil de versão de API. Eles devem ser instalados separadamente, conforme especificado na etapa 3 das [instalar o PowerShell para Azure Stack](../azure-stack-powershell-install.md) artigo.

```PowerShell 
Install-AzureRMProfile -Profile 2018-03-01-hybrid
```

## <a name="install-and-import-modules-in-a-profile"></a>Instalar e importar os módulos em um perfil

Use o **uso-AzureRmProfile** cmdlet para instalar e importar os módulos que estão associados um perfil de versão de API. Você pode importar somente um perfil de versão de API em uma sessão do PowerShell. Para importar um perfil de versão de API diferente, você deve abrir uma nova sessão do PowerShell. O cmdlet Use-AzureRMProfile executa as seguintes tarefas:  
1. Verifica se os módulos do PowerShell associados ao perfil especificado de versão de API estão instalados no escopo atual.  
2. Baixa e instala os módulos se eles não ainda estiverem instalados.   
3. Importa os módulos na sessão atual do PowerShell. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser -Force
```

Para instalar e importar os módulos AzureRM selecionados de um perfil de versão de API, execute o cmdlet Use-AzureRMProfile com o **módulo** parâmetro:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Obtenha os perfis instalados

Use o **Get-AzureRmProfile** para obter a lista de perfis de versão de API disponíveis: 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```

## <a name="update-profiles"></a>Atualizar perfis

Use o **Update-AzureRmProfile** cmdlet para atualizar os módulos em um perfil de versão de API para a versão mais recente dos módulos que estão disponíveis em PSGallery. É recomendável para sempre executar o **Update-AzureRmProfile** cmdlet em uma nova sessão do PowerShell para evitar conflitos durante a importação de módulos. O cmdlet Update-AzureRmProfile executa as seguintes tarefas:

1. Verifica se as versões mais recentes dos módulos são instaladas em um determinado perfil de versão de API para o escopo atual.  
2. Solicita que você instale se eles não ainda estiverem instalados.  
3. Instala e importa os módulos atualizados para a sessão atual do PowerShell.  

```PowerShell
Update-AzureRmProfile -Profile 2018-03-01-hybrid
```

<!-- To remove the previously installed versions of the modules before updating to the latest available version, use the Update-AzureRmProfile cmdlet along with the **-RemovePreviousVersions** parameter:

```PowerShell 
Update-AzureRmProfile -Profile 2018-03-01-hybrid -RemovePreviousVersions
``` --> 

Esse cmdlet executa as seguintes tarefas:  

1. Verifica se as versões mais recentes dos módulos são instaladas em um determinado perfil de versão de API para o escopo atual.  
2. Remove as versões mais antigas dos módulos do perfil da versão de API atual e na sessão atual do PowerShell.  
4. solicita que você instale a versão mais recente.  
5. Instala e importa os módulos atualizados para a sessão atual do PowerShell.  
 
## <a name="uninstall-profiles"></a>Desinstalar perfis

Use o **Uninstall-AzureRmProfile** cmdlet para desinstalar o perfil de versão de API especificado.

```PowerShell 
Uninstall-AzureRmProfile -Profile  2018-03-01-hybrid
```

## <a name="next-steps"></a>Próximas etapas
* [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
* [Configurar o ambiente do PowerShell do usuário do Azure Stack](azure-stack-powershell-configure-user.md)  
