---
title: Registrar a pilha do Azure | Microsoft Docs
description: Registre a pilha do Azure com sua assinatura do Azure.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/5/2018
ms.author: jeffgilb
ms.openlocfilehash: c2edafbf483692d5a11771268a1755c11b74521f
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Registrar a pilha do Azure com sua assinatura do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode registrar [Azure pilha](azure-stack-poc.md) com o Azure para baixar itens do marketplace do Azure e configurar dados de comércio relatadas à Microsoft.

> [!NOTE]
>Registro é recomendado porque ela permite que você teste funcionalidades importantes da pilha do Azure, como distribuição de mercado e relatórios de uso. Depois de registrar a pilha do Azure, o uso é relatado para comércio do Azure. Você poderá ver isso na assinatura usado para registro. Usuários do Azure do Kit de desenvolvimento de pilha não são cobrados para qualquer uso que eles relatam.


## <a name="get-azure-subscription"></a>Obter assinatura do Azure

Antes de registrar a pilha do Azure com o Azure, você deve ter:

- A ID de assinatura para uma assinatura do Azure. Para obter a ID, entre no Azure, clique em **mais serviços** > **assinaturas**, clique na assinatura que você deseja usar, e, em **Essentials** você pode encontrar o **ID da assinatura**. Na China, Alemanha e assinaturas de nuvem do governo dos EUA não têm suporte no momento.
- O nome de usuário e a senha de uma conta que é proprietário da assinatura (há suporte para contas MSA/2FA).
- *Não são necessários a partir da versão de atualização de 1712 de pilha do Azure (1.0.180103.2):* o Active Directory do Azure para a assinatura do Azure. Você pode encontrar esse diretório no Azure focalizando seu avatar no canto superior direito do portal do Azure.

