---
title: Baixar ferramentas de pilha do Azure do GitHub | Microsoft Docs
description: Saiba como baixar as ferramentas necessárias para trabalhar com a pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: a116b7a048ff95ca601a65633cdc63f98fefee9d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075282"
---
# <a name="download-azure-stack-tools-from-github"></a>Baixar ferramentas de pilha do Azure do GitHub

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

**Ferramentas de AzureStack** é um repositório do GitHub que hospeda os módulos do PowerShell para gerenciar e implantar recursos a pilha do Azure. Se você estiver planejando estabelecer a conectividade VPN, você pode baixar esses módulos do PowerShell para o Kit de desenvolvimento de pilha do Azure, ou para um cliente externo baseado em Windows. Para obter essas ferramentas, clone o repositório GitHub ou baixar o **AzureStack ferramentas** pasta executando o script a seguir:

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>Funcionalidade fornecida pelos módulos

O **AzureStack ferramentas** repositório contém módulos do PowerShell que dão suporte as seguintes funcionalidades de pilha do Azure:  

| Funcionalidade | DESCRIÇÃO | Quem pode usar este módulo? |
| --- | --- | --- |
| [Recursos de nuvem](user/azure-stack-validate-templates.md) | Use este módulo para obter os recursos de nuvem de uma nuvem. Por exemplo, ao usar este módulo, você pode obter os recursos de nuvem, como versão de API e recursos do Gerenciador de recursos do Azure. Você também pode obter as extensões VM para a pilha do Azure e nuvens do Azure usando este módulo. | Os usuários e os operadores de nuvem |
| [Política do Gerenciador de recursos para a pilha do Azure](user/azure-stack-policy-module.md) | Use este módulo para configurar uma assinatura do Azure ou um grupo de recursos do Azure com o mesmo serviço e controle de versão de disponibilidade como a pilha do Azure. | Os usuários e os operadores de nuvem |
| [Registrar com o Azure](azure-stack-register.md) | Use este módulo para registrar sua instância do kit de desenvolvimento com o Azure. Depois de registrar, você pode baixar os itens do marketplace do Azure e usá-los na pilha do Azure. | Operadores de nuvem |
| [Implantação de pilha do Azure](azure-stack-run-powershell-script.md) | Use este módulo para preparar o computador de host de pilha do Azure para implantar e reimplantar usando a imagem de disco rígido virtual (VHD) de pilha do Azure. | Operadores de nuvem|
| [Conectando-se a pilha do Azure](azure-stack-connect-powershell.md) | Use este módulo para configurar a conectividade VPN com a pilha do Azure. | Os usuários e os operadores de nuvem |
| [Validador de modelo](user/azure-stack-validate-templates.md) | Use este módulo para verificar se um existente ou um novo modelo pode ser implantado a pilha do Azure. | Os usuários e os operadores de nuvem|


## <a name="next-steps"></a>Próximas etapas
* [Configurar o ambiente do PowerShell do usuário a pilha do Azure](user/azure-stack-powershell-configure-user.md)   
* [Conecte-se ao Kit de desenvolvimento de pilha do Azure através de uma VPN](azure-stack-connect-azure-stack.md)  
