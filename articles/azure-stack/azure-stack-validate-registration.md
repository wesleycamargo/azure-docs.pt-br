---
title: Validar o registro do Azure para o Azure Stack | Microsoft Docs
description: Use o verificador de preparação do Azure Stack para validar o registro do Azure.
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
ms.date: 06/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 57869de8a99c65810da0c75f81c75d93eac88412
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "35998340"
---
# <a name="validate-azure-registration"></a>Validar o registro do Azure 
Use a ferramenta de verificador de preparação do Azure Stack (AzsReadinessChecker) para validar que a sua assinatura do Azure está pronta para uso com o Azure Stack. Valide o registro antes de começar uma implantação do Azure Stack. Valida o verificador de preparação:
- A assinatura do Azure que você usa é um tipo com suporte. Assinaturas devem ser um provedor de serviços de nuvem (CSP) ou o Enterprise Agreement (EA). 
- A conta usada para registrar sua assinatura com o Azure pode entrar no Azure e é um proprietário da assinatura. 

Para obter mais informações sobre o registro do Azure Stack, consulte [registrar o Azure Stack com o Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Obter a ferramenta de verificação da prontidão
Baixar a versão mais recente da ferramenta de verificador de preparação do Azure Stack (AzsReadinessChecker) está disponível no [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Pré-requisitos
Os seguintes pré-requisitos devem estar em vigor.

**O computador em que a ferramenta é executada:**
 - Windows 10 ou Windows Server 2016, a conectividade com a internet.
 - PowerShell 5.1 ou posterior. Para verificar sua versão, execute o seguinte comando do PowerShell e, em seguida, examine os *principais* versão e *secundárias* versões:  

    >`$PSVersionTable.PSVersion` 
 - Configure [PowerShell para o Azure Stack](azure-stack-powershell-install.md). 
 - Baixe a versão mais recente do [Verificador de preparação do Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) ferramenta.  

**Ambiente do Active Directory do Azure:**
 - Identificar o nome de usuário e senha para uma conta que seja um proprietário da assinatura do Azure, que você usará com o Azure Stack.  
 - Identifique a ID da assinatura para a assinatura do Azure que você usará. 
 - Identificar o AzureEnvironment você usará: *AzureCloud*, *AzureGermanCloud*, ou *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Validar o registro do Azure
1. Em um computador que atenda aos pré-requisitos, abra um prompt do PowerShell administrativo e, em seguida, execute o seguinte comando para instalar o AzsReadinessChecker.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. No prompt do PowerShell, execute o seguinte para definir *$registrationCredential* como a conta que é o proprietário da assinatura.   Substitua *subscriptionowner@contoso.onmicrosoft.com* com sua conta e o locatário. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. No prompt do PowerShell, execute o seguinte para definir *$subscriptionID* como a assinatura do Azure que você usará. Substitua *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* com sua própria ID de assinatura.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. No prompt do PowerShell, execute o seguinte para iniciar a validação de sua assinatura 
   - Especifique o valor para AzureEnvironment como *AzureCloud*, *AzureGermanCloud*, ou *AzureChinaCloud*.  
   - Forneça o administrador do Active Directory do Azure e seu nome de locatário do Azure Active Directory. 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Depois que a ferramenta é executada, examine a saída. Confirme que o status é Okey para logon e os requisitos de registro. Uma validação bem-sucedida é exibida como a imagem a seguir:  
![executar a validação](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>Arquivo de log e relatório
A cada hora de validação é executada, ela registra resultados a serem **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. Exibe o local desses arquivos com os resultados de validação no PowerShell. 

Esses arquivos podem ajudá-lo a compartilhar o status de validação antes de implantar o Azure Stack ou investigar problemas de validação. Os dois arquivos persistirem os resultados de cada verificação de validação subsequente. O relatório fornece sua confirmação da equipe de implantação da configuração de identidade. O arquivo de log pode ajudar sua equipe de implantação ou suporte a investigar problemas de validação. 

Por padrão, os dois arquivos são gravados *C:\Users\<nome de usuário > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Use o **- OutputPath** ***&lt;caminho&gt;*** parâmetro no final da linha de comando executar para especificar um local diferente do relatório.   
 - Use o **- CleanReport** parâmetro no final do comando para limpar as informações de execução *AzsReadinessCheckerReport.json*.  sobre as execuções anteriores da ferramenta. Para obter mais informações, [relatório de validação do Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Falha na validação
Se uma verificação de validação falhar, detalhes sobre a falha será exibido na janela do PowerShell. A ferramenta também registra informações para o AzsReadinessChecker.log.

Os exemplos a seguir fornecem orientação sobre as falhas comuns de validação.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Usuário deve ser um proprietário da assinatura   
![proprietário da assinatura](./media/azure-stack-validate-registration/subscription-owner.png)
**causa** -a conta não é um administrador da assinatura do Azure.   

**Resolução** -usar uma conta que seja um administrador da assinatura do Azure será cobrado pelo uso da implantação do Azure Stack.


### <a name="expired-or-temporary-password"></a>Senha expirada ou temporária 
![senha expirada](./media/azure-stack-validate-registration/expired-password.png)
**causa** -a conta não pode fazer logon porque a senha expirou ou é temporária.     

**Resolução** - no PowerShell executar e siga os prompts para redefinir a senha. 
  > `Login-AzureRMAccount` 

Como alternativa, faça logon no https://portal.azure.com como a conta e o usuário serão forçados a alterar a senha.


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>Não há suporte para contas da Microsoft para o registro  
![conta sem suporte](./media/azure-stack-validate-registration/unsupported-account.png)
**causa** -Microsoft de uma conta (como o Outlook.com ou Hotmail.com) foi especificada.  Não há suporte para essas contas.

**Resolução** -usar uma conta e assinatura de um provedor de serviços de nuvem (CSP) ou o Enterprise Agreement (EA). 


### <a name="unknown-user-type"></a>Tipo de usuário desconhecido  
![usuário desconhecido](./media/azure-stack-validate-registration/unknown-user.png)
**causa** -a conta não pode fazer logon para o ambiente do Azure Active Directory especificado. Neste exemplo, *AzureChinaCloud* é especificado como o *AzureEnvironment*.  

**Resolução** -confirme que a conta é válida para o ambiente do Azure especificado. No PowerShell, execute o seguinte para verificar se que a conta é válida para um ambiente específico.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Próximas etapas
[Validar a identidade do Azure](azure-stack-validate-identity.md)
[exibir o relatório de preparação](azure-stack-validation-report.md)
[considerações sobre a integração geral do Azure Stack](azure-stack-datacenter-integration.md)

