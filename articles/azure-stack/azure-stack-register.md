---
title: Registrar a pilha do Azure | Microsoft Docs
description: Registre a pilha do Azure com sua assinatura do Azure.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: erikje
ms.openlocfilehash: 24cde66a132ae2e1ba0eb9b1564915746e5ca448
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Registrar a pilha do Azure com sua assinatura do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode registrar [Azure pilha](azure-stack-poc.md) com o Azure para baixar itens do marketplace do Azure e configurar dados de comércio relatadas à Microsoft. 

> [!NOTE]
>Registro é recomendado porque ela permite que você teste funcionalidades importantes da pilha do Azure, como distribuição de mercado e relatórios de uso. Depois de registrar a pilha do Azure, o uso é relatado para comércio do Azure. Você poderá ver isso na assinatura usado para registro. Usuários do Azure do Kit de desenvolvimento de pilha não são cobrados para qualquer uso que eles relatam.
>


## <a name="get-azure-subscription"></a>Obter assinatura do Azure

Antes de registrar a pilha do Azure com o Azure, você deve ter:

- A ID de assinatura para uma assinatura do Azure. Para obter a ID, entre no Azure, clique em **mais serviços** > **assinaturas**, clique na assinatura que você deseja usar, e, em **Essentials** você pode encontrar o **ID da assinatura**. Na China, Alemanha e assinaturas de nuvem do governo dos EUA não têm suporte no momento.
- O nome de usuário e a senha de uma conta que é proprietário da assinatura (há suporte para contas MSA/2FA).
- O Active Directory do Azure para a assinatura do Azure. Você pode encontrar esse diretório no Azure focalizando seu avatar no canto superior direito do portal do Azure. 
- [Registrado o provedor de recursos do Azure pilha](#register-azure-stack-resource-provider-in-azure).

Se você não tiver uma assinatura do Azure que atende a esses requisitos, você pode [criar uma conta gratuita do Azure aqui](https://azure.microsoft.com/en-us/free/?b=17.06). Registrando a pilha do Azure, incorre em sem custo na sua assinatura do Azure.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Registrar o provedor de recursos da pilha do Azure no Azure
> [!NOTE] 
> Esta etapa só precisa ser preenchida uma vez em um ambiente de pilha do Azure.
>

1. Inicie o ISE do Powershell como administrador.
2. Login para a conta do Azure que é proprietário da assinatura do Azure com o parâmetro - EnvironmentName definido como "AzureCloud".
3. Registre o provedor de recursos do Azure "Microsoft.AzureStack".

Exemplo: 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```


## <a name="register-azure-stack-with-azure"></a>Registrar a pilha do Azure com o Azure

> [!NOTE]
>Todas essas etapas devem ser concluídas no computador host.
>

1. [Instale o PowerShell para Azure pilha](azure-stack-powershell-install.md). 
2. Copie o [RegisterWithAzure.psm1 script](https://go.microsoft.com/fwlink/?linkid=842959) para uma pasta (como C:\Temp).
3. Iniciar o ISE do PowerShell como administrador e importe o módulo RegisterWithAzure.    
4. O script RegisterWithAzure.psm1, execute o módulo AzsRegistration adicionar. Substitua os espaços reservados a seguir: 
    - *YourCloudAdminCredential* é um objeto do PowerShell que contém as credenciais de domínio local para o domain\cloudadmin (para o kit de desenvolvimento, isso é azurestack\cloudadmin).
    - *YourAzureSubscriptionID* é a ID da assinatura do Azure que você deseja usar para registrar a pilha do Azure.
    - *YourAzureDirectoryTenantName* é o nome do diretório do locatário do Azure associado à sua assinatura do Azure. O recurso de registro será criado nesse locatário de diretório. 
    - *YourPrivilegedEndpoint* é o nome do [ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md).

    ```powershell
    Add-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -AzureDirectoryTenantName $YourAzureDirectoryTenantName  -AzureSubscriptionId $YourAzureSubscriptionId -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Development 
    ```
5. Na janela pop-up de logon, insira suas credenciais de assinatura do Azure.

## <a name="verify-the-registration"></a>Verifique se o registro

1. Entre portal do administrador (https://adminportal.local.azurestack.external).
2. Clique em **mais serviços** > **Marketplace gerenciamento** > **adicionar do Azure**.
3. Se você vir uma lista de itens disponíveis do Azure (como o WordPress), a ativação foi bem-sucedida.

> [!NOTE]
> Após a conclusão do registro, o aviso de ativo para não registrar não mais aparecerá.

## <a name="next-steps"></a>Próximas etapas

[Conectar-se ao Azure Stack](azure-stack-connect-azure-stack.md)

