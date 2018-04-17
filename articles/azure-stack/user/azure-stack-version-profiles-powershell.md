---
title: Usando perfis de versão de API com o PowerShell na pilha do Azure | Microsoft Docs
description: Saiba mais sobre como usar perfis de versão de API com o PowerShell na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: b09799fe102522e1ad91f4983cf4f5fa8122b2c1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Use perfis de versão de API do PowerShell na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Perfis de versão de API fornecem uma maneira de gerenciar as diferenças de versão entre o Azure e a pilha do Azure. Um perfil de versão de API é um conjunto de módulos do AzureRM PowerShell com versões específicas de API. Cada plataforma de nuvem tem um conjunto de perfis de versão de API com suporte. Por exemplo, pilha do Azure oferece suporte a uma versão de perfil de data específico como **2017-03-09-perfil**, e o Azure suporta o **mais recente** perfil da versão de API. Quando você instalar um perfil, os módulos do AzureRM PowerShell que correspondem ao perfil especificado são instalados.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Instalar o módulo PowerShell necessário para usar perfis de versão de API

O **AzureRM.Bootstrapper** módulo que está disponível por meio da Galeria do PowerShell fornece cmdlets do PowerShell que são necessárias para trabalhar com perfis de versão de API. Use o cmdlet a seguir para instalar o módulo AzureRM.Bootstrapper:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="install-a-profile"></a>Instalar um perfil

Use o **instalar AzureRmProfile** cmdlet com o **2017-03-09-perfil** perfil da versão de API para instalar os módulos de AzureRM necessários pela pilha do Azure. Os módulos de operador de pilha do Azure não são instalados com este perfil de versão de API. Elas devem ser instaladas separadamente, conforme especificado na etapa 3 do [instalar o PowerShell para Azure pilha](azure-stack-powershell-install.md) artigo.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Instalar e importar os módulos em um perfil

Use o **AzureRmProfile Use** cmdlet para instalar e importar os módulos que estão associados um perfil de versão de API. Você pode importar somente um perfil de versão de API em uma sessão do PowerShell. Para importar um perfil diferente de versão de API, você deve abrir uma nova sessão do PowerShell. O cmdlet Use-AzureRMProfile executa as seguintes tarefas:  
1. Verifica se os módulos do PowerShell associados ao perfil especificado de versão de API estão instalados no escopo atual.  
2. Baixa e instala os módulos se eles não ainda estiverem instalados.   
3. Importa os módulos na sessão atual do PowerShell. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

Para instalar e importar os módulos de AzureRM selecionados de um perfil de versão de API, execute o cmdlet Use-AzureRMProfile com o **módulo** parâmetro:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Obter os perfis instalados

Use o **AzureRmProfile Get** para obter a lista de perfis disponíveis de versão de API: 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Perfis de atualização

Use o **AzureRmProfile atualização** cmdlet para atualizar os módulos em um perfil de versão de API para a versão mais recente dos módulos que estão disponíveis no PSGallery. Recomenda-se para sempre executar o **AzureRmProfile atualização** cmdlet em uma nova sessão do PowerShell para evitar conflitos durante a importação de módulos. O cmdlet Update-AzureRmProfile executa as seguintes tarefas:

1. Verifica se as versões mais recentes dos módulos estão instaladas em um determinado perfil de versão de API para o escopo atual.  
2. Solicita que você instale se eles não ainda estiverem instalados.  
3. Instala e importa os módulos atualizados para a sessão atual do PowerShell.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Para remover as versões instaladas anteriormente dos módulos antes de atualizar para a versão mais recente, use o cmdlet Update-AzureRmProfile juntamente com o **- RemovePreviousVersions** parâmetro:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Esse cmdlet executa as seguintes tarefas:  

1. Verifica se as versões mais recentes dos módulos estão instaladas em um determinado perfil de versão de API para o escopo atual.  
2. Remove as versões mais antigas dos módulos do perfil atual de versão de API e na sessão atual do PowerShell.  
4. solicita que você instale a versão mais recente.  
5. Instala e importa os módulos atualizados para a sessão atual do PowerShell.  
 
## <a name="uninstall-profiles"></a>Desinstalar perfis

Use o **AzureRmProfile desinstalação** cmdlet ao desinstalar o perfil de versão de API especificado.

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>Próximas etapas
* [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
* [Configurar o ambiente do PowerShell do usuário a pilha do Azure](azure-stack-powershell-configure-user.md)  
