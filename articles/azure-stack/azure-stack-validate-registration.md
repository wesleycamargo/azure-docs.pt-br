---
title: Validar o registro do Azure para a pilha do Azure | Microsoft Docs
description: Use o verificador de preparação de pilha do Azure para validar o registro do Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 84eb1c08cc3f9ef104e2eb0b96ed397315c3f374
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937835"
---
# <a name="validate-azure-registration"></a>Validar o registro do Azure 
Use a ferramenta de verificador de preparação de pilha do Azure (AzsReadinessChecker) para validar a assinatura do Azure está pronta para uso com a pilha do Azure. Valide o registro antes de começar uma implantação de pilha do Azure. Valida o verificador de preparação:
- A assinatura do Azure que você usa é um tipo com suporte. As assinaturas devem estar em um provedor de serviços de nuvem (CSP) ou EA (Enterprise Agreement). 
- A conta usada para registrar sua assinatura do Azure pode fazer logon Azure e é um proprietário da assinatura. 

Para obter mais informações sobre o registro de pilha do Azure, consulte [registrar pilha do Azure com o Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Obter o verificador de preparação
Baixar a versão mais recente da ferramenta Verificador de preparação de pilha do Azure (AzsReadinessChecker) está disponível em [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Pré-requisitos
Os seguintes pré-requisitos devem estar em vigor.

**O computador onde a ferramenta é executada:**
 - Windows 10 ou Windows Server 2016, a conectividade com a internet.
 - PowerShell 5.1 ou posterior. Para verificar a versão, execute o seguinte cmd do PowerShell e, em seguida, examine o *principais* versão e *secundária* versões:  

    >`$PSVersionTable.PSVersion` 
 - Configurar [PowerShell para Azure pilha](azure-stack-powershell-install.md). 
 - Baixar a versão mais recente do [Verificador de preparação de pilha do Microsoft Azure](https://aka.ms/AzsReadinessChecker) ferramenta.  

**Ambiente do Active Directory do Azure:**
 - Identificar o nome de usuário e a senha de uma conta que é proprietário da assinatura do Azure que você usará com pilha do Azure.  
 - Identifique a ID da assinatura para a assinatura do Azure que você usará. 
 - Identificar o AzureEnvironment que você usará: *AzureCloud*, *AzureGermanCloud*, ou *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Validar o registro do Azure
1. Em um computador que atenda aos pré-requisitos, abra um prompt do PowerShell administrativo e, em seguida, execute o seguinte comando para instalar o AzsReadinessChecker.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. No prompt do PowerShell, execute o seguinte para definir *$registrationCredential* como a conta que é o proprietário da assinatura.   Substituir *subscriptionowner@contoso.onmicrosoft.com* com sua conta e locatário. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. No prompt do PowerShell, execute o seguinte para definir *$subscriptionID* como a assinatura do Azure que você usará. Substituir *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* com sua ID de assinatura.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. No prompt do PowerShell, execute o seguinte para iniciar a validação de sua assinatura 
   - Especifique o valor para AzureEnvironment como *AzureCloud*, *AzureGermanCloud*, ou *AzureChinaCloud*.  
   - Forneça o administrador do Active Directory do Azure e o nome do locatário do Active Directory do Azure. 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Depois que a ferramenta é executada, examine a saída. Confirme que o status é Okey para o logon e os requisitos de registro. Validação bem-sucedida é exibida como a imagem a seguir:  
![executar a validação](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>Arquivo de log e relatório
Executa a validação de cada vez, ela registra resultados **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. Exibe o local desses arquivos com os resultados de validação no PowerShell. 

Esses arquivos podem ajudá-lo a compartilhar o status de validação antes de implantar o Azure pilha ou investigar problemas de validação. Os dois arquivos mantém os resultados de cada verificação de validação subsequentes. O relatório fornece sua confirmação da equipe de implantação da configuração de identidade. O arquivo de log pode ajudar sua equipe de implantação ou o suporte a investigar problemas de validação. 

Por padrão, os dois arquivos são gravados *C:\Users\<nome de usuário > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Use o **- OutputPath** ***&lt;caminho&gt;*** parâmetro no final da linha de comando de execução para especificar um local diferente do relatório.   
 - Use o **- CleanReport** parâmetro no final do comando para limpar as informações de execução *AzsReadinessCheckerReport.json*.  sobre as execuções anteriores da ferramenta. Para obter mais informações, [relatório de validação de pilha do Azure](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Falhas de validação
Se uma verificação de validação falhar, detalhes sobre a falha são exibidos na janela do PowerShell. A ferramenta também registra informações de AzsReadinessChecker.log.

Os exemplos a seguir fornecem orientação sobre falhas de validação comuns.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Usuário deve ser o proprietário da assinatura   
![proprietário da assinatura](./media/azure-stack-validate-registration/subscription-owner.png)
**causa** -a conta não é um administrador da assinatura do Azure.   

**Resolução** -usar uma conta que seja um administrador da assinatura do Azure que será cobrado pelo uso da implantação do Azure pilha.


### <a name="expired-or-temporary-password"></a>Senha expirada ou temporária 
![senha expirada](./media/azure-stack-validate-registration/expired-password.png)
**causa** -a conta não é possível fazer logon porque a senha expirou ou é temporária.     

**Resolução** - no PowerShell executar e siga os prompts para redefinir a senha. 
  > `Login-AzureRMAccount` 

Como alternativa, o logon em https://portal.azure.com como a conta e o usuário serão forçados a alterar a senha.


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>Não há suporte para contas da Microsoft para o registro  
![conta sem suporte](./media/azure-stack-validate-registration/unsupported-account.png)
**causa** -conta de um Microsoft (como o Outlook.com ou Hotmail.com) foi especificado.  Não há suporte para essas contas.

**Resolução** -usar uma conta e assinatura a partir de um provedor de serviços de nuvem (CSP) ou EA (Enterprise Agreement). 


### <a name="unknown-user-type"></a>Tipo de usuário desconhecido  
![usuário desconhecido](./media/azure-stack-validate-registration/unknown-user.png)
**causa** -a conta não pode fazer logon para o ambiente do Active Directory do Azure especificado. Neste exemplo, *AzureChinaCloud* é especificado como o *AzureEnvironment*.  

**Resolução** -confirme se a conta é válida para o ambiente do Azure especificado. No PowerShell, execute o seguinte para verificar se que a conta é válida para um ambiente específico.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Próximas etapas
[Validar a identidade do Azure](azure-stack-validate-identity.md)
[exibir o relatório de preparação](azure-stack-validation-report.md)
[considerações sobre a integração geral do Azure pilha](azure-stack-datacenter-integration.md)

