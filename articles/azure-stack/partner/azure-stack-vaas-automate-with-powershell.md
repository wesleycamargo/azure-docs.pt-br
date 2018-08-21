---
title: Automatizar a validação de pilha do Azure com o PowerShell | Microsoft Docs
description: Você pode automatizar a validação de pilha do Azure com o PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1cb4b1a7cfd72ea302676244a53af58e77215aa9
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235197"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatizar a validação de pilha do Azure com o PowerShell 

Validação como um serviço (VaaS) fornece a capacidade de automatizar a inicialização de testes usando o **LaunchVaaSTests.ps1** script.

Você pode usar o PowerShell para o fluxo de trabalho a seguir:

- Fluxo de trabalho de aprovação do teste

Esse script abrange quatro elementos de um fluxo de trabalho:

- Instala pré-requisitos.
- Iniciando o agente local e instalado.
- Inicia uma categoria de testes, como a integração, funcional, confiabilidade.
- Geração de arquivos de relatórios de cada teste passe o resultado para o monitoramento e relatório.

## <a name="launch-the-test-pass-workflow"></a>Iniciar o fluxo de trabalho de aprovação de teste

1. Abra um prompt elevado do PowerShell.

2. Execute o script a seguir para baixar o script de automação:

    ````PowerShell  
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://vaastestpacksprodeastus.blob.core.windows.net/packages/Microsoft.VaaS.Scripts.3.0.0.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ````

3. Execute o script a seguir com seus valores:

    ````PowerShell  
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>"  -AsPlainText -Force)
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<ServiceAdminUser>", (ConvertTo-SecureString "<ServiceAdminPassword>" -AsPlainText -Force)
    $TenantAdminCreds = New-Object System.Management.Automation.PSCredential "<TenantAdminUser>", (ConvertTo-SecureString "<TenantAdminPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
        -VaaSAccountTenantId <VaaSAccountTenantId> `
        -VaaSSolutionName <VaaSSolutionName> `
        -VaaSTestPassName <VaaSTestPassName> `
        -VaaSTestCategories Integration,Functional `
        -MaxScriptWaitTimeInHours 12 `
        -ServiceAdminCreds $ServiceAdminCreds `
    ````

    **Parâmetros**

    | Parâmetro | DESCRIÇÃO |
    | --- | --- |
    | VaaSUserld | Sua ID de usuário VaaS. | 
    | VaaSUserPassword | Sua senha VaaS. |
    | ServiceAdminUser | Sua conta de administrador de serviço do Azure Stack.  |
    | ServiceAdminPassword | Sua senha de serviço do Azure Stack.  |
    | TenantAdminUser | O administrador para o locatário principal.  |
    | TenantAdminPassword | A senha para o locatário principal.  |
    | FunctionalCategory| Integração, funcional, ou. Confiabilidade. Se você usar vários valores, separe cada valor por uma vírgula.  |

4. Examine os resultados do teste. Para obter informações sobre como ler os resultados do teste, consulte [monitorar testes](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Próximas etapas

 - Para saber mais sobre [validação do Azure Stack como um serviço](https://docs.microsoft.com/azure/azure-stack/partner).
 - Para saber mais sobre o PowerShell no Azure Stack, consulte o [módulo do Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) referência.