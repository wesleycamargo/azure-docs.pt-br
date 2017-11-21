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
ms.date: 11/15/2017
ms.author: erikje
ms.openlocfilehash: 977630741b8424c4c6bd5f5d492e33b9981b9cb5
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2017
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

Se você não tiver uma assinatura do Azure que atende a esses requisitos, você pode [criar uma conta gratuita do Azure aqui](https://azure.microsoft.com/en-us/free/?b=17.06). Registrando a pilha do Azure, incorre em sem custo na sua assinatura do Azure.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Registrar o provedor de recursos da pilha do Azure no Azure
> [!NOTE] 
> Esta etapa deve ser concluída apenas uma vez em um ambiente de pilha do Azure.
>

1. Inicie uma sessão do Powershell como administrador.
2. Login para a conta do Azure que é proprietário da assinatura do Azure (você pode usar o cmdlet AzureRmAccount de logon para efetuar login e quando você entrar, certifique-se de definir o parâmetro - EnvironmentName para "AzureCloud").
3. Registrar o provedor de recursos do Azure "Microsoft.AzureStack".

**Exemplo:** 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```

## <a name="register-azure-stack-with-azure"></a>Registrar a pilha do Azure com o Azure

> [!NOTE]
> Todas essas etapas devem ser executadas a partir de um computador que tenha acesso ao ponto de extremidade com privilégios. Kit de desenvolvimento de pilha do Azure, seria o computador host. Se você estiver usando um sistema integrado, entre em contato com seu operador de pilha do Azure.
>

1. Abra um console do PowerShell como administrador e [instale o PowerShell para Azure pilha](azure-stack-powershell-install.md).  

2. Adicione a conta do Azure que você usará para registrar a pilha do Azure. Para fazer isso, execute o `Add-AzureRmAccount` cmdlet sem parâmetros. Você for solicitado a digitar suas credenciais de conta do Azure e talvez você precise usar a autenticação de 2 fatores com base na configuração da sua conta.  

3. Se você tiver várias assinaturas, execute o seguinte comando para selecionar o que você deseja usar:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Excluir todas as versões existentes dos módulos do Powershell que correspondem ao registro e [baixar a versão mais recente do GitHub](azure-stack-powershell-download.md).  

5. No diretório "AzureStack ferramentas-mestre" que é criado na etapa anterior, navegue até a pasta "Registro" e importar o módulo ".\RegisterWithAzure.psm1":  

   ```powershell 
   Import-Module .\RegisterWithAzure.psm1 
   ```

6. Na sessão do PowerShell, execute o script a seguir. Quando solicitado a fornecer credenciais, especifique `azurestack\cloudadmin` como o usuário e a senha é o mesmo que o que você usou para o administrador local durante a implantação.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Add-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -AzureSubscriptionId $AzureContext.Subscription.Id `
       -AzureDirectoryTenantName $AzureContext.Tenant.TenantId `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development 
   ```

   | Parâmetro | Descrição |
   | -------- | ------------- |
   | CloudAdminCredential | As credenciais de domínio de nuvem que são usadas para [acessar o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). O nome de usuário está no formato "\<domínio Azure pilha\>\cloudadmin". Kit de desenvolvimento, o nome de usuário é definido como "azurestack\cloudadmin". Se você estiver usando um sistema integrado, entre em contato com seu operador de pilha do Azure para obter esse valor.|
   | AzureSubscriptionId | A assinatura do Azure que você usa para registrar a pilha do Azure.|
   | AzureDirectoryTenantName | Nome do diretório locatário do Azure que está associado à sua assinatura do Azure. O recurso de registro será criado nesse locatário de diretório. |
   | PrivilegedEndpoint | Um pré-configurado console remoto do PowerShell que fornece recursos, como a coleta de log e outra post tarefas de implantação. Kit de desenvolvimento, o ponto de extremidade privilegiado é hospedado na máquina virtual "AzS ERCS01". Se você estiver usando um sistema integrado, entre em contato com seu operador de pilha do Azure para obter esse valor. Para obter mais informações, consulte o [usando o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md) tópico.|
   | BillingModel | O modelo de cobrança que usa sua assinatura. Valores permitidos para este parâmetro são "Capacidade", "PayAsYouUse" e "Desenvolvimento". Kit de desenvolvimento, esse valor é definido como "Desenvolvimento". Se você estiver usando um sistema integrado, entre em contato com seu operador de pilha do Azure para obter esse valor. |

7. Quando o script for concluído, você verá uma mensagem "Azure ativando pilha (esta etapa pode levar até 10 minutos para terminar)." 

## <a name="verify-the-registration"></a>Verifique se o registro

1. Entre portal do administrador (https://adminportal.local.azurestack.external).
2. Clique em **mais serviços** > **Marketplace gerenciamento** > **adicionar do Azure**.
3. Se você vir uma lista de itens disponíveis do Azure (como o WordPress), a ativação foi bem-sucedida.

> [!NOTE]
> Após a conclusão do registro, o aviso de ativo para não registrar não mais aparecerá.

## <a name="next-steps"></a>Próximas etapas

[Conectar-se ao Azure Stack](azure-stack-connect-azure-stack.md)

