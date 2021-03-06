---
title: Validar a identidade do Azure para o Azure Stack | Microsoft Docs
description: Use o verificador de preparação do Azure Stack para validar a identidade do Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: b6a859f1104be8650b95ac789f3da606fb1b70f6
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486675"
---
# <a name="validate-azure-identity"></a>Validar a identidade do Azure

Use a ferramenta de verificador de preparação do Azure Stack (**AzsReadinessChecker**) para validar que seu Azure Active Directory (Azure AD) está pronta para uso com o Azure Stack. Valide sua solução de identidade do Azure antes de começar uma implantação do Azure Stack.  

Valida o verificador de preparação:

- Azure Active Directory (Azure AD) como um provedor de identidade para o Azure Stack.
- Conta do Azure AD que você planeja usar pode entrar como um administrador global do Active Directory do Azure.

A validação garante que seu ambiente está preparado para o Azure Stack armazenar informações sobre usuários, aplicativos, grupos e entidades de serviço do Azure Stack no Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Obter a ferramenta de verificação da prontidão

Baixar a versão mais recente da ferramenta de verificador de preparação do Azure Stack (AzsReadinessChecker) a [da Galeria do PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são necessários:

**O computador no qual a ferramenta é executada:**

- Windows 10 ou Windows Server 2016, a conectividade com a internet.
- PowerShell 5.1 ou posterior. Para verificar sua versão, execute o seguinte comando do PowerShell e, em seguida, examine os **principais** versão e **secundárias** versões:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [PowerShell configurado para o Azure Stack](azure-stack-powershell-install.md).
- A versão mais recente do [Verificador de preparação do Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) ferramenta.

**Ambiente do Active Directory do Azure:**

- Identifique a conta do Azure AD para usar para o Azure Stack e verifique se que ele é um administrador global do Azure Active Directory.
- Identificar o nome do locatário do Azure AD. O nome do locatário deve ser o nome de domínio primário para o Azure Active Directory; Por exemplo, **contoso.onmicrosoft.com**.
- Identifica o ambiente do Azure que você usará. Valores com suporte para o parâmetro de nome de ambiente são **AzureCloud**, **AzureChinaCloud**, ou **AzureUSGovernment**, dependendo de qual assinatura do Azure que você usar.

## <a name="steps-to-validate-azure-identity"></a>Etapas para validar a identidade do Azure

1. Em um computador que atenda aos pré-requisitos, abra um prompt de comando elevado do PowerShell e, em seguida, execute o seguinte comando para instalar **AzsReadinessChecker**:  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. No prompt do PowerShell, execute o seguinte comando para definir **$serviceAdminCredential** como o administrador de serviço para seu locatário do AD do Azure.  Substitua **serviceadmin\@contoso.onmicrosoft.com** com seu nome de conta e locatário:

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. No prompt do PowerShell, execute o seguinte comando para iniciar a validação do AD do Azure:

   - Especifique o valor de nome de ambiente para **AzureEnvironment**. Valores com suporte para o parâmetro de nome de ambiente são **AzureCloud**, **AzureChinaCloud**, ou **AzureUSGovernment**, dependendo de qual assinatura do Azure que você usar.
   - Substitua **contoso.onmicrosoft.com** pelo nome do locatário do Active Directory do Azure.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Depois que a ferramenta é executada, examine a saída. Confirme o status é **Okey** para requisitos de instalação. Uma validação bem-sucedida é exibida como a imagem a seguir:

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```

## <a name="report-and-log-file"></a>Arquivo de log e relatório

A cada hora de validação é executada, ela registra resultados a serem **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. Exibe o local desses arquivos com os resultados de validação no PowerShell.

Esses arquivos podem ajudá-lo a compartilhar o status de validação antes de implantar o Azure Stack ou investigar problemas de validação. Os dois arquivos persistirem os resultados de cada verificação de validação subsequente. O relatório fornece sua confirmação da equipe de implantação da configuração de identidade. O arquivo de log pode ajudar sua equipe de implantação ou suporte a investigar problemas de validação.

Por padrão, os dois arquivos são gravados **C:\Users\<nome de usuário > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Use o **- OutputPath** ***&lt;caminho&gt;*** parâmetro no final da linha de comando executar para especificar um local diferente do relatório.
- Use o **- CleanReport** parâmetro no final do comando para limpar as informações sobre as execuções anteriores da ferramenta de execução **AzsReadinessCheckerReport.json**.

Para obter mais informações, consulte [relatório de validação do Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Falha na validação

Se uma verificação de validação falhar, detalhes sobre a falha será exibido na janela do PowerShell. A ferramenta também registra informações no arquivo AzsReadinessChecker.log.

Os exemplos a seguir fornecem orientação sobre as falhas comuns de validação.

### <a name="expired-or-temporary-password"></a>Senha expirada ou temporária

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Causa** -a conta não pode entrar porque a senha expirou ou é temporária.

**Resolução** - o no PowerShell, execute o comando a seguir e, em seguida, siga os prompts para redefinir a senha:

```powershell
Login-AzureRMAccount
```

Como alternativa, entrar para o [portal do Azure](https://portal.azure.com) como o proprietário da conta e o usuário serão forçado a alterar a senha.

### <a name="unknown-user-type"></a>Tipo de usuário desconhecido 
 
```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Causa** -a conta não pode entrar no Azure Active Directory especificado (**AADDirectoryTenantName**). Neste exemplo, **AzureChinaCloud** é especificado como o **AzureEnvironment**.

**Resolução** -confirme que a conta é válida para o ambiente do Azure especificado. No PowerShell, execute o seguinte comando para verificar se que a conta é válida para um ambiente específico:

```powershell
Login-AzureRmAccount –EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>Conta não é um administrador

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Causa** -Embora a conta pode entrar com êxito, a conta não é um administrador do Azure Active Directory (**AADDirectoryTenantName**).  

**Resolução** -entrar no [portal do Azure](https://portal.azure.com) como o proprietário da conta, acesse **do Azure Active Directory**, em seguida, **usuários**, em seguida, **selecionar o Usuário**, em seguida, **função de diretório**e, em seguida, verifique se o usuário é um **Administrador Global**. Se a conta for um **usuário**, acesse **Azure Active Directory** > **nomes de domínio personalizados**e confirme que o nome fornecido para  **AADDirectoryTenantName** está marcado como o nome de domínio primário para este diretório. Neste exemplo, que é **contoso.onmicrosoft.com**.

O Azure Stack requer que o nome de domínio é o nome de domínio primário.

## <a name="next-steps"></a>Próximas etapas

[Validar o registro do Azure](azure-stack-validate-registration.md)  
[Exibir o relatório de preparação](azure-stack-validation-report.md)  
[Considerações sobre a integração geral do Azure Stack](azure-stack-datacenter-integration.md)  
