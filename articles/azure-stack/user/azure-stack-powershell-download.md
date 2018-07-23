---
title: Baixar ferramentas do Azure Stack no GitHub | Microsoft Docs
description: Saiba como baixar as ferramentas necessárias para trabalhar com o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: mabrigg
ms.openlocfilehash: 9c1e4abe50b06db58a4ca05a99e1ae4a531b2294
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187444"
---
# <a name="download-azure-stack-tools-from-github"></a>Baixar ferramentas do Azure Stack no GitHub

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Ferramentas de AzureStack é um repositório do GitHub que hospeda os módulos do PowerShell que você pode usar para gerenciar e implantar recursos no Azure Stack.

## <a name="download-targets"></a>Baixe os destinos

Você pode baixar e usar esses módulos do PowerShell para o Kit de desenvolvimento do Azure Stack ou para um cliente externo baseado em Windows que usa uma conexão VPN.

## <a name="how-to-get-the-tools"></a>Como obter as ferramentas

Para obter essas ferramentas, clone o repositório GitHub de ferramentas AzureStack ou baixar a pasta de ferramentas AzureStack executando o seguinte script:

```PowerShell
# Change directory to the root directory
cd \

# Download the tools archive
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>Funcionalidades fornecidas pelos módulos

O repositório de ferramentas AzureStack contém módulos do PowerShell que dão suporte as seguintes funcionalidades para o Azure Stack:

| Funcionalidade | DESCRIÇÃO | Quem pode usar este módulo? |
| --- | --- | --- |
| [Recursos de nuvem](https://github.com/Azure/AzureStack-Tools/tree/master/CloudCapabilities) | Use este módulo para obter os recursos de nuvem de uma nuvem. Por exemplo, você pode obter os recursos de nuvem, como versão de API, recursos do Azure Resource Manager, as extensões de VM etc. para o Azure Stack e nuvens do Azure usando esse módulo. | Os administradores de nuvem e usuários. |
| [Política do Gerenciador de recursos para o Azure Stack](azure-stack-policy-module.md) | Use este módulo para configurar uma assinatura do Azure ou um grupo de recursos do Azure com a mesma controle de versão e disponibilidade do serviço como o Azure Stack. | Os usuários e administradores de nuvem |
| [Conectar-se ao Azure Stack](azure-stack-connect-azure-stack.md) | Use este módulo para se conectar a uma instância do Azure Stack por meio do PowerShell e para configurar a conectividade VPN para o Azure Stack. | Os usuários e administradores de nuvem |
| [Validador de modelo](azure-stack-validate-templates.md) | Use este módulo para verificar se um existente ou um novo modelo pode ser implantado para o Azure Stack. | Os usuários e administradores de nuvem |

## <a name="next-steps"></a>Próximas etapas

- [Configurar o ambiente do PowerShell do usuário do Azure Stack](azure-stack-powershell-configure-user.md)
- [Conectar ao Kit de desenvolvimento do Azure Stack por uma VPN](azure-stack-connect-azure-stack.md)
