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
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 3cda9d487d9548251c68d61db51bf2fb826b6f79
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775281"
---
# <a name="release-notes-for-validation-as-a-service"></a>Notas de versão para a validação como um serviço

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Este artigo apresenta as notas de versão para a validação de pilha do Azure como um serviço.

## <a name="version-405"></a>Versão 4.0.5

17 de janeiro de 2019

- Teste de identificação de disco atualizado para inconsistências de pool de armazenamento do endereço. Versão: 5.1.14.0 -> 5.1.15.0
- Azure Stack mensal verificação de atualização atualizado para o endereço aprovado inconsistências de validação de software e conteúdo. Versão: 5.1.14.0 -> 5.1.17.0
- Verificação do pacote de extensão de OEM atualizado para executar as verificações necessárias antes da etapa de atualização do Azure Stack. Versão: 5.1.14.0 -> 5.1.16.0
- Correções de bugs internas

## <a name="version-402"></a>Versão 4.0.2

7 de janeiro de 2019

Se você estiver executando o fluxo de trabalho do Azure Stack mensal atualizar verificação e a versão para o seu pacote de atualização do OEM não é 1810 ou superior, você receberá um erro quando você chegar à etapa de atualização de OEM. Este é um bug. Uma correção está sendo desenvolvida. As etapas de mitigação são da seguinte maneira:

1.  Execute a atualização de OEM como de costume.
2.  Executar teste AzureStack após a aplicação bem-sucedida do pacote e salvar a saída.
3.  Cancele o teste.
4.  Enviar a saída salva VaaSHelp@microsoft.com para receber os resultados de passar para a execução.

## <a name="version-402"></a>Versão 4.0.2

30 de novembro de 2018

- Correções de bugs internas

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

    `Install-VaaSPrerequisites` Agora exige credenciais de administrador de nuvem para resolver um problema durante a validação de pacote. A documentação em [Baixe e instale o agente](azure-stack-vaas-local-agent.md#download-and-install-the-agent) foi atualizado com o seguinte:

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

    Quando um pacote de personalização de OEM é enviado como parte do fluxo de trabalho de validação do pacote, o formato de pacote será validado para garantir que ela segue a especificação publicada. Se o pacote não estiver em conformidade, a execução falhará. Notificações por email serão enviadas ao endereço de email do contato do Active Directory do Azure registrado para o locatário.

  - Categoria de teste interativa

    O **interativo** categoria de teste foi adicionada. Esses testes exercitam interativos e não-automatizados cenários do Azure Stack.

  - Verificação de recurso interativo

    A capacidade de fornecer comentários com foco para determinados recursos agora está disponível no fluxo de trabalho de aprovação do teste. O `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` teste verifica se as atualizações específicas foram aplicadas corretamente e, em seguida, coleta de comentários.

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas de validação como um serviço](azure-stack-vaas-troubleshoot.md)
