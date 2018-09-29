---
title: Baixar ferramentas do Azure Stack no GitHub | Microsoft Docs
description: Saiba como baixar as ferramentas que são necessárias para trabalhar com o Azure Stack.
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
ms.date: 09/28/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 5b90ebc554738c89816cf88f8984ffab01c87d4d
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451305"
---
# <a name="download-azure-stack-tools-from-github"></a>Baixar ferramentas do Azure Stack no GitHub

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

**Ferramentas de AzureStack** é um [repositório GitHub](https://github.com/Azure/AzureStack-Tools) que hospeda os módulos do PowerShell para gerenciar e implantar recursos no Azure Stack. Se você estiver planejando estabelecer a conectividade VPN, você pode baixar esses módulos do PowerShell para o Kit de desenvolvimento do Azure Stack ou para um cliente externo baseado em Windows. Para obter essas ferramentas, clone o repositório do GitHub ou baixe o **AzureStack ferramentas** pasta executando o seguinte script:

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

O **AzureStack ferramentas** repositório contém módulos do PowerShell que dão suporte as seguintes funcionalidades para o Azure Stack:  

| Funcionalidade | DESCRIÇÃO | Quem pode usar este módulo? |
| --- | --- | --- |
| [Recursos de nuvem](user/azure-stack-validate-templates.md) | Use este módulo para obter os recursos de nuvem de uma nuvem. Por exemplo, usando esse módulo, você pode obter os recursos de nuvem, como a versão de API e recursos do Azure Resource Manager. Você também pode obter as extensões de VM para o Azure Stack e nuvens do Azure usando esse módulo. | Os usuários e os operadores de nuvem |
| [Política do Gerenciador de recursos para o Azure Stack](user/azure-stack-policy-module.md) | Use este módulo para configurar uma assinatura do Azure ou um grupo de recursos do Azure com a mesma controle de versão e disponibilidade do serviço como o Azure Stack. | Os usuários e os operadores de nuvem |
| [Registre-se com o Azure](azure-stack-register.md) | Use este módulo para registrar sua instância do kit de desenvolvimento com o Azure. Após o registro, você pode baixar os itens do marketplace do Azure e usá-los no Azure Stack. | Operadores de nuvem |
| [Implantação de pilha do Azure](azure-stack-run-powershell-script.md) | Use este módulo para preparar o computador de host do Azure Stack para implantar e reimplantar usando a imagem de disco rígido virtual (VHD) do Azure Stack. | Operadores de nuvem|
| [Conectar-se ao Azure Stack](azure-stack-connect-powershell.md) | Use este módulo para configurar a conectividade VPN para o Azure Stack. | Os usuários e os operadores de nuvem |
| [Validador de modelo](user/azure-stack-validate-templates.md) | Use este módulo para verificar se um existente ou um novo modelo pode ser implantado para o Azure Stack. | Os usuários e os operadores de nuvem|


## <a name="next-steps"></a>Próximas etapas
* [Configurar o ambiente do PowerShell do usuário do Azure Stack](user/azure-stack-powershell-configure-user.md)   
* [Conectar ao Kit de desenvolvimento do Azure Stack por uma VPN](azure-stack-connect-azure-stack.md)  
