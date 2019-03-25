---
title: Validar o registro do Azure para o Azure Stack | Microsoft Docs
description: Use o verificador de preparação do Azure Stack para validar o registro do Azure.
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
ms.openlocfilehash: a777fc1d9052eb58bbebd319fe6cc7f42a09cb9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403605"
---
# <a name="validate-azure-registration"></a>Validar o registro do Azure

Use a ferramenta de verificador de preparação do Azure Stack (**AzsReadinessChecker**) para validar que a sua assinatura do Azure está pronta para uso com o Azure Stack. Valide o registro antes de começar uma implantação do Azure Stack. O verificador de preparação valida que:

- A assinatura do Azure que você usa é um tipo com suporte. Assinaturas devem ser um provedor de serviços de nuvem (CSP) ou o Enterprise Agreement (EA).
- A conta usada para registrar sua assinatura com o Azure pode entrar no Azure e é um proprietário da assinatura.

Para obter mais informações sobre o registro do Azure Stack, consulte [registrar o Azure Stack com o Azure](azure-stack-registration.md).

## <a name="get-the-readiness-checker-tool"></a>Obter a ferramenta de verificação da prontidão

Baixe a versão mais recente do **AzsReadinessChecker** da [Galeria do PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são necessários:

**O computador no qual a ferramenta é executada:**

- Windows 10 ou Windows Server 2016, a conectividade com a internet.
- PowerShell 5.1 ou posterior. Para verificar sua versão, execute o seguinte cmdlet do PowerShell e, em seguida, examine os **principais** e **secundárias** versões:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [PowerShell configurado para o Azure Stack](azure-stack-powershell-install.md).
- A versão mais recente do [Verificador de preparação do Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker).  

**Ambiente do Active Directory do Azure:**

- Identificar o nome de usuário e senha para uma conta que seja um proprietário da assinatura do Azure, que você usará com o Azure Stack.  
- Identifique a ID da assinatura para a assinatura do Azure que você usará.
- Identificar o **AzureEnvironment** será usado. Valores com suporte para o parâmetro de nome de ambiente são **AzureCloud**, **AzureChinaCloud**, ou **AzureUSGovernment**, dependendo de qual assinatura do Azure que você está usando o.

## <a name="steps-to-validate-azure-registration"></a>Etapas para validar o registro do Azure

1. Em um computador que atenda aos pré-requisitos, abra um prompt elevado do PowerShell e, em seguida, execute o seguinte comando para instalar **AzsReadinessChecker**:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. No prompt do PowerShell, execute o seguinte comando para definir `$registrationCredential` como a conta que é o proprietário da assinatura. Substitua `subscriptionowner@contoso.onmicrosoft.com` com seu nome de conta e locatário:

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > Como um CSP, ao usar uma assinatura de serviços compartilhados ou IUR, você deve fornecer as credenciais de um usuário do AAD que respectivo. Normalmente, isso será semelhante a `subscriptionowner@iurcontoso.onmicrosoft.com`. Esse usuário deve ter as credenciais apropriadas, conforme descrito na etapa anterior.

3. No prompt do PowerShell, execute o seguinte para definir `$subscriptionID` como a assinatura do Azure para usar. Substitua `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` pela sua ID de assinatura:

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. No prompt do PowerShell, execute o seguinte comando para iniciar a validação de sua assinatura:

   - Especifique o valor para `AzureEnvironment` como **AzureCloud**, **AzureGermanCloud**, ou **AzureChinaCloud**.  
   - Forneça o administrador do Active Directory do Azure e o nome do locatário do Active Directory do Azure.

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. Depois que a ferramenta é executada, examine a saída. Confirme que o status está correto para entrar e os requisitos de registro. Saída de validação bem-sucedida é semelhante ao exemplo a seguir:

   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>Arquivo de log e relatório

A cada hora de validação é executada, ela registra resultados a serem **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. Exibe o local desses arquivos, juntamente com os resultados da validação no PowerShell.

Esses arquivos podem ajudá-lo a compartilhar o status de validação antes de implantar o Azure Stack ou investigar problemas de validação. Os dois arquivos persistirem os resultados de cada verificação de validação subsequente. O relatório fornece sua confirmação da equipe de implantação da configuração de identidade. O arquivo de log pode ajudar sua equipe de implantação ou suporte a investigar problemas de validação.

Por padrão, os dois arquivos são gravados **C:\Users\<nome de usuário > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Use o **- OutputPath** ***&lt;caminho&gt;*** parâmetro no final da linha de comando executar para especificar um local diferente do relatório.
- Use o **- CleanReport** parâmetro no final do comando para limpar as informações sobre as execuções anteriores da ferramenta de execução **AzsReadinessCheckerReport.json**.

Para obter mais informações, consulte [relatório de validação do Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Falha na validação

Se uma verificação de validação falhar, detalhes sobre a falha será exibido na janela do PowerShell. A ferramenta também registra informações para o **AzsReadinessChecker.log** arquivo.

Os exemplos a seguir fornecem orientação sobre falhas de validação comuns:

### <a name="user-must-be-an-owner-of-the-subscription"></a>Usuário deve ser um proprietário da assinatura

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Causa** -a conta não é um administrador da assinatura do Azure.

**Resolução** -usar uma conta que seja um administrador da assinatura do Azure será cobrado pelo uso da implantação do Azure Stack.

### <a name="expired-or-temporary-password"></a>Senha expirada ou temporária

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change Password.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Causa** -a conta não é possível entrar porque a senha expirou ou é temporária.

**Resolução** - o no PowerShell, execute o seguinte comando e siga os prompts para redefinir a senha.

```powershell
Login-AzureRMAccount
```

Como alternativa, entrar para o [portal do Azure](https://portal.azure.com) como o proprietário da conta e o usuário serão forçado a alterar a senha.

### <a name="unknown-user-type"></a>Tipo de usuário desconhecido  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Causa** -a conta não pode entrar no ambiente do Azure Active Directory especificado. Neste exemplo, **AzureChinaCloud** é especificado como o **AzureEnvironment**.  

**Resolução** -confirme que a conta é válida para o ambiente do Azure especificado. No PowerShell, execute o seguinte comando para verificar se que a conta é válida para um ambiente específico:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>Próximas etapas

- [Validar a identidade do Azure](azure-stack-validate-identity.md)
- [Exibir o relatório de preparação](azure-stack-validation-report.md)
- [Considerações sobre a integração geral do Azure Stack](azure-stack-datacenter-integration.md)
