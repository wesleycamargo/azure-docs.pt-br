---
title: Notas de versão de validação de pilha do Azure como um serviço | Microsoft Docs
description: Notas de versão de validação de pilha do Azure como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 687e47eff000679f24088a59297a258a1469f3fd
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106266"
---
# <a name="release-notes-for-validation-as-a-service"></a>Notas de versão para a validação como um serviço

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Este artigo apresenta as notas de versão para a validação de pilha do Azure como um serviço.

## <a name="version-401"></a>Versão 4.0.1

8 de outubro de 2018

- Pré-requisitos de VaaS

    `Install-VaaSPrerequisites` não exige credenciais de administrador de nuvem. Se você estiver executando a versão mais recente desse cmdlet, consulte [Baixe e instale o agente](azure-stack-vaas-local-agent.md#download-and-install-the-agent) para os comandos revisados para instalar os pré-requisitos. Aqui estão os comandos:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>Versão 4.0.0

29 de agosto de 2018

- Pré-requisitos de VaaS e o VHD de atualizações

    `Install-VaaSPrerequisites` Agora exige credenciais de administrador de nuvem para resolver um problema durante a validação da solução. A documentação em [Baixe e instale o agente](azure-stack-vaas-local-agent.md#download-and-install-the-agent) foi atualizado com o seguinte:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > O `$CloudAdminCreds` exigido pelo script é para o Azure Stack instância que está sendo validada. Eles não são as credenciais do Active Directory do Azure usadas pelo locatário VaaS.

- Atualização do agente local

    A versão anterior do agente local não é compatível com o 4.0.0 atual versão do serviço. Todos os usuários devem atualizar seus agentes locais. Ver [Baixe e instale o agente](azure-stack-vaas-local-agent.md#download-and-install-the-agent) para obter instruções sobre como instalar o agente mais recente.

- Atualização da automação do PowerShell

    Foram feitas alterações `LaunchVaaSTests` scripts do PowerShell que exigem a versão mais recente dos pacotes de script. Ver [iniciar o fluxo de trabalho de aprovação do teste](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow) para obter instruções sobre como instalar a versão mais recente do pacote de script.

- Validação como um Portal de serviço

  - Notificações de assinatura de pacote

    Quando um pacote de personalização de OEM é enviado como parte do fluxo de trabalho de validação de soluções, o formato de pacote será validado para garantir que ela segue a especificação publicada. Se o pacote não estiver em conformidade, a execução falhará. Notificações por email serão enviadas ao endereço de email do contato do Active Directory do Azure registrado para o locatário.

  - Categoria de teste interativa

    O **interativo** categoria de teste foi adicionada. Esses testes permitem que os parceiros para exercitar cenários do Azure Stack interativos não automatizada.

  - Verificação de recurso interativo

    A capacidade de fornecer comentários com foco para determinados recursos agora está disponível no fluxo de trabalho de aprovação do teste. O `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` teste verifica se as atualizações específicas foram aplicadas corretamente e, em seguida, coleta de comentários.

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas de validação como um serviço](azure-stack-vaas-troubleshoot.md)