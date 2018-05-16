---
title: Sistemas integrados de registro do Azure para a pilha do Azure | Microsoft Docs
description: Descreve o processo de registro do Azure para implantações de vários nós conectados do Azure de pilha do Azure.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: f34c4697439685ce6ea0ce3f2c7e954ee81b5079
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="register-azure-stack-with-azure"></a>Registrar a pilha do Azure com o Azure
Registrando [Azure pilha](azure-stack-poc.md) com o Azure permite que você para baixar itens do marketplace do Azure e configurar dados de comércio relatadas à Microsoft. Depois de registrar a pilha do Azure, uso é relatado para comércio do Azure e você pode vê-lo sob a assinatura usada para registro. 

> [!IMPORTANT]
> Registro é necessário para dar suporte a funcionalidade completa da pilha do Azure, incluindo a distribuição do marketplace. Além disso, você será em violação da pilha do Azure termos de licença se você não registrar ao usar o modelo de cobrança de pagamento que você-uso. Para saber mais sobre a pilha do Azure modelos de licenciamento, consulte o [como comprar página](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Pré-requisitos
Antes de registrar a pilha do Azure com o Azure, você deve ter:

- A ID de assinatura para uma assinatura do Azure. Para obter a ID, entre no Azure, clique em **mais serviços** > **assinaturas**, clique na assinatura que você deseja usar, e, em **Essentials** você pode encontrar o ID da assinatura. 

  > [!NOTE]
  > Alemanha e assinaturas de nuvem do governo dos EUA não têm suporte no momento.

- O nome de usuário e a senha de uma conta que é proprietário da assinatura (há suporte para contas MSA/2FA).
- Registrado o provedor de recursos da pilha do Azure (consulte a seção de registrar o provedor de recursos da pilha do Azure abaixo para obter detalhes).

Se você não tiver uma assinatura do Azure que atende a esses requisitos, você pode [criar uma conta gratuita do Azure aqui](https://azure.microsoft.com/free/?b=17.06). Registrando a pilha do Azure, incorre em sem custo na sua assinatura do Azure.

### <a name="bkmk_powershell"></a>Instale o PowerShell para a pilha do Azure
Você precisa usar o PowerShell mais recente para a pilha do Azure para registrar com o Azure.

Se ainda não estiver instalado, [instale o PowerShell para Azure pilha](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Baixar as ferramentas de pilha do Azure
O repositório do GitHub de ferramentas do Azure pilha contém módulos do PowerShell que dão suporte à funcionalidade de pilha do Azure; incluindo a funcionalidade de registro. Durante o processo de registro, você precisa importar e usar o módulo do PowerShell RegisterWithAzure.psm1, encontrado no repositório de ferramentas da pilha do Azure, para registrar sua instância de pilha do Azure com o Azure. 

Para garantir que você estiver usando a versão mais recente, você deve excluir todas as versões existentes das ferramentas do Azure pilha e [baixar a versão mais recente do GitHub](azure-stack-powershell-download.md) antes de registrar com o Azure.

## <a name="register-azure-stack-in-connected-environments"></a>Registrar a pilha do Azure em ambientes conectados
Ambientes conectados podem acessar a internet e o Azure. Para esses ambientes, você precisa registrar o provedor de recursos da pilha do Azure com o Azure e, em seguida, configurar o modelo de cobrança.

> [!NOTE]
> Todas essas etapas devem ser executadas em um computador que tenha acesso ao ponto de extremidade com privilégios. 

### <a name="register-the-azure-stack-resource-provider"></a>Registrar o provedor de recursos da pilha do Azure
Para registrar o provedor de recursos da pilha do Azure com o Azure, inicie o ISE do PowerShell como administrador e use os seguintes comandos do PowerShell com a **EnvironmentName** parâmetro definido para o tipo apropriado de assinatura do Azure (consulte parâmetros abaixo). 

1. Adicione a conta do Azure que você usa para registrar a pilha do Azure. Para adicionar a conta, execute o **AzureRmAccount adicionar** cmdlet. Você for solicitado a inserir suas credenciais de conta de administrador global do Azure e talvez você precise usar a autenticação de 2 fatores com base na configuração da sua conta.

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | Parâmetro | DESCRIÇÃO |  
   |-----|-----|
   | EnvironmentName | O nome de ambiente de assinatura de nuvem do Azure. Nomes de ambiente com suporte são **AzureCloud** ou, se usar uma assinatura do Azure na China, **AzureChinaCloud**.  |
   |  |  |

2. Se você tiver várias assinaturas, execute o seguinte comando para selecionar o que você deseja usar:  

   ```PowerShell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Execute o comando a seguir para registrar o provedor de recursos da pilha do Azure em sua assinatura do Azure:

   ```PowerShell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Registrar a pilha do Azure com o Azure usando o modelo de cobrança de pagamento que você-uso
Use estas etapas para registrar a pilha do Azure com o Azure usando o modelo de cobrança de pagamento que você-uso.

1. Iniciar o ISE do PowerShell como administrador e navegue até o **registro** pasta o **mestre de ferramentas AzureStack** diretório criado quando você [baixado as ferramentas do Azure pilha](#bkmk_tools). Importar o **RegisterWithAzure.psm1** módulo usando o PowerShell: 

  ```powershell
  Import-Module .\RegisterWithAzure.psm1
  ```

2. Em seguida, na mesma sessão do PowerShell, verifique se que você está conectado ao Azure PowerShell contexto correto. Essa é a conta do azure que foi usada para registrar o provedor de recursos do Azure pilha acima. PowerShell para executar: 

  ```powershell 
  Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>" 
  ``` 

3. Na sessão do PowerShell, execute o **AzsRegistration conjunto** cmdlet. PowerShell para executar:  

  ```powershell
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse
  ```

  |Parâmetro|DESCRIÇÃO|
  |-----|-----|
  |PrivilegedEndpointCredential|As credenciais usadas para [acessar o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). O nome de usuário está no formato **AzureStackDomain\CloudAdmin**.|
  |PrivilegedEndpoint|Um pré-configurado console remoto do PowerShell que fornece recursos, como a coleta de log e outra post tarefas de implantação. Para obter mais informações, consulte o [usando o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artigo.|
  |BillingModel|O modelo de cobrança que usa sua assinatura. Valores para esse parâmetro permitidos são: capacidade, PayAsYouUse e desenvolvimento.|
  |  |  |

  O processo levará entre 10 e 15 minutos. Quando o comando for concluído, você verá a mensagem **"ambiente agora é registrado e ativada usando os parâmetros fornecidos."**

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Registrar a pilha do Azure com o Azure usando o modelo de cobrança de capacidade
Siga as mesmas instruções usadas para registrar usando o modelo de cobrança de pagamento que você-uso, mas adicione o número de contrato no qual foi adquirida capacidade e altere o **BillingModel** parâmetro **capacidade**. Todos os outros parâmetros são inalterados.

PowerShell para executar:

```powershell
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Registrar a pilha do Azure em ambientes desconectados 
*As informações nesta seção se aplica a partir da versão de atualização do Azure pilha 1712 (180106.1) e não é compatível com versões anteriores.*

Se você estiver registrando pilha do Azure em um ambiente desconectado (sem conectividade com a internet), você precisa obter um registro de token do ambiente de pilha do Azure e, em seguida, usar esse token em um computador que pode se conectar ao Azure e tenha [PowerShell para a pilha do Azure instalado](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obter um registro de token do ambiente de pilha do Azure

1. Iniciar o ISE do PowerShell como administrador e navegue até o **registro** pasta o **mestre de ferramentas AzureStack** diretório criado quando você [baixado as ferramentas do Azure pilha](#bkmk_tools). Importar o **RegisterWithAzure.psm1** módulo:  

  ```powershell 
  Import-Module .\RegisterWithAzure.psm1 
  ``` 

2. Para obter o token de registro, execute os seguintes comandos do PowerShell:  

  ```Powershell
  $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
  $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
  ```
  
  > [!TIP]  
  > O token de registro é salvo no arquivo especificado para *$FilePathForRegistrationToken*. Você pode alterar o caminho de arquivo ou nome de arquivo a seu critério. 

3. Salve esse token de registro para uso no Azure máquina conectada. Você pode copiar o arquivo ou o texto de $FilePathForRegistrationToken.


### <a name="connect-to-azure-and-register"></a>Conecte-se ao Azure e ao registro
No computador que esteja conectada à internet, execute as mesmas etapas para importar o módulo RegisterWithAzure.psm1 e o logon para o contexto correto do Powershell do Azure. Em seguida, chame Register AzsEnvironment e especifique o token de registro para registrar com o Azure:

  ```Powershell  
  $registrationToken = "<Your Registration Token>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```
Opcionalmente, você pode usar o cmdlet Get-Content para apontar para um arquivo que contém o token de registro:

  ```Powershell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```
  > [!NOTE]  
  > Salve o nome do recurso de registro e o token de registro para referência futura.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Recuperar uma chave de ativação do recurso de registro do Azure 
Em seguida, você precisa recuperar uma chave de ativação do recurso de registro criado no Azure durante o registro AzsEnvironment. 
 
Para obter a chave de ativação, execute os seguintes comandos do PowerShell:  

  ```Powershell 
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>" 
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt" 
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath 
  ``` 
  > [!TIP]   
  > A chave de ativação é salvo no arquivo especificado para *$KeyOutputFilePath*. Você pode alterar o caminho de arquivo ou nome de arquivo a seu critério. 

### <a name="create-an-activation-resource-in-azure-stack"></a>Criar um recurso de ativação na pilha do Azure 
Retornar para o ambiente de pilha do Azure com o arquivo ou o texto da chave de ativação criado a partir de Get-AzsActivationKey. Em seguida, você criará um recurso de ativação na pilha do Azure usando a chave de ativação. Para criar um recurso de ativação, execute os seguintes comandos do PowerShell:  

  ```Powershell 
  $ActivationKey = "<activation key>" 
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey 
  ``` 

Opcionalmente, você pode usar o cmdlet Get-Content para apontar para um arquivo que contém o token de registro: 

  ```Powershell   
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>' 
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey 
  ``` 

## <a name="verify-azure-stack-registration"></a>Verificar o registro de pilha do Azure
Use estas etapas para verificar que o Azure pilha foi registrado com êxito com o Azure.
1. Entrar para a pilha do Azure [portal do administrador](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;região >. &lt;fqdn >*.
2. Clique em **mais serviços** > **Marketplace gerenciamento** > **adicionar do Azure**.

Se você vir uma lista de itens disponíveis do Azure (como o WordPress), a ativação foi bem-sucedida. No entanto, em ambientes desconectados você não verá itens do marketplace do Azure no mercado de pilha do Azure.

> [!NOTE]
> Após a conclusão do registro, o aviso de ativo para não registrar não mais aparecerá.

## <a name="renew-or-change-registration"></a>Renovar ou alterar o registro
### <a name="renew-or-change-registration-in-connected-environments"></a>Renovar ou alterar o registro em ambientes conectados
Você precisará atualizar ou renovar seu registro nas seguintes circunstâncias:
- Depois que você renovar sua assinatura anual baseado na capacidade.
- Quando você altera o modelo de cobrança.
- Quando você expandir alterações (Adicionar ou remover nós) para cobrança baseado na capacidade.

#### <a name="change-the-subscription-you-use"></a>Alterar a assinatura que você usar
Se você quiser alterar a assinatura usar, primeiro você deve executar o **AzsRegistration remover** cmdlet, em seguida, certifique-se de que você está conectado ao contexto correto do PowerShell do Azure e finalmente executar **AzsRegistration conjunto**  com qualquer alterado parâmetros:

  ```powershell
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>Alterar os recursos de distribuição ou de modelo de cobrança
Se você quiser alterar o modelo de cobrança ou recursos de distribuição para a instalação, você pode chamar a função de registro para definir os novos valores. Você não precisa primeiro remover o registro atual: 

  ```powershell
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Renovar ou alterar o registro em ambientes desconectados 
Você precisará atualizar ou renovar seu registro nas seguintes circunstâncias: 
- Depois que você renovar sua assinatura anual baseado na capacidade. 
- Quando você altera o modelo de cobrança. 
- Quando você expandir alterações (Adicionar ou remover nós) para cobrança baseado na capacidade. 

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Remova o recurso de ativação de pilha do Azure 
Primeiro, você precisará remover o recurso de ativação da pilha do Azure e, em seguida, o recurso de registro no Azure.  

Para remover o recurso de ativação na pilha do Azure, execute os seguintes comandos do PowerShell em seu ambiente de pilha do Azure:  

  ```Powershell 
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint 
  ``` 

Em seguida, para remover o recurso de registro no Azure, certifique-se você estiver usando um Azure conectado no computador, faça logon no contexto correto do PowerShell do Azure e executar os comandos apropriados do PowerShell, conforme descrito abaixo.

Você pode usar o token de registro usado para criar o recurso:  

  ```Powershell 
  $registrationToken = "<registration token>" 
  Unregister-AzsEnvironment -RegistrationToken $registrationToken 
  ``` 
  
Ou você pode usar o nome do registro: 

  ```Powershell 
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>" 
  Unregister-AzsEnvironment -RegistrationName $registrationName 
  ``` 

### <a name="re-register-using-disconnected-steps"></a>Registre novamente usando as etapas desconectadas 
Completamente agora tem não registradas em um cenário de desconectado e deve repetir as etapas para registrar um ambiente de pilha do Azure em um cenário desconectado. 

## <a name="next-steps"></a>Próximas etapas

[Fazer o download de itens do marketplace do Azure](azure-stack-download-azure-marketplace-item.md)