Se você não tiver uma assinatura do Azure que atende a esses requisitos, você pode [criar uma conta gratuita do Azure aqui](https://azure.microsoft.com/en-us/free/?b=17.06). Registrando a pilha do Azure, incorre em sem custo na sua assinatura do Azure.

## <a name="register-azure-stack-with-azure"></a>Registrar a pilha do Azure com o Azure  
> [!NOTE]
> Todas essas etapas devem ser executadas a partir de um computador que tenha acesso ao ponto de extremidade com privilégios. Kit de desenvolvimento de pilha do Azure, seria o computador host. Se você estiver usando um sistema integrado, entre em contato com seu operador de pilha do Azure.
>

1. Abra um console do PowerShell como administrador e [instale o PowerShell para Azure pilha](azure-stack-powershell-install.md).  

2. Adicione a conta do Azure que você usa para registrar a pilha do Azure. Para adicionar a conta, execute o `Add-AzureRmAccount` cmdlet com o parâmetro EnvironmentName definido como **AzureCloud**. Você for solicitado a digitar suas credenciais de conta do Azure e talvez você precise usar a autenticação de 2 fatores com base na configuração da sua conta.

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. Se você tiver várias assinaturas, execute o seguinte comando para selecionar o que você deseja usar:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Execute o comando a seguir para registrar o provedor de recursos da pilha do Azure em sua assinatura do Azure:

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Excluir todas as versões existentes dos módulos do PowerShell que correspondem ao registro e [baixar a versão mais recente do GitHub](azure-stack-powershell-download.md).  

6. No diretório "AzureStack ferramentas-mestre" que é criado na etapa anterior, navegue até a pasta "Registro" e importar o módulo ".\RegisterWithAzure.psm1":  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

7. Na sessão do PowerShell, execute o seguinte script: quando solicitado a fornecer credenciais, especifique `azurestack\cloudadmin` como o usuário e a senha é o mesmo que o que você usou para o administrador local durante a implantação.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Set-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development
   ```

   | Parâmetro | DESCRIÇÃO |  
   |--------|-------------|
   | CloudAdminCredential | As credenciais de domínio de nuvem que são usadas para [acessar o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). O nome de usuário está no formato  **\<domínio Azure pilha\>\cloudadmin**. Kit de desenvolvimento, o nome de usuário é definido como **azurestack\cloudadmin**. Se você estiver usando um sistema integrado, entre em contato com seu operador de pilha do Azure para obter esse valor.|  
   | PrivilegedEndpoint | Um pré-configurado console remoto do PowerShell que fornece recursos, como a coleta de log e outra post tarefas de implantação. Kit de desenvolvimento, o ponto de extremidade privilegiado é hospedado na máquina virtual "AzS ERCS01". Se você estiver usando um sistema integrado, entre em contato com seu operador de pilha do Azure para obter esse valor. Para obter mais informações, consulte o [usando o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md) artigo.|  
   | BillingModel | O modelo de cobrança que usa sua assinatura. Valores permitidos para este parâmetro são - **capacidade**, **PayAsYouUse**, e **desenvolvimento**. Kit de desenvolvimento, esse valor é definido como **desenvolvimento**. Se você estiver usando um sistema integrado, entre em contato com seu operador de pilha do Azure para obter esse valor. |

8. Quando o script for concluído, você verá uma mensagem "Azure ativando pilha (esta etapa pode levar até 10 minutos para terminar)."




## <a name="verify-the-registration"></a>Verifique se o registro  

1. Entre portal do administrador (https://adminportal.local.azurestack.external).

2. Clique em **mais serviços** > **Marketplace gerenciamento** > **adicionar do Azure**.

3. Se você vir uma lista de itens disponíveis do Azure (como o WordPress), a ativação foi bem-sucedida.

> [!NOTE]
> Após a conclusão do registro, o aviso de ativo para não registrar não mais aparecerá.


## <a name="modify-the-registration"></a>Modificar o registro

### <a name="change-the-subscription-you-use"></a>Alterar a assinatura que você usar
Se você quiser alterar a assinatura que você usar, você deve primeiro executar remover AzsRegistration, certifique-se de que você está conectado ao contexto correto do PowerShell do Azure e execute AzsRegistration conjunto com os parâmetros alterados.

  ```Powershell   
  Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```
### <a name="change-the-billing-model-or-syndication-features"></a>Alterar os recursos de distribuição ou de modelo de cobrança
Se você quiser alterar o modelo de cobrança ou recursos de distribuição para a instalação, você pode chamar a função de registro para definir os novos valores. Não é necessário primeiro remover o registro atual.  

  ```Powershell
     Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```


## <a name="disconnected-registration"></a>Registro desconectado
*As informações nesta seção se aplica a partir da versão de atualização do Azure pilha 1712 (1.0.180103.2) e não é compatível com versões anteriores.*

Se você estiver registrando a pilha do Azure em um ambiente desconectado, você precisa obter um registro de token do ambiente de pilha do Azure e, em seguida, usa esse token em um computador que pode se conectar ao Azure para o registro.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obter um registro de token do ambiente de pilha do Azure
  1. Para obter o token de registro, execute o seguinte:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > O token de registro é salvo no arquivo especificado para *$env:SystemDrive\RegistrationToken.txt*.

  2. Salve esse token de registro para uso no Azure máquina conectada.


### <a name="connect-to-azure-and-register"></a>Conecte-se ao Azure e ao registro
Execute as etapas para esse procedimento em um computador que pode se conectar ao Azure.

  1. [Baixar os módulos do PowerShell mais recentes que correspondem ao registro do GitHub](azure-stack-powershell-download.md).  

  2. No diretório "AzureStack ferramentas-mestre" que é criado na etapa anterior, navegue até a pasta "Registro" e importar o módulo ".\RegisterWithAzure.psm1":  

     ```powershell
     Import-Module .\RegisterWithAzure.psm1
     ```

  3. Cópia [RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959) para uma pasta, como *C:\Temp*.

  4. Iniciar o ISE do PowerShell como administrador e, em seguida, importe o módulo de RegisterWithAzure.  

  5. Certifique-se de que você está conectado ao contexto correto do PowerShell do Azure que você deseja usar para registrar o seu ambiente de pilha do Azure:  

     ```Powershell
        Set-AzureRmContext -SubscriptionId $YourAzureSubscriptionId   
     ```
  6. Especifique o token de registro para registrar com o Azure:

     ```Powershell  
       $registrationToken = "*Your Registration Token*"
       Register-AzsEnvironment -RegistrationToken $registrationToken  
     ```
    Opcionalmente, você pode usar o cmdlet Get-Content para apontar para um arquivo que contém o token de registro:

    ```Powershell  
       $registrationToken = Get-Content -Path 'C:\Temp\<Registration Token File>'
       Register-AzsEnvironment -RegistrationToken $registrationToken  
    ```
> [!NOTE]  
> Salve o nome do recurso de registro ou o token de registro para referência futura.

### <a name="remove-a-registered-resource"></a>Remover um recurso registrado
Se você quiser remover o recurso de registro, você deve usar o UnRegister-AzsEnvironment e passar o nome do recurso de registro ou o token de registro que é usado para registrar AzsEnvironment.
- **Nome do recurso de registro:**

  ```Powershell    
     UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
  ```
- **Token de registro:**    

  ```Powershell
     $registrationToken = "*Your copied registration token*"
     UnRegister-AzsEnvironment -RegistrationToken $registrationToken
   ```


## <a name="next-steps"></a>Próximas etapas
[Conectar-se ao Azure Stack](azure-stack-connect-azure-stack.md)
