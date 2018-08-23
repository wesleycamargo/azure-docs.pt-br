---
title: Registre o ASDK com o Azure | Microsoft Docs
description: Descreve como registrar o Azure Stack com o Azure para habilitar o relatório de uso e distribuição de mercado.
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
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 95ab06685452f647884bf92f110e3ab56f3c2714
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2018
ms.locfileid: "42139405"
---
# <a name="azure-stack-registration"></a>Registro de pilha do Azure
Você pode registrar sua instalação do Kit de desenvolvimento na pilha do Azure (ASDK) com o Azure para baixar itens do marketplace do Azure e configurar dados de comércio relatórios de volta para a Microsoft. Registro é necessário para dar suporte à funcionalidade completa do Azure Stack, incluindo a sindicalização do marketplace. Registro é recomendado porque ele permite que você teste funcionalidades importantes da pilha do Azure, como o relatório de uso e distribuição de mercado. Depois de registrar o Azure Stack, o uso é relatado para comércio do Azure. Você pode vê-lo sob a assinatura que você usou para o registro. No entanto, os usuários ASDK não são cobrados por qualquer uso que eles relatam.

Se você não registrou sua ASDK, talvez você veja uma **ativação necessária** alerta de aviso que recomenda que você registre seu Kit de desenvolvimento do Azure Stack. Esse comportamento é esperado.

## <a name="prerequisites"></a>Pré-requisitos
Antes de usar estas instruções para registrar o ASDK com o Azure, certifique-se de que você instalou o Azure Stack PowerShell e baixei as ferramentas do Azure Stack, conforme descrito na [configuração pós-implantação](asdk-post-deploy.md) artigo.

Além disso, o modo de linguagem do PowerShell deve ser definido como **FullLanguageMode** no computador usado para registrar o ASDK com o Azure. Para verificar se o modo de linguagem atual está definido como completo, abra uma janela do PowerShell com privilégios elevados e execute os seguintes comandos do PowerShell:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Certifique-se a saída retorna **FullLanguageMode**. Se qualquer outro modo de linguagem é retornado, registro precisa ser executado em outro computador ou o modo de linguagem precisará ser definido como **FullLanguageMode** antes de continuar.

## <a name="register-azure-stack-with-azure"></a>Registre-se a pilha do Azure com o Azure
Siga estas etapas para registrar o ASDK com o Azure.

> [!NOTE]
> Todas essas etapas devem ser executadas em um computador que tem acesso ao ponto de extremidade com privilégios. Para ASDK, que é o computador de host do kit de desenvolvimento.

1. Abra um console do PowerShell como administrador.  

2. Execute os seguintes comandos do PowerShell para registrar sua instalação ASDK com o Azure. Você precisará entrar em sua assinatura do Azure e a instalação ASDK local. Se você não tiver uma assinatura do Azure, você pode [criar uma conta gratuita do Azure aqui](https://azure.microsoft.com/free/?b=17.06). Registrar o Azure Stack incorre em sua assinatura do Azure sem custo.  

Se você estiver executando o script de registro em mais de uma instância do Azure Stack usando a mesma ID de assinatura do Azure, defina um nome exclusivo para o registro quando você executa o **AzsRegistration conjunto** cmdlet. O **RegistrationName** parâmetro tem um valor padrão de **AzureStackRegistration**. No entanto, se você usar o mesmo nome em mais de uma instância do Azure Stack, o script falhará.

  ```PowerShell  
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
    -BillingModel Development `
    -RegistrationName "<Unique-name>"
  ```
3. Quando o script for concluído, você deverá ver esta mensagem: **seu ambiente agora está registrado e ativada usando os parâmetros fornecidos.**

    ![Seu ambiente agora está registrado](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>Verifique se que o registro foi bem-sucedido
Siga estas etapas para verificar se o registro ASDK com o Azure foi bem-sucedida.

1. Entrar para o [portal de administração do Azure Stack](https://adminportal.local.azurestack.external).

2. Clique em **gerenciamento do Marketplace** > **adicione do Azure**.

    ![](media/asdk-register/2.PNG)

3. Se você vir uma lista de itens disponíveis do Azure, a ativação foi bem-sucedida.

    ![](media/asdk-register/3.PNG)

## <a name="next-steps"></a>Próximas etapas
[Adicionar um item do marketplace do Azure Stack](.\.\azure-stack-marketplace.md)
