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
ms.date: 08/24/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: 58c8568da0a818f87a5bb3d6966d2d4a6c977fd9
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247816"
---
# <a name="register-azure-stack-with-azure"></a>Registre-se a pilha do Azure com o Azure

Registrar o Azure Stack com o Azure permite que você para baixar itens do marketplace do Azure e configurar dados de comércio relatórios de volta para a Microsoft. Depois de registrar o Azure Stack, o uso é relatado para comércio do Azure e você pode vê-lo sob a assinatura usada para o registro.

> [!IMPORTANT]  
> Registro é necessário para dar suporte à funcionalidade completa do Azure Stack, incluindo a oferta de itens no marketplace. Além disso, você será em violação dos termos de licença, se você não registrar ao usar o modelo de cobrança pagamento-como-o uso do Azure Stack. Para saber mais sobre o Azure Stack, modelos de licenciamento, consulte o [como comprar página](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte em vigor antes de registrar:

 - Verificar suas credenciais
 - Definir o modo de linguagem do PowerShell
 - Instale o PowerShell para o Azure Stack
 - Baixe as ferramentas do Azure Stack
 - Determinar seu cenário de registro

### <a name="verify-your-credentials"></a>Verificar suas credenciais

Antes de registrar o Azure Stack com o Azure, você deve ter:

- A ID de assinatura para uma assinatura do Azure. Para obter a ID, entre no Azure, clique em **mais serviços** > **assinaturas**, clique na assinatura que você deseja usar, e, em **Essentials** você pode encontrar o ID da assinatura.

  > [!Note]  
  > Atualmente, não há suporte para assinaturas de nuvem da Alemanha.

- O nome de usuário e senha para uma conta que seja um proprietário da assinatura (há suporte para contas MSA/2FA).

- A conta de usuário precisa ser um administrador no locatário do AD do Azure no qual Azure Stack está registrado, por exemplo, `yourazurestacktenant.onmicrosoft.com`.

- Registrar o provedor de recursos do Azure Stack (consulte a seção de registrar o provedor de recursos do Azure Stack abaixo para obter detalhes).

  Se você não tiver uma assinatura do Azure que atende a esses requisitos, você poderá [criar uma conta gratuita do Azure aqui](https://azure.microsoft.com/free/?b=17.06). Registrar o Azure Stack incorre em sua assinatura do Azure sem custo.

### <a name="powershell-language-mode"></a>Modo de linguagem do PowerShell

Para registrar com êxito o Azure Stack, o modo de linguagem do PowerShell deve ser definido como **FullLanguageMode**.  Para verificar se o modo de linguagem atual está definido como completo, abra uma janela do PowerShell com privilégios elevados e execute os seguintes cmdlets do PowerShell:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Certifique-se a saída retorna **FullLanguageMode**. Se qualquer outro modo de linguagem é retornado, registro precisa ser executado em outro computador ou o modo de linguagem precisará ser definido como **FullLanguageMode** antes de continuar.

### <a name="install-powershell-for-azure-stack"></a>Instale o PowerShell para o Azure Stack

Você precisa usar o PowerShell mais recente para o Azure Stack para registrar com o Azure.

Se não a versão mais recente já não estiver instalada, consulte [instalar o PowerShell para Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="download-the-azure-stack-tools"></a>Baixe as ferramentas do Azure Stack

O repositório do GitHub de ferramentas do Azure Stack contém módulos do PowerShell que dão suporte à funcionalidade de pilha do Azure; incluindo a funcionalidade de registro. Durante o processo de registro, você precisará importar e usar o **RegisterWithAzure.psm1** módulo do PowerShell, encontrado no repositório de ferramentas do Azure Stack, para registrar sua instância do Azure Stack com o Azure.

Para garantir que você estiver usando a versão mais recente, você deve excluir todas as versões existentes das ferramentas do Azure Stack e [baixar a versão mais recente do GitHub](azure-stack-powershell-download.md) antes de registrar com o Azure.

### <a name="determine-your-registration-scenario"></a>Determinar seu cenário de registro

Pode ser a sua implantação do Azure Stack *conectados* ou *desconectado*.

 - **Conectado**  
 Conectado significa que você implantou o Azure Stack para que ele possa se conectar à Internet e para o Azure. Você têm Azure Active Directory (Azure AD) ou serviços de Federação do Active Directory (AD FS) para armazenamento de identidade. Com uma implantação de conectado, você pode escolher entre dois modelos de cobrança: de pagamento-como-o uso ou baseada em capacidade.
    - [Registrar uma pilha do Azure conectada com o Azure usando o **pagamento-como-uso** modelo de cobrança](#register-connected-with-pay-as-you-go-billing)
    - [Registrar uma pilha do Azure conectada com o Azure usando o **capacidade** modelo de cobrança](#register-connected-with-capacity-billing)

 - **Desconectado**  
 Com o desconectado da opção de implantação do Azure, você pode implantar e usar o Azure Stack sem uma conexão à Internet. No entanto, com uma implantação desconectada, você está limitado a um repositório de identidades do AD FS e o modelo de cobrança com base em capacidade.
    - [Registrar um desconectado do Azure Stack usando o **capacidade** modelo de cobrança ](#register-disconnected-with-capacity-billing)

## <a name="register-connected-with-pay-as-you-go-billing"></a>Registre-se conectado com cobrança pré-paga

Use estas etapas para registrar o Azure Stack com o Azure usando o modelo de cobrança pagamento-como-o uso.

> [!Note]  
> Todas essas etapas devem ser executadas em um computador que tem acesso ao ponto de extremidade com privilégios (PEP). Para obter detalhes sobre o PEP, consulte [usando o ponto de extremidade privilegiado no Azure Stack](azure-stack-privileged-endpoint.md).

Ambientes conectados podem acessar a internet e o Azure. Para esses ambientes, você precisa registrar o provedor de recursos do Azure Stack com o Azure e, em seguida, configurar o modelo de cobrança.

1. Para registrar o provedor de recursos do Azure Stack com o Azure, inicie o PowerShell ISE como administrador e usar os seguintes cmdlets do PowerShell com o **EnvironmentName** parâmetro definido como o tipo de assinatura do Azure apropriada (consulte parâmetros abaixo).

2. Adicione a conta do Azure que você usa para registrar o Azure Stack. Para adicionar a conta, execute as **Add-AzureRmAccount** cmdlet. Você precisará inserir suas credenciais de conta de administrador global do Azure e você talvez precise usar a autenticação de fator de 2, com base na configuração da sua conta.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

   | Parâmetro | DESCRIÇÃO |  
   |-----|-----|
   | EnvironmentName | O nome de ambiente de assinatura de nuvem do Azure. Nomes de ambiente com suporte são **AzureCloud**, **AzureUSGovernment**, ou se usando uma assinatura do Azure na China, **AzureChinaCloud**.  |

3. Se você tiver várias assinaturas, execute o seguinte comando para selecionar aquela que você deseja usar:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Execute o seguinte comando para registrar o provedor de recursos do Azure Stack em sua assinatura do Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Inicie o PowerShell ISE como administrador e navegue até a **registro** pasta o **AzureStack-Tools-master** diretório criado quando você [baixado as ferramentas do Azure Stack](#bkmk_tools). Importar o **RegisterWithAzure.psm1** módulo usando o PowerShell:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Em seguida, na mesma sessão do PowerShell, verifique se que você está conectado ao Azure PowerShell contexto correto. Essa é a conta do azure que foi usada para registrar o provedor de recursos do Azure Stack acima. PowerShell para executar:

   ```PowerShell  
   Add-AzureRmAccount -Environment "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

7. Na mesma sessão do PowerShell, execute as **AzsRegistration conjunto** cmdlet. PowerShell para executar:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Para obter mais informações sobre o cmdlet Set-AzsRegistration, consulte [referência de registro](#registration-reference).

  O processo levará entre 10 e 15 minutos. Quando o comando for concluído, você verá a mensagem **"seu ambiente agora está registrado e ativado usando os parâmetros fornecidos."**

## <a name="register-connected-with-capacity-billing"></a>Registre-se conectado com a cobrança de capacidade

Use estas etapas para registrar o Azure Stack com o Azure usando o modelo de cobrança pagamento-como-o uso.

> [!Note]  
> Todas essas etapas devem ser executadas em um computador que tem acesso ao ponto de extremidade com privilégios (PEP). Para obter detalhes sobre o PEP, consulte [usando o ponto de extremidade privilegiado no Azure Stack](azure-stack-privileged-endpoint.md).

Ambientes conectados podem acessar a internet e o Azure. Para esses ambientes, você precisa registrar o provedor de recursos do Azure Stack com o Azure e, em seguida, configurar o modelo de cobrança.

1. Para registrar o provedor de recursos do Azure Stack com o Azure, inicie o PowerShell ISE como administrador e usar os seguintes cmdlets do PowerShell com o **EnvironmentName** parâmetro definido como o tipo de assinatura do Azure apropriada (consulte parâmetros abaixo).

2. Adicione a conta do Azure que você usa para registrar o Azure Stack. Para adicionar a conta, execute as **Add-AzureRmAccount** cmdlet. Você precisará inserir suas credenciais de conta de administrador global do Azure e você talvez precise usar a autenticação de fator de 2, com base na configuração da sua conta.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

   | Parâmetro | DESCRIÇÃO |  
   |-----|-----|
   | EnvironmentName | O nome de ambiente de assinatura de nuvem do Azure. Nomes de ambiente com suporte são **AzureCloud**, **AzureUSGovernment**, ou se usando uma assinatura do Azure na China, **AzureChinaCloud**.  |

3. Se você tiver várias assinaturas, execute o seguinte comando para selecionar aquela que você deseja usar:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Execute o seguinte comando para registrar o provedor de recursos do Azure Stack em sua assinatura do Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Inicie o PowerShell ISE como administrador e navegue até a **registro** pasta o **AzureStack-Tools-master** diretório criado quando você [baixado as ferramentas do Azure Stack](#bkmk_tools). Importar o **RegisterWithAzure.psm1** módulo usando o PowerShell:

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
   > [!Note]  
   > Você pode desabilitar o uso dos relatórios com o parâmetro UsageReportingEnabled para o **AzsRegistration conjunto** cmdlet. Defina o parâmetro como false. Por exemplo: ' UsageReportingEnabled
   
  Para obter mais informações sobre o cmdlet Set-AzsRegistration, consulte [referência de registro](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>Registrar desconectada com a cobrança de capacidade

Se você estiver registrando o Azure Stack em um ambiente desconectado (sem conectividade com a internet), você precisará obter um registro de token do ambiente do Azure Stack e, em seguida, usar esse token em um computador que pode se conectar ao Azure e tem [PowerShell para o Azure Stack instalado](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obter um registro de token do ambiente do Azure Stack

1. Inicie o PowerShell ISE como administrador e navegue até a **registro** pasta o **AzureStack-Tools-master** diretório criado quando você [baixado as ferramentas do Azure Stack](#bkmk_tools). Importar o **RegisterWithAzure.psm1** módulo:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Para obter o token de registro, execute os seguintes cmdlets do PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Para obter mais informações sobre o cmdlet Get-AzsRegistrationToken, consulte [referência de registro](#registration-reference).

   > [!Tip]  
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

  > [!Note]  
  > Salve o nome do recurso de registro e o token de registro para referência futura.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Recuperar uma chave de ativação do recurso de registro do Azure

Em seguida, você precisa recuperar uma chave de ativação do recurso do registro criado no Azure durante o registro AzsEnvironment.

Para obter a chave de ativação, execute os seguintes cmdlets do PowerShell:  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > A chave de ativação é salvo no arquivo especificado para *$KeyOutputFilePath*. Você pode alterar o caminho do arquivo ou nome de arquivo a seu critério.

### <a name="create-an-activation-resource-in-azure-stack"></a>Criar um recurso de ativação no Azure Stack

Retornar para o ambiente do Azure Stack com o arquivo ou o texto da chave de ativação criado a partir de Get-AzsActivationKey. Em seguida, você criará um recurso de ativação no Azure Stack usando essa chave de ativação. Para criar um recurso de ativação, execute os seguintes cmdlets do PowerShell:  

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

> [!Note]  
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

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Alterar o modelo de cobrança ou como oferecer recursos

Se você quiser alterar o modelo de cobrança ou como oferecer recursos de sua instalação, você pode chamar a função de registro para definir os novos valores. Você não precisará primeiro remover o registro atual:

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

Para remover o recurso de ativação no Azure Stack, execute os seguintes cmdlets do PowerShell em seu ambiente do Azure Stack:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Em seguida, para remover o recurso de registro no Azure, verifique se você estiver usando um Azure conectado no computador, entre no contexto correto do Azure PowerShell e executar os cmdlets do PowerShell apropriado, conforme descrito abaixo.

Você pode usar o token de registro usado para criar o recurso:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Ou você pode usar o nome de registro:

  ```Powershell
  $registrationName = "AzureStack-<Cloud ID of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Registre novamente usando as etapas desconectadas

Você agora tem completamente cancelado em um cenário desconectado e deve repetir as etapas para registrar um ambiente do Azure Stack em um cenário desconectado.

### <a name="disable-or-enable-usage-reporting"></a>Desabilitar ou habilitar o relatório de uso

Por ambientes Azure Stack que usam um modelo de cobrança de capacidade, desative o uso dos relatórios com o **UsageReportingEnabled** parâmetro usando o **AzsRegistration conjunto** ou o  **Get-AzsRegistrationToken** cmdlets. O Azure Stack relata as métricas de uso por padrão. Os operadores com usos de capacidade ou dar suporte a um ambiente desconectado serão necessário desativar o relatório de uso.

#### <a name="with-a-connected-azure-stack"></a>Com uma pilha do Azure conectada

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>Com uma pilha do Azure desconectados

1. Para alterar o token de registro, execute os seguintes cmdlets do PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > O token de registro é salvo no arquivo especificado para *$FilePathForRegistrationToken*. Você pode alterar o caminho do arquivo ou nome de arquivo a seu critério.

2. Salve esse token de registro para uso no Azure máquina conectada. Você pode copiar o arquivo ou o texto de $FilePathForRegistrationToken.


## <a name="registration-reference"></a>Referência de registro

### <a name="set-azsregistration"></a>Conjunto AzsRegistration

Você pode usar o conjunto AzsRegistration para registrar o Azure Stack com o Azure e habilitar ou desabilitar a oferta de itens no marketplace e o relatório de uso.

Para executar o cmdlet, você precisa:
- Uma assinatura do Azure global de qualquer tipo.
- Você deve também ser conectado ao Azure PowerShell com uma conta que seja um proprietário ou colaborador dessa assinatura.

```PowerShell
    Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
   ```

| Parâmetro | Tipo | DESCRIÇÃO |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | As credenciais usadas para [acessar o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). O nome de usuário está no formato **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Cadeia de caracteres | Um pré-configurados console remoto do PowerShell que fornece recursos como coleta de logs e outro post tarefas de implantação. Para obter mais informações, consulte o [usando o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artigo. |
| AzureContext | PSObject |  |
| ResourceGroupName | Cadeia de caracteres |  |
| ResourceGroupLocation | Cadeia de caracteres |  |
| BillingModel | Cadeia de caracteres | O modelo de cobrança que usa sua assinatura. Valores para esse parâmetro permitidos são: capacidade, PayAsYouUse e desenvolvimento. |
| MarketplaceSyndicationEnabled |  |  |
| UsageReportingEnabled | Verdadeiro/Falso | O Azure Stack relata as métricas de uso por padrão. Os operadores com usos de capacidade ou dar suporte a um ambiente desconectado serão necessário desativar o relatório de uso. Valores para esse parâmetro permitidos são: True, False. |
| AgreementNumber | Cadeia de caracteres |  |
| registrationName | Cadeia de caracteres | Defina um nome exclusivo para o registro, se você estiver executando o script de registro em mais de uma instância do Azure Stack usando a assinatura do Azure mesmo ID. O parâmetro tem um valor padrão de **AzureStackRegistration**. No entanto, se você usar o mesmo nome em mais de uma instância do Azure Stack, o script falhará. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken irá gerar um token de registro de parâmetros de entrada.

```PowerShell  
    Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Parâmetro | Tipo | DESCRIÇÃO |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | As credenciais usadas para [acessar o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). O nome de usuário está no formato **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Cadeia de caracteres |  Um pré-configurados console remoto do PowerShell que fornece recursos como coleta de logs e outro post tarefas de implantação. Para obter mais informações, consulte o [usando o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artigo. |
| AzureContext | PSObject |  |
| ResourceGroupName | Cadeia de caracteres |  |
| ResourceGroupLocation | Cadeia de caracteres |  |
| BillingModel | Cadeia de caracteres | O modelo de cobrança que usa sua assinatura. Valores para esse parâmetro permitidos são: capacidade, PayAsYouUse e desenvolvimento. |
| MarketplaceSyndicationEnabled | Verdadeiro/Falso |  |
| UsageReportingEnabled | Verdadeiro/Falso | O Azure Stack relata as métricas de uso por padrão. Os operadores com usos de capacidade ou dar suporte a um ambiente desconectado serão necessário desativar o relatório de uso. Valores para esse parâmetro permitidos são: True, False. |
| AgreementNumber | Cadeia de caracteres |  |


## <a name="next-steps"></a>Próximas etapas

[Baixar itens do marketplace do Azure](azure-stack-download-azure-marketplace-item.md)
