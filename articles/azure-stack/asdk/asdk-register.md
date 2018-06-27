---
title: Registrar o ASDK com o Azure | Microsoft Docs
description: Descreve como registrar a pilha do Azure com o Azure para habilitar a distribuição do marketplace e o relatório de uso.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 08a300d0e2d1565428f282a2073d91b5dd08c060
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016992"
---
# <a name="azure-stack-registration"></a>Registro de pilha do Azure
Você pode registrar a instalação do Kit de desenvolvimento na pilha do Azure (ASDK) com o Azure para baixar itens do marketplace do Azure e configurar dados de comércio relatadas à Microsoft. Registro é necessário para dar suporte a funcionalidade completa da pilha do Azure, incluindo a distribuição do marketplace. Registro é recomendado porque ela permite que você teste funcionalidades importantes da pilha do Azure como distribuição de mercado e relatórios de uso. Depois de registrar a pilha do Azure, o uso é relatado para comércio do Azure. Você poderá ver isso na assinatura usado para registro. No entanto, usuários ASDK não são cobrados para qualquer uso que eles relatam.

Se você não registrar seu ASDK, talvez você veja uma **ativação necessária** alerta de aviso que o aconselha a registrar seu Kit de desenvolvimento de pilha do Azure. Esse comportamento é esperado.

## <a name="prerequisites"></a>Pré-requisitos
Antes de usar estas instruções para registrar o ASDK com o Azure, certifique-se de que você instalou o Azure PowerShell de pilha e baixado as ferramentas de pilha do Azure conforme descrito no [configuração pós-implantação](asdk-post-deploy.md) artigo.

Além disso, o modo de linguagem do PowerShell deve ser definido como **FullLanguageMode** no computador usado para registrar o ASDK com o Azure. Para verificar se o modo de linguagem atual está definido como completo, abra uma janela do PowerShell com privilégios elevados e execute os seguintes comandos do PowerShell:

```powershell
$ExecutionContext.SessionState.LanguageMode
```

Certifique-se de que a saída retorna **FullLanguageMode**. Se qualquer outro modo de linguagem é retornado, registro precisa ser executado em outro computador ou o modo de idioma precisa ser definido como **FullLanguageMode** antes de continuar.

## <a name="register-azure-stack-with-azure"></a>Registrar a pilha do Azure com o Azure
Siga estas etapas para registrar o ASDK com o Azure.

> [!NOTE]
> Todas essas etapas devem ser executadas em um computador que tenha acesso ao ponto de extremidade com privilégios. Para ASDK, que é o computador de host do kit de desenvolvimento.

1. Abra um console do PowerShell como administrador.  

2. Execute os seguintes comandos do PowerShell para registrar sua instalação ASDK com o Azure. Você precisará fazer logon sua assinatura do Azure e a instalação local do ASDK. Se você não tiver uma assinatura do Azure, você pode [criar uma conta gratuita do Azure aqui](https://azure.microsoft.com/free/?b=17.06). Registrando a pilha do Azure, incorre em sem custo na sua assinatura do Azure.

  ```powershell
  # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
  Add-AzureRmAccount -EnvironmentName "AzureCloud"

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  #Register Azure Stack
  $AzureContext = Get-AzureRmContext
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01 `
      -BillingModel Development
  ```
3. Quando o script for concluído, você verá esta mensagem: **seu ambiente agora é registrado e ativada usando os parâmetros fornecidos.**

    ![](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>Verifique se que o registro foi bem-sucedido
Siga estas etapas para verificar se o registro ASDK com o Azure foi bem-sucedida.

1. Entrar para o [do portal de administração do Azure pilha](https://adminportal.local.azurestack.external).

2. Clique em **Marketplace gerenciamento** > **adicionar do Azure**.

    ![](media/asdk-register/2.PNG)

3. Se você vir uma lista de itens disponíveis do Azure, a ativação foi bem-sucedida.

    ![](media/asdk-register/3.PNG)

## <a name="next-steps"></a>Próximas etapas
[Adicionar um item do marketplace de pilha do Azure](.\.\azure-stack-marketplace.md)
