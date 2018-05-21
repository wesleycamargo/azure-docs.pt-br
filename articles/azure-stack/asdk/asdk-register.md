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
ms.date: 05/17/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: eb1f939f76c3528f05a9002b6365359fb6599aa2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="azure-stack-registration"></a>Registro de pilha do Azure
Você pode registrar a instalação do Kit de desenvolvimento na pilha do Azure (ASDK) com o Azure para baixar itens do marketplace do Azure e configurar dados de comércio relatadas à Microsoft. Registro é necessário para dar suporte a funcionalidade completa da pilha do Azure, incluindo a distribuição do marketplace. Registro é recomendado porque ela permite que você teste funcionalidades importantes da pilha do Azure como distribuição de mercado e relatórios de uso. Depois de registrar a pilha do Azure, o uso é relatado para comércio do Azure. Você poderá ver isso na assinatura usado para registro. No entanto, usuários ASDK não são cobrados para qualquer uso que eles relatam.

Se você não registrar seu ASDK, talvez você veja uma **ativação necessária** alerta de aviso que o aconselha a registrar seu Kit de desenvolvimento de pilha do Azure. Esse comportamento é esperado.

## <a name="register-azure-stack-with-azure"></a>Registrar a pilha do Azure com o Azure 
Siga estas etapas para registrar o ASDK com o Azure.

> [!NOTE]
> Todas essas etapas devem ser executadas em um computador que tenha acesso ao ponto de extremidade com privilégios. Para ASDK, que é o computador de host do kit de desenvolvimento. 

Antes de usar estas etapas para registrar o ASDK com o Azure, certifique-se de que você instalou o Azure PowerShell de pilha e baixado as ferramentas de pilha do Azure conforme descrito no [configuração pós-implantação](asdk-post-deploy.md) artigo. 

1. Abra um console do PowerShell como administrador.  

2. Execute os seguintes comandos de PowerShell para registrar sua instalação ASDK com o Azure (você precisará fazer logon sua assinatura do Azure e a instalação local do ASDK):

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
        -BillingModel Development

3. When the script completes, you should see this message: **Your environment is now registered and activated using the provided parameters.**

    ![](media/asdk-register/1.PNG) 

## Verify the registration was successful
Follow these steps to verify that the ASDK registration with Azure was successful.

1. Sign in to the [Azure Stack administration portal](https://adminportal.local.azurestack.external).

2. Click **Marketplace Management** > **Add from Azure**.

    ![](media/asdk-register/2.PNG) 

3. If you see a list of items available from Azure, your activation was successful.

    ![](media/asdk-register/3.PNG) 

## Next steps
[Add an Azure Stack marketplace item](asdk-marketplace-item.md)
