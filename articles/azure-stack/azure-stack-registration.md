---
title: Sistemas integrados do registro do Azure para o Azure Stack | Microsoft Docs
description: Descreve o processo de registro do Azure para implantações de vários nós conectados ao Azure de pilha do Azure.
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
ms.date: 07/25/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: a5e31df435d5e9af8543301e7a4540faa3d6410f
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258058"
---
# <a name="register-azure-stack-with-azure"></a>Registre-se a pilha do Azure com o Azure

Registrando [do Azure Stack](azure-stack-poc.md) com o Azure permite a você para baixar itens do marketplace do Azure e configurar dados de comércio relatórios de volta para a Microsoft. Depois de registrar o Azure Stack, o uso é relatado para comércio do Azure e você pode vê-lo sob a assinatura usada para o registro.

> [!IMPORTANT]  
> Registro é necessário para dar suporte à funcionalidade completa do Azure Stack, incluindo a sindicalização do marketplace. Além disso, você será em violação dos termos de licença, se você não registrar ao usar o modelo de cobrança pagamento-como-o uso do Azure Stack. Para saber mais sobre o Azure Stack, modelos de licenciamento, consulte o [como comprar página](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de registrar o Azure Stack com o Azure, você deve ter:

- A ID de assinatura para uma assinatura do Azure. Para obter a ID, entre no Azure, clique em **mais serviços** > **assinaturas**, clique na assinatura que você deseja usar, e, em **Essentials** você pode encontrar o ID da assinatura.

  > [!NOTE]
  > Alemanha e assinaturas de nuvem do governo dos EUA não têm suporte no momento.

- O nome de usuário e senha para uma conta que seja um proprietário da assinatura (há suporte para contas MSA/2FA).
- Registrar o provedor de recursos do Azure Stack (consulte a seção de registrar o provedor de recursos do Azure Stack abaixo para obter detalhes).

Se você não tiver uma assinatura do Azure que atende a esses requisitos, você poderá [criar uma conta gratuita do Azure aqui](https://azure.microsoft.com/free/?b=17.06). Registrar o Azure Stack incorre em sua assinatura do Azure sem custo.

### <a name="powershell-language-mode"></a>Modo de linguagem do PowerShell

Para registrar com êxito o Azure Stack, o modo de linguagem do PowerShell deve ser definido como **FullLanguageMode**.  Para verificar se o modo de linguagem atual está definido como completo, abra uma janela do PowerShell com privilégios elevados e execute os seguintes comandos do PowerShell:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Certifique-se a saída retorna **FullLanguageMode**. Se qualquer outro modo de linguagem é retornado, registro precisa ser executado em outro computador ou o modo de linguagem precisará ser definido como **FullLanguageMode** antes de continuar.

### <a name="bkmk_powershell"></a>Instale o PowerShell para o Azure Stack

Você precisa usar o PowerShell mais recente para o Azure Stack para registrar com o Azure.

Se ainda não estiver instalado, [instalar o PowerShell para Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="bkmk_tools"></a>Baixe as ferramentas do Azure Stack

O repositório do GitHub de ferramentas do Azure Stack contém módulos do PowerShell que dão suporte à funcionalidade de pilha do Azure; incluindo a funcionalidade de registro. Durante o processo de registro, você precisará importar e usar o módulo PowerShell RegisterWithAzure.psm1, encontrado no repositório de ferramentas do Azure Stack, para registrar sua instância do Azure Stack com o Azure.

Para garantir que você estiver usando a versão mais recente, você deve excluir todas as versões existentes das ferramentas do Azure Stack e [baixar a versão mais recente do GitHub](azure-stack-powershell-download.md) antes de registrar com o Azure.

## <a name="register-azure-stack-in-connected-environments"></a>Registrar o Azure Stack em ambientes conectados

Ambientes conectados podem acessar a internet e o Azure. Para esses ambientes, você precisa registrar o provedor de recursos do Azure Stack com o Azure e, em seguida, configurar o modelo de cobrança.

> [!NOTE]
> Todas essas etapas devem ser executadas em um computador que tem acesso ao ponto de extremidade com privilégios.

### <a name="register-the-azure-stack-resource-provider"></a>Registrar o provedor de recursos do Azure Stack

Para registrar o provedor de recursos do Azure Stack com o Azure, inicie o PowerShell ISE como administrador e use os seguintes comandos do PowerShell com o **EnvironmentName** parâmetro definido como o tipo de assinatura do Azure apropriada (consulte parâmetros abaixo).

1. Adicione a conta do Azure que você usa para registrar o Azure Stack. Para adicionar a conta, execute as **Add-AzureRmAccount** cmdlet. Você precisará inserir suas credenciais de conta de administrador global do Azure e você talvez precise usar a autenticação de fator de 2, com base na configuração da sua conta.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | Parâmetro | DESCRIÇÃO |  
   |-----|-----|
   | EnvironmentName | O nome de ambiente de assinatura de nuvem do Azure. Nomes de ambiente com suporte são **AzureCloud** ou, se usando uma assinatura do Azure na China, **AzureChinaCloud**.  |
   |  |  |

2. Se você tiver várias assinaturas, execute o seguinte comando para selecionar aquela que você deseja usar:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Execute o seguinte comando para registrar o provedor de recursos do Azure Stack em sua assinatura do Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Registre-se a pilha do Azure com o Azure usando o modelo de cobrança pagamento-como-uso

Use estas etapas para registrar o Azure Stack com o Azure usando o modelo de cobrança pagamento-como-o uso.

1. Inicie o PowerShell ISE como administrador e navegue até a **registro** pasta o **AzureStack-Tools-master** diretório criado quando você [baixado as ferramentas do Azure Stack](#bkmk_tools). Importar o **RegisterWithAzure.psm1** módulo usando o PowerShell:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Em seguida, na mesma sessão do PowerShell, verifique se que você está conectado ao Azure PowerShell contexto correto. Essa é a conta do azure que foi usada para registrar o provedor de recursos do Azure Stack acima. PowerShell para executar:

   ```PowerShell  
   Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>"
   ```

3. Na mesma sessão do PowerShell, execute as **AzsRegistration conjunto** cmdlet. PowerShell para executar:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse
      -RegistrationName $RegistrationName
   ```

  |Parâmetro|DESCRIÇÃO|
  |-----|-----|
  |PrivilegedEndpointCredential|As credenciais usadas para [acessar o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). O nome de usuário está no formato **AzureStackDomain\CloudAdmin**.|
  |PrivilegedEndpoint|Um pré-configurados console remoto do PowerShell que fornece recursos como coleta de logs e outro post tarefas de implantação. Para obter mais informações, consulte o [usando o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artigo.|
  |BillingModel|O modelo de cobrança que usa sua assinatura. Valores para esse parâmetro permitidos são: capacidade, PayAsYouUse e desenvolvimento.|
  | registrationName | Defina um nome exclusivo para o registro, se você estiver executando o script de registro em mais de uma instância do Azure Stack usando a assinatura do Azure mesmo ID. O parâmetro tem um valor padrão de **AzureStackRegistration**. No entanto, se você usar o mesmo nome em mais de uma instância do Azure Stack, o script falhará. |

  O processo levará entre 10 e 15 minutos. Quando o comando for concluído, você verá a mensagem **"seu ambiente agora está registrado e ativado usando os parâmetros fornecidos."**

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Registre-se a pilha do Azure com o Azure usando o modelo de cobrança de capacidade

Siga as mesmas instruções usadas para registrar usando o modelo de cobrança pagamento-como-o uso, mas adicionar o número do contrato sob a qual a capacidade foi comprada e alterar o **BillingModel** parâmetro **capacidade**. Todos os outros parâmetros são as mesmas.

PowerShell para executar:

```PowerShell  
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
$RegistrationName = "<unique-registration-name>"
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
    -RegistrationName $RegistrationName
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Registrar o Azure Stack em ambientes desconectados
Se você estiver registrando o Azure Stack em um ambiente desconectado (sem conectividade com a internet), você precisará obter um registro de token do ambiente do Azure Stack e, em seguida, usar esse token em um computador que pode se conectar ao Azure e tem [PowerShell para o Azure Stack instalado](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obter um registro de token do ambiente do Azure Stack

1. Inicie o PowerShell ISE como administrador e navegue até a **registro** pasta o **AzureStack-Tools-master** diretório criado quando você [baixado as ferramentas do Azure Stack](#bkmk_tools). Importar o **RegisterWithAzure.psm1** módulo:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Para obter o token de registro, execute os seguintes comandos do PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!TIP]  
   > O token de registro é salvo no arquivo especificado para *$FilePathForRegistrationToken*. Você pode alterar o caminho do arquivo ou nome de arquivo a seu critério.

3. Salve esse token de registro para uso no Azure máquina conectada. Você pode copiar o arquivo ou o texto de $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Conectar-se ao Azure e ao registro

No computador que está conectado à Internet, execute as mesmas etapas para importar o módulo RegisterWithAzure.psm1 e entrar no contexto correto do Azure Powershell. Em seguida, chame AzsEnvironment Register. Especifique o token de registro para registrar com o Azure. Se você estiver registrando a mais de uma instância do Azure Stack usando a mesma ID de assinatura do Azure, especifique um nome exclusivo do registro. Execute o cmdlet a seguir:

  ```PowerShell  
  $registrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  -RegistrationName $RegistrationName
  ```

Opcionalmente, você pode usar o cmdlet Get-Content para apontar para um arquivo que contém seu token de registro:

  ```PowerShell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken -RegistrationName $RegistrationName
  ```

  > [!NOTE]  
  > Salve o nome do recurso de registro e o token de registro para referência futura.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Recuperar uma chave de ativação do recurso de registro do Azure

Em seguida, você precisa recuperar uma chave de ativação do recurso do registro criado no Azure durante o registro AzsEnvironment.

Para obter a chave de ativação, execute os seguintes comandos do PowerShell:  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!TIP]  
  > A chave de ativação é salvo no arquivo especificado para *$KeyOutputFilePath*. Você pode alterar o caminho do arquivo ou nome de arquivo a seu critério.

### <a name="create-an-activation-resource-in-azure-stack"></a>Criar um recurso de ativação no Azure Stack

Retornar para o ambiente do Azure Stack com o arquivo ou o texto da chave de ativação criado a partir de Get-AzsActivationKey. Em seguida, você criará um recurso de ativação no Azure Stack usando essa chave de ativação. Para criar um recurso de ativação, execute os seguintes comandos do PowerShell:  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Opcionalmente, você pode usar o cmdlet Get-Content para apontar para um arquivo que contém seu token de registro:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Verificar o registro do Azure Stack

Use estas etapas para verificar se o Azure Stack está registrado com êxito com o Azure.

1. Entrar para o Azure Stack [portal do administrador](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;região >. &lt;fqdn >*.
2. Selecione **mais serviços** > **gerenciamento de Marketplace** > **adicionar do Azure**.

Se você vir uma lista de itens disponíveis do Azure (por exemplo, o WordPress), a ativação foi bem-sucedida. No entanto, em ambientes desconectados você não verá itens do marketplace do Azure no marketplace do Azure Stack.

> [!NOTE]
> Após a conclusão do registro, o Active Directory aviso por não registrar não aparecerá mais.

## <a name="renew-or-change-registration"></a>Renovar ou alterar o registro

### <a name="renew-or-change-registration-in-connected-environments"></a>Renovar ou alterar o registro em ambientes conectados

Você precisará atualizar ou renovar seu registro nas seguintes circunstâncias:

- Depois de você renovar sua assinatura anual com base em capacidade.
- Quando você altera o modelo de cobrança.
- Quando você dimensionar as alterações (Adicionar ou remover nós) para cobrança baseada em capacidade.

#### <a name="change-the-subscription-you-use"></a>Alterar a assinatura que você usa

Se você gostaria de alterar a assinatura é usar, primeiro você deve executar o **AzsRegistration remover** cmdlet, em seguida, verifique se você efetuou logon no contexto correto do Azure PowerShell e finalmente executar **AzsRegistration conjunto**  com qualquer alterado parâmetros:

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>Alterar os recursos de distribuição ou de modelo de cobrança

Se você quiser alterar o modelo de cobrança ou recursos de distribuição de sua instalação, você pode chamar a função de registro para definir os novos valores. Você não precisará primeiro remover o registro atual:

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Renovar ou alterar o registro em ambientes desconectados

Você precisará atualizar ou renovar seu registro nas seguintes circunstâncias:

- Depois de você renovar sua assinatura anual com base em capacidade.
- Quando você altera o modelo de cobrança.
- Quando você dimensionar as alterações (Adicionar ou remover nós) para cobrança baseada em capacidade.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Remover o recurso de ativação do Azure Stack

Primeiro, você precisará remover o recurso de ativação do Azure Stack e, em seguida, o recurso de registro no Azure.  

Para remover o recurso de ativação no Azure Stack, execute os seguintes comandos do PowerShell em seu ambiente do Azure Stack:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Em seguida, para remover o recurso de registro no Azure, verifique se você estiver usando um Azure conectado computador, entre no contexto correto do Azure PowerShell e execute os comandos do PowerShell apropriados, conforme descrito abaixo.

Você pode usar o token de registro usado para criar o recurso:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Ou você pode usar o nome de registro:

  ```Powershell
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Registre novamente usando as etapas desconectadas

Você agora tem completamente cancelado em um cenário desconectado e deve repetir as etapas para registrar um ambiente do Azure Stack em um cenário desconectado.

## <a name="next-steps"></a>Próximas etapas

[Baixar itens do marketplace do Azure](azure-stack-download-azure-marketplace-item.md)
