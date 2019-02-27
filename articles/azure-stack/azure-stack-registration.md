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
ms.date: 02/14/2019
ms.author: jeffgilb
ms.reviewer: brbartle
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 1fb9917e30a88cf5a68c9e170d66eeaa1d477deb
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877139"
---
# <a name="register-azure-stack-with-azure"></a>Registre-se a pilha do Azure com o Azure

Registrar o Azure Stack com o Azure permite que você para baixar itens do marketplace do Azure e configurar dados de comércio relatórios de volta para a Microsoft. Depois de registrar o Azure Stack, o uso é relatado para comércio do Azure e você pode vê-lo sob a ID da assinatura cobrança do Azure usados para o registro.

As informações neste artigo descrevem o registro sistemas integrados do Azure Stack com o Azure. Para obter informações sobre como registrar o ASDK com o Azure, consulte [registro do Azure Stack](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-register) na documentação do ASDK.

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

- A ID de assinatura para uma assinatura do Azure. Apenas EA, CSP ou CSP compartilhado assinaturas têm suporte para registro de serviços. CSPs precisam decidir se deseja [usar uma assinatura do CSP ou APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>Para obter a ID, entre no Azure, clique em **todos os serviços**. Em seguida, na **gerais** categoria, selecione **assinaturas**, clique na assinatura que você deseja usar, e, em **Essentials** você pode encontrar a ID da assinatura.

  > [!Note]  
  > Atualmente, não há suporte para assinaturas de nuvem da Alemanha.

- O nome de usuário e senha para uma conta que seja um proprietário da assinatura.

- A conta de usuário precisa ter acesso à assinatura do Azure e tem permissões para criar aplicativos de identidades e entidades de serviço no diretório associado a essa assinatura. É recomendável que você registre o Azure Stack com o Azure usando a administração de privilégios mínimos. Para obter mais informações sobre como criar uma definição de função personalizada que limita o acesso à sua assinatura para o registro, consulte [criar uma função de registro para o Azure Stack](azure-stack-registration-role.md).

- Registrar o provedor de recursos do Azure Stack (consulte a seção a seguir registrar provedor de recursos do Azure Stack para obter detalhes).

Após o registro, a permissão de administrador global do Azure Active Directory não é necessária. No entanto, algumas operações podem exigir a credencial de administrador global. Por exemplo, um script de instalador do provedor de recursos ou um novo recurso que exigem uma permissão para ser concedida. Você temporariamente pode reaplicar as permissões da conta administrador global ou usar uma conta de administrador global separado que é proprietário do *padrão de assinatura do provedor*.

O usuário que registra o Azure Stack é o proprietário do serviço principal no Azure Active Directory. Somente o usuário que registrou o Azure Stack pode modificar o registro do Azure Stack. Se um usuário não administrador que não é um proprietário da entidade de serviço de registro tentar se registrar ou registrar novamente o Azure Stack, eles podem encontrar uma resposta 403. Uma resposta 403 indica que o usuário não tem permissões suficientes para concluir a operação.

Se você não tiver uma assinatura do Azure que atende a esses requisitos, você poderá [criar uma conta gratuita do Azure aqui](https://azure.microsoft.com/free/?b=17.06). Registrar o Azure Stack incorre em sua assinatura do Azure sem custo.

### <a name="powershell-language-mode"></a>Modo de linguagem do PowerShell

Para registrar com êxito o Azure Stack, o modo de linguagem do PowerShell deve ser definido como **FullLanguageMode**.  Para verificar se o modo de linguagem atual está definido como completo, abra uma janela do PowerShell com privilégios elevados e execute os seguintes cmdlets do PowerShell:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Certifique-se a saída retorna **FullLanguageMode**. Se qualquer outro modo de linguagem é retornado, registro precisa ser executado em outro computador ou o modo de linguagem deve ser definido como **FullLanguageMode** antes de continuar.

### <a name="install-powershell-for-azure-stack"></a>Instale o PowerShell para o Azure Stack

Use o PowerShell mais recente para o Azure Stack para registrar com o Azure.

Se a versão mais recente já não estiver instalada, consulte [instalar o PowerShell para Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

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

### <a name="determine-a-unique-registration-name-to-use"></a>Determinar um nome exclusivo de registro para usar 
Quando você registra o Azure Stack com o Azure, você deve fornecer um nome exclusivo do registro. Uma maneira fácil de associar sua assinatura do Azure Stack com o registro do Azure é usar o Azure Stack **ID da nuvem**. 

> [!NOTE]
> Registros de pilha do Azure usando o modelo de cobrança com base em capacidade serão necessário alterar o nome exclusivo ao registrar novamente depois que as assinaturas anuais expirarem, a menos que você [excluir o registro expirado](azure-stack-registration.md#change-the-subscription-you-use) e registrar novamente com Azure.

Para determinar a ID de nuvem para sua implantação do Azure Stack, abra o PowerShell como administrador em um computador que pode acessar o ponto de extremidade com privilégios, execute os seguintes comandos, e registre os **CloudID** valor: 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Registre-se conectado com cobrança pré-paga

Use estas etapas para registrar o Azure Stack com o Azure usando o modelo de cobrança pagamento-como-o uso.

> [!Note]  
> Todas essas etapas devem ser executadas em um computador que tem acesso ao ponto de extremidade com privilégios (PEP). Para obter detalhes sobre o PEP, consulte [usando o ponto de extremidade privilegiado no Azure Stack](azure-stack-privileged-endpoint.md).

Ambientes conectados podem acessar a internet e o Azure. Para esses ambientes, você precisa registrar o provedor de recursos do Azure Stack com o Azure e, em seguida, configurar o modelo de cobrança.

1. Para registrar o provedor de recursos do Azure Stack com o Azure, inicie o PowerShell ISE como administrador e use os seguintes cmdlets do PowerShell com o **EnvironmentName** parâmetro definido como o tipo de assinatura do Azure apropriada (consulte parâmetros abaixo).

2. Adicione a conta do Azure que você usa para registrar o Azure Stack. Para adicionar a conta, execute as **Add-AzureRmAccount** cmdlet. Você for solicitado a inserir suas credenciais de conta do Azure e você talvez precise usar a autenticação de fator de 2, com base na configuração da sua conta.

   ```PowerShell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
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

5. Inicie o PowerShell ISE como administrador e navegue até a **registro** pasta o **AzureStack-Tools-master** diretório criado quando você baixar as ferramentas do Azure Stack. Importar o **RegisterWithAzure.psm1** módulo usando o PowerShell:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Em seguida, na mesma sessão do PowerShell, verifique se que você está conectado ao Azure PowerShell contexto correto. Essa é a conta do Azure que foi usada para registrar o provedor de recursos do Azure Stack anteriormente. PowerShell para executar:

   ```PowerShell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Parâmetro | DESCRIÇÃO |  
   |-----|-----|
   | EnvironmentName | O nome de ambiente de assinatura de nuvem do Azure. Nomes de ambiente com suporte são **AzureCloud**, **AzureUSGovernment**, ou se usando uma assinatura do Azure na China, **AzureChinaCloud**.  |

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

  O processo leva entre 10 e 15 minutos. Quando o comando for concluído, você verá a mensagem **"seu ambiente agora está registrado e ativado usando os parâmetros fornecidos."**

## <a name="register-connected-with-capacity-billing"></a>Registre-se conectado com a cobrança de capacidade

Use estas etapas para registrar o Azure Stack com o Azure usando o modelo de cobrança pagamento-como-o uso.

> [!Note]  
> Todas essas etapas devem ser executadas em um computador que tem acesso ao ponto de extremidade com privilégios (PEP). Para obter detalhes sobre o PEP, consulte [usando o ponto de extremidade privilegiado no Azure Stack](azure-stack-privileged-endpoint.md).

Ambientes conectados podem acessar a internet e o Azure. Para esses ambientes, você precisa registrar o provedor de recursos do Azure Stack com o Azure e, em seguida, configurar o modelo de cobrança.

1. Para registrar o provedor de recursos do Azure Stack com o Azure, inicie o PowerShell ISE como administrador e use os seguintes cmdlets do PowerShell com o **EnvironmentName** parâmetro definido como o tipo de assinatura do Azure apropriada (consulte parâmetros abaixo).

2. Adicione a conta do Azure que você usa para registrar o Azure Stack. Para adicionar a conta, execute as **Add-AzureRmAccount** cmdlet. Você for solicitado a inserir suas credenciais de conta do Azure e você talvez precise usar a autenticação de fator de 2, com base na configuração da sua conta.

   ```PowerShell  
   Connect-AzureRmAccount -Environment "<environment name>"
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

5. Inicie o PowerShell ISE como administrador e navegue até a **registro** pasta o **AzureStack-Tools-master** diretório criado quando você baixar as ferramentas do Azure Stack. Importar o **RegisterWithAzure.psm1** módulo usando o PowerShell:

  ```PowerShell  
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  $RegistrationName = "<unique-registration-name>"
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
  ```
   > [!Note]  
   > Você pode desabilitar o uso dos relatórios com o parâmetro UsageReportingEnabled para o **AzsRegistration conjunto** cmdlet, definindo o parâmetro como false. 
   
  Para obter mais informações sobre o cmdlet Set-AzsRegistration, consulte [referência de registro](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>Registrar desconectada com a cobrança de capacidade

Se você estiver registrando o Azure Stack em um ambiente desconectado (sem conectividade com a internet), você precisará obter um registro de token do ambiente do Azure Stack e, em seguida, usar esse token em um computador que pode se conectar ao Azure e tem o PowerShell para o Azure Stack instalado.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obter um registro de token do ambiente do Azure Stack

1. Inicie o PowerShell ISE como administrador e navegue até a **registro** pasta o **AzureStack-Tools-master** diretório criado quando você baixar as ferramentas do Azure Stack. Importar o **RegisterWithAzure.psm1** módulo:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Para obter o token de registro, execute os seguintes cmdlets do PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Para obter mais informações sobre o cmdlet Get-AzsRegistrationToken, consulte [referência de registro](#registration-reference).

   > [!Tip]  
   > O token de registro é salvo no arquivo especificado para *$FilePathForRegistrationToken*. Você pode alterar o caminho do arquivo ou nome de arquivo a seu critério.

3. Salve esse token de registro para uso no Azure máquina conectada. Você pode copiar o arquivo ou o texto de $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Conectar-se ao Azure e ao registro

No computador que está conectado à Internet, execute as mesmas etapas para importar o módulo RegisterWithAzure.psm1 e entrar no contexto correto do Azure Powershell. Em seguida, chame AzsEnvironment Register. Especifique o token de registro para registrar com o Azure. Se você estiver registrando a mais de uma instância do Azure Stack usando a mesma ID de assinatura do Azure, especifique um nome exclusivo do registro. Execute o cmdlet a seguir:

  ```PowerShell  
  $RegistrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

Opcionalmente, você pode usar o cmdlet Get-Content para apontar para um arquivo que contém seu token de registro:

  ```PowerShell  
  $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

  > [!Note]  
  > Salve o nome do recurso de registro e o token de registro para referência futura.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Recuperar uma chave de ativação do recurso de registro do Azure

Em seguida, você precisa recuperar uma chave de ativação do recurso do registro criado no Azure durante o registro AzsEnvironment.

Para obter a chave de ativação, execute os seguintes cmdlets do PowerShell:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > A chave de ativação é salvo no arquivo especificado para *$KeyOutputFilePath*. Você pode alterar o caminho do arquivo ou nome de arquivo a seu critério.

### <a name="create-an-activation-resource-in-azure-stack"></a>Criar um recurso de ativação no Azure Stack

Retornar para o ambiente do Azure Stack com o arquivo ou o texto da chave de ativação criado a partir de Get-AzsActivationKey. Em seguida, você cria um recurso de ativação no Azure Stack usando essa chave de ativação. Para criar um recurso de ativação, execute os seguintes cmdlets do PowerShell: 

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

Você pode usar o **gerenciamento de região** lado a lado para verificar se o registro do Azure Stack foi bem-sucedida. Esse bloco está disponível no painel no portal do administrador padrão. O status pode ser registrado ou não registrado. Se registrado, ele também mostra a ID da assinatura do Azure que é usado para registrar seu Azure Stack, juntamente com o grupo de recursos de registro e o nome.

1. Entrar para o [portal de administração do Azure Stack](https://adminportal.local.azurestack.external).

2. O painel, selecione **gerenciamento de região**.

3. Selecione **Propriedades**. Essa folha mostra o status e os detalhes do seu ambiente. O status pode ser **registrado** ou **não registrado**.

    [![Bloco de gerenciamento de região](media/azure-stack-registration/admin1sm.png "bloco de gerenciamento de região")](media/azure-stack-registration/admin1.png#lightbox)

    Se registrado, as propriedades incluem:
    
    - **ID de assinatura de registro**: A ID de assinatura do Azure registrados e associados ao Azure Stack
    - **Grupo de recursos de registro**: O grupo de recursos do Azure na assinatura associada que contém os recursos do Azure Stack.

4. Use o portal do Azure para exibir os registros de aplicativo do Azure Stack. Entrar no portal do Azure usando uma conta associada à assinatura usada para registrar o Azure Stack. Alterne para o locatário associado com o Azure Stack.
5. Navegue até **Azure Active Directory > registros de aplicativo > Exibir todos os aplicativos**.

    ![Registros de aplicativo](media/azure-stack-registration/app-registrations.png)

    Registros de aplicativo do Azure Stack são prefixados com **do Azure Stack**.

Como alternativa, você pode verificar se o seu registro foi bem-sucedido usando o recurso de gerenciamento do Marketplace. Se você vir uma lista de itens do marketplace na folha de gerenciamento do Marketplace, seu registro foi bem-sucedido. No entanto, em ambientes desconectados, você não poderá ver os itens do marketplace no gerenciamento do Marketplace. No entanto, você pode usar a ferramenta offline para verificar o registro.

> [!NOTE]
> Após a conclusão do registro, o Active Directory aviso por não registrar não aparecerá mais. Em cenários desconectados, você verá uma mensagem no gerenciamento de Marketplace solicitando que você se registrar e ativar o Azure Stack, mesmo se você tiver registrado com êxito.

## <a name="renew-or-change-registration"></a>Renovar ou alterar o registro

### <a name="renew-or-change-registration-in-connected-environments"></a>Renovar ou alterar o registro em ambientes conectados

Você precisará atualizar ou renovar seu registro nas seguintes circunstâncias:

- Depois de você renovar sua assinatura anual com base em capacidade.
- Quando você altera o modelo de cobrança.
- Quando você dimensionar as alterações (Adicionar ou remover nós) para cobrança baseada em capacidade.

#### <a name="change-the-subscription-you-use"></a>Alterar a assinatura que você usa

Se você gostaria de alterar a assinatura é usar, primeiro você deve executar o **AzsRegistration remover** cmdlet, em seguida, verifique se você efetuou logon no contexto correto do Azure PowerShell e finalmente executar **AzsRegistration conjunto**  com todos os parâmetros alterados incluindo \<modelo de cobrança\>:

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Alterar o modelo de cobrança ou como oferecer recursos

Se você quiser alterar o modelo de cobrança ou como oferecer recursos de sua instalação, você pode chamar a função de registro para definir os novos valores. Você não precisará primeiro remover o registro atual:

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Renovar ou alterar o registro em ambientes desconectados

Você precisará atualizar ou renovar seu registro nas seguintes circunstâncias:

- Depois de você renovar sua assinatura anual com base em capacidade.
- Quando você altera o modelo de cobrança.
- Quando você dimensionar as alterações (Adicionar ou remover nós) para cobrança baseada em capacidade.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Remover o recurso de ativação do Azure Stack

Primeiro você precisa remover o recurso de ativação do Azure Stack e, em seguida, o recurso de registro no Azure.  

Para remover o recurso de ativação no Azure Stack, execute os seguintes cmdlets do PowerShell em seu ambiente do Azure Stack:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Em seguida, para remover o recurso de registro no Azure, verifique se você estiver usando um Azure conectado computador, entre no contexto correto do Azure PowerShell e execute os cmdlets do PowerShell apropriados, conforme descrito abaixo.

Você pode usar o token de registro usado para criar o recurso:  

  ```Powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

Ou você pode usar o nome de registro:

  ```Powershell
  $RegistrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $RegistrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Registre novamente usando as etapas desconectadas

Você agora tem completamente cancelado em um cenário desconectado e deve repetir as etapas para registrar um ambiente do Azure Stack em um cenário desconectado.

### <a name="disable-or-enable-usage-reporting"></a>Desabilitar ou habilitar o relatório de uso

Por ambientes Azure Stack que usam um modelo de cobrança de capacidade, desative o uso dos relatórios com o **UsageReportingEnabled** parâmetro usando o **AzsRegistration conjunto** ou o  **Get-AzsRegistrationToken** cmdlets. O Azure Stack relata as métricas de uso por padrão. Os operadores com usos de capacidade ou dar suporte a um ambiente desconectado precisa desativar o relatório de uso.

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

## <a name="move-a-registration-resource"></a>Mover um recurso de registro
Mover um recurso de registro entre grupos de recursos na mesma assinatura **é** com suporte para todos os ambientes. No entanto, mover um recurso de registro entre as assinaturas é suportado apenas para os CSPs quando ambas as assinaturas resolvem para a mesma ID de parceiro. Para obter mais informações sobre como mover recursos para um novo grupo de recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="registration-reference"></a>Referência de registro

### <a name="set-azsregistration"></a>Set-AzsRegistration

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

| Parâmetro | Type | DESCRIÇÃO |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | As credenciais usadas para [acessar o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). O nome de usuário está no formato **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Cadeia de caracteres | Um pré-configurados console remoto do PowerShell que fornece recursos como coleta de logs e outro post tarefas de implantação. Para obter mais informações, consulte o [usando o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artigo. |
| AzureContext | PSObject |  |
| ResourceGroupName | Cadeia de caracteres |  |
| ResourceGroupLocation | Cadeia de caracteres |  |
| BillingModel | Cadeia de caracteres | O modelo de cobrança que usa sua assinatura. Os valores permitidos para esse parâmetro são: Capacidade, PayAsYouUse e desenvolvimento. |
| MarketplaceSyndicationEnabled | Verdadeiro/Falso | Determina se o recurso de gerenciamento do marketplace está disponível no portal. Defina como verdadeiro se registrar com conectividade com a internet. Defina como false se o registro em ambientes desconectados. Para registros desconectados, o [ferramenta de sindicalização offline](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) pode ser usado para baixar itens do marketplace. |
| UsageReportingEnabled | Verdadeiro/Falso | O Azure Stack relata as métricas de uso por padrão. Os operadores com usos de capacidade ou dar suporte a um ambiente desconectado precisa desativar o relatório de uso. Os valores permitidos para esse parâmetro são: True, False. |
| AgreementNumber | Cadeia de caracteres |  |
| RegistrationName | Cadeia de caracteres | Defina um nome exclusivo para o registro, se você estiver executando o script de registro em mais de uma instância do Azure Stack usando a assinatura do Azure mesmo ID. O parâmetro tem um valor padrão de **AzureStackRegistration**. No entanto, se você usar o mesmo nome em mais de uma instância do Azure Stack, o script falhará. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken gera um token de registro de parâmetros de entrada.

```PowerShell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Parâmetro | Type | DESCRIÇÃO |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | As credenciais usadas para [acessar o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). O nome de usuário está no formato **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Cadeia de caracteres |  Um pré-configurados console remoto do PowerShell que fornece recursos como coleta de logs e outro post tarefas de implantação. Para obter mais informações, consulte o [usando o ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artigo. |
| AzureContext | PSObject |  |
| ResourceGroupName | Cadeia de caracteres |  |
| ResourceGroupLocation | Cadeia de caracteres |  |
| BillingModel | Cadeia de caracteres | O modelo de cobrança que usa sua assinatura. Os valores permitidos para esse parâmetro são: Capacidade, PayAsYouUse e desenvolvimento. |
| MarketplaceSyndicationEnabled | Verdadeiro/Falso |  |
| UsageReportingEnabled | Verdadeiro/Falso | O Azure Stack relata as métricas de uso por padrão. Os operadores com usos de capacidade ou dar suporte a um ambiente desconectado precisa desativar o relatório de uso. Os valores permitidos para esse parâmetro são: True, False. |
| AgreementNumber | Cadeia de caracteres |  |

## <a name="registration-failures"></a>Falhas de registro

Você pode ver um dos erros a seguir durante a tentativa de registro do Azure Stack:
1. Não foi possível recuperar informações de hardware obrigatório para $hostName. Por favor, verifique a conectividade e o host físico e tente executar novamente o registro.
2. Não é possível conectar ao $hostName para obter informações sobre o hardware - Verifique a conectividade e o host físico e tente executar novamente o registro.

Causa: Isso é normalmente porque podemos tentar obter os detalhes de hardware, como o UUID, Bios e CPU do host para tentar a ativação e não foi possível devido à incapacidade de se conectar ao host físico.

## <a name="next-steps"></a>Próximas etapas

[Baixar itens do marketplace do Azure](azure-stack-download-azure-marketplace-item.md)
