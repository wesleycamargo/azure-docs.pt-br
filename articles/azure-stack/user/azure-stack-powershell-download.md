---
title: Baixar ferramentas de pilha do Azure do GitHub | Microsoft Docs
description: "Saiba como baixar as ferramentas necessárias para trabalhar com a pilha do Azure."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: 1957e63914d5f9f443a504ef90df49d79ec3e40f
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2017
---
# <a name="download-azure-stack-tools-from-github"></a>Baixar ferramentas de pilha do Azure do GitHub

Ferramentas AzureStack é um repositório do GitHub que hospeda os módulos do PowerShell que você pode usar para gerenciar e implantar recursos a pilha do Azure. Você pode baixar e usar esses módulos do PowerShell para o Kit de desenvolvimento de pilha do Azure, ou para um cliente externo baseado em windows, se você estiver planejando estabelecer a conectividade VPN. Para obter essas ferramentas, clone o repositório GitHub ou baixar a pasta Ferramentas AzureStack executando o script a seguir:

```PowerShell
# Change directory to the root directory 
cd \

# Download the tools archive
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

## <a name="functionalities-provided-by-the-modules"></a>Funcionalidades oferecidas pelos módulos

O repositório de ferramentas AzureStack contém módulos do PowerShell que dão suporte as seguintes funcionalidades de pilha do Azure:  

| Funcionalidade | Descrição | Quem pode usar este módulo? |
| --- | --- | --- |
| [Recursos de nuvem](azure-stack-validate-templates.md) | Use este módulo para obter os recursos de nuvem de uma nuvem. Por exemplo, você pode obter os recursos de nuvem, como versão de API, recursos do Gerenciador de recursos do Azure, etc. as extensões de VM para a pilha do Azure e nuvens do Azure usando este módulo. | Os administradores de nuvem e usuários. |
| [Política do Gerenciador de recursos para a pilha do Azure](azure-stack-policy-module.md) | Use este módulo para configurar uma assinatura do Azure ou um grupo de recursos do Azure com o mesmo serviço e controle de versão de disponibilidade como a pilha do Azure. | Os usuários e administradores de nuvem |
| [Conectando-se a pilha do Azure](azure-stack-connect-azure-stack.md) | Use este módulo para se conectar a uma instância de pilha do Azure por meio do PowerShell e para configurar a conectividade VPN com a pilha do Azure. | Os usuários e administradores de nuvem |
| [Validador de modelo](azure-stack-validate-templates.md) | Use este módulo para verificar se um existente ou um novo modelo pode ser implantado a pilha do Azure. | Os usuários e administradores de nuvem |


## <a name="next-steps"></a>Próximas etapas
* [Configurar o ambiente do PowerShell do usuário a pilha do Azure](azure-stack-powershell-configure-user.md)   
* [Conecte-se ao Kit de desenvolvimento de pilha do Azure através de uma VPN](azure-stack-connect-azure-stack.md)  
