---
title: Validar a identidade do Azure para a pilha do Azure | Microsoft Docs
description: Use o verificador de preparação de pilha do Azure para validar a identidade do Azure.
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
ms.openlocfilehash: fe5e7281cbe01ad11f667729df344f91ef1327d2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="validate-azure-identity"></a>Validar a identidade do Azure 
Use a ferramenta de verificador de preparação de pilha do Azure (AzsReadinessChecker) para validar que o Azure Active Directory (AD do Azure) está pronto para uso com a pilha do Azure. Valide sua solução de identidade do Azure antes de começar uma implantação de pilha do Azure.  

Valida o verificador de preparação:
 - Azure Active Directory (AD do Azure) como um provedor de identidade para a pilha do Azure.
 - A conta do AD do Azure que você planeja usar pode fazer logon como um administrador global do Active Directory do Azure. 

A validação garante que o seu ambiente está preparado para a pilha do Azure armazenar informações sobre usuários, aplicativos, grupos e entidades de serviço da pilha do Azure no AD do Azure.

## <a name="get-the-readiness-checker-tool"></a>Obter o verificador de preparação
Baixar a versão mais recente da ferramenta Verificador de preparação de pilha do Azure (AzsReadinessChecker) da [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Pré-requisitos
Os seguintes pré-requisitos devem estar em vigor.

**O computador onde a ferramenta é executada:**
 - Windows 10 ou Windows Server 2016, a conectividade com a internet.
 - PowerShell 5.1 ou posterior. Para verificar a versão, execute o seguinte cmd do PowerShell e, em seguida, examine o *principais* versão e *secundária* versões:  

   > `$PSVersionTable.PSVersion`
 - Configurar [PowerShell para Azure pilha](azure-stack-powershell-install.md). 
 - A versão mais recente do [Verificador de preparação de pilha do Microsoft Azure](https://aka.ms/AzsReadinessChecker) ferramenta.

**Ambiente do Active Directory do Azure:**
 - Identificar a conta do AD do Azure é usado para a pilha do Azure e certifique-se de que ele seja um Administrador Global do Active Directory do Azure.
 - Identificar o nome do locatário do Azure AD. O nome do locatário deve ser o *primário* nome de domínio do Active Directory do Azure. Por exemplo, *contoso.onmicrosoft.com*. 
 - Identificar o AzureEnvironement que você usará: *AzureCloud*, *AzureGermanCloud*, ou *AzureChinaCloud*.

## <a name="validate-azure-identity"></a>Validar a identidade do Azure 
1. Em um computador que atenda aos pré-requisitos, abra um prompt do PowerShell administrativo e, em seguida, execute o seguinte comando para instalar o AzsReadinessChecker:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. No prompt do PowerShell, execute o seguinte para definir *$serviceAdminCredential* como o administrador de serviço para seu locatário do AD do Azure.  Substituir *serviceadmin@contoso.onmicrosoft.com* com sua conta e locatário. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. No prompt do PowerShell, execute o seguinte para iniciar a validação do AD do Azure. 
   - Especifique o valor para AzureEnvironment como *AzureCloud*, *AzureGermanCloud*, ou *AzureChinaCloud*.  
   - Especifique o nome do locatário do Azure Active Directory para substituir *contoso.onmicrosoft.com*. 

   > `Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Depois que a ferramenta é executada, examine a saída. Confirme o status é **Okey** para o logon e os requisitos de instalação. Validação bem-sucedida é exibida como a imagem a seguir: 
 
![executar a validação](./media/azure-stack-validate-identity/validation.png)


## <a name="report-and-log-file"></a>Arquivo de log e relatório
Executa a validação de cada vez, ela registra resultados **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. Exibe o local desses arquivos com os resultados de validação no PowerShell.

Esses arquivos podem ajudá-lo a compartilhar o status de validação antes de implantar o Azure pilha ou investigar problemas de validação.  Os dois arquivos mantém os resultados de cada verificação de validação subsequentes. O relatório fornece sua confirmação da equipe de implantação da configuração de identidade. O arquivo de log pode ajudar sua equipe de implantação ou o suporte a investigar problemas de validação. 

Por padrão, os dois arquivos são gravados *C:\Users\<nome de usuário > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Use o **- OutputPath** ***&lt;caminho&gt;*** parâmetro no final da linha de comando de execução para especificar um local diferente do relatório.   
 - Use o **- CleanReport** parâmetro no final do comando para limpar as informações de execução *AzsReadinessCheckerReport.json*.  sobre as execuções anteriores da ferramenta. 

Para obter mais informações, [relatório de validação de pilha do Azure](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Falhas de validação
Se uma verificação de validação falhar, detalhes sobre a falha são exibidos na janela do PowerShell. A ferramenta também registra informações de AzsReadinessChecker.log.

Os exemplos a seguir fornecem orientação sobre falhas de validação comuns.

### <a name="expired-or-temporary-password"></a>Senha expirada ou temporária 
 
![expirado senha](./media/azure-stack-validate-identity/expired-password.png)
**causa** -a conta não é possível fazer logon porque a senha expirou ou é temporária.     

**Resolução** - no PowerShell, execute o seguinte e, em seguida, siga os prompts para redefinir a senha.  
> `Login-AzureRMAccount`

Como alternativa, o logon em https://portal.azure.com como a conta e o usuário serão forçados a alterar a senha.
### <a name="unknown-user-type"></a>Tipo de usuário desconhecido 
 
![usuário desconhecido](./media/azure-stack-validate-identity/unknown-user.png)
**causa** -a conta não pode efetuar logon especificado do Azure Active Directory (AADDirectoryTenantName). Neste exemplo, *AzureChinaCloud* é especificado como o *AzureEnvironment*.

**Resolução** -confirme se a conta é válida para o ambiente do Azure especificado. No PowerShell, execute o seguinte para verificar se a conta é válida para um ambiente específico: logon-AzureRmAccount – EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>Conta não é um administrador 
 
![não administrador](./media/azure-stack-validate-identity/not-admin.png)

**Causa** -embora com êxito, a conta pode fazer logon, a conta não é um administrador do Active Directory do Azure (AADDirectoryTenantName).  

**Resolução** -logon em https://portal.azure.com como a conta, vá para **Active Directory do Azure** > **usuários** > *selecione o usuário*  >  **Função de diretório**e, em seguida, certifique-se de que o usuário é um **Administrador Global**.  Se a conta for um usuário, vá para **Active Directory do Azure** > **domínio personalizado** nomes e confirme que o nome fornecido para *AADDirectoryTenantName* é marcado como o nome de domínio primário para este diretório.  Neste exemplo, que é *contoso.onmicrosoft.com*. 

A pilha do Azure requer que o nome de domínio é o nome de domínio primário.

## <a name="next-steps"></a>Próximas etapas
[Validar o registro do Azure](azure-stack-validate-registration.md)  
[Exibir o relatório de preparação](azure-stack-validation-report.md)  
[Considerações sobre a integração geral do Azure pilha](azure-stack-datacenter-integration.md)  

