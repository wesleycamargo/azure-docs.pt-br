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
ms.date: 11/19/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: e86ff4ebf91d0c0b691caf429d9489bf769f16af
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975185"
---
# <a name="azure-stack-registration"></a>Registro de pilha do Azure
Você pode registrar sua instalação do Kit de desenvolvimento na pilha do Azure (ASDK) com o Azure para baixar itens do marketplace do Azure e configurar dados de comércio relatórios de volta para a Microsoft. Registro é necessário para dar suporte à funcionalidade completa do Azure Stack, incluindo a sindicalização do marketplace. Registro é recomendado porque ele permite que você teste funcionalidades importantes da pilha do Azure, como o relatório de uso e distribuição de mercado. Depois de registrar o Azure Stack, o uso é relatado para comércio do Azure. Você pode vê-lo sob a assinatura que você usou para o registro. No entanto, os usuários ASDK não são cobrados por qualquer uso que eles relatam.

Se você não registrou sua ASDK, talvez você veja uma **ativação necessária** alerta de aviso que recomenda que você registre seu Kit de desenvolvimento do Azure Stack. O comportamento é esperado.

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

2. Execute os seguintes comandos do PowerShell para registrar sua instalação ASDK com o Azure. Você precisará entrar em sua assinatura do Azure e a instalação ASDK local. Se você não tiver uma assinatura do Azure, você pode [criar uma conta gratuita do Azure aqui](https://azure.microsoft.com/free/?b=17.06). Registrar o Azure Stack incorre em sua assinatura do Azure sem custo.<br><br>Se você estiver executando o script de registro em mais de uma instância do Azure Stack usando a mesma ID de assinatura do Azure, defina um nome exclusivo para o registro quando você executa o **AzsRegistration conjunto** cmdlet. O **RegistrationName** parâmetro tem um valor padrão de **AzureStackRegistration**. No entanto, se você usar o mesmo nome em mais de uma instância do Azure Stack, o script falhará.

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```
3. Quando o script for concluído, você deverá ver esta mensagem: **seu ambiente agora está registrado e ativada usando os parâmetros fornecidos.**

    ![Seu ambiente agora está registrado](media/asdk-register/1.PNG)


## <a name="register-in-disconnected-environments"></a>Registre-se em ambientes desconectados
Se você estiver registrando o Azure Stack em um ambiente desconectado (sem conectividade com a internet), você precisará obter um registro de token do ambiente do Azure Stack e, em seguida, usar esse token em um computador que pode se conectar ao Azure para registrar e criar uma ativação recursos para o seu ambiente ASDK.
 
 > [!IMPORTANT]
 > Antes de usar estas instruções para registrar o Azure Stack, certifique-se de que você instalou o PowerShell para o Azure Stack e baixei as ferramentas do Azure Stack, conforme descrito na [configuração pós-implantação](asdk-post-deploy.md) artigo no host ASDK computador e o computador com acesso à internet usado para se conectar ao Azure e registre-se.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obter um registro de token do ambiente do Azure Stack
No computador host ASDK, inicie o PowerShell como administrador e navegue até a **registro** pasta o **AzureStack-Tools-master** diretório criado quando você baixar as ferramentas do Azure Stack. Use os seguintes comandos do PowerShell para importar os **RegisterWithAzure.psm1** módulo e, em seguida, use o **Get-AzsRegistrationToken** para obter o token de registro:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```
Por padrão, o token de registro é salvo no arquivo especificado para *$FilePathForRegistrationToken* parâmetro. Você pode alterar o caminho do arquivo ou nome de arquivo a seu critério.

Salve esse token de registro para uso no computador conectado à internet. Você pode copiar o arquivo ou o texto de $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Conectar-se ao Azure e ao registro
No computador conectado à internet, inicie o PowerShell como administrador e navegue até a **registro** pasta o **AzureStack-Tools-master** diretório criado quando você baixou do Azure Ferramentas de pilha. Use os seguintes comandos do PowerShell para importar os **RegisterWithAzure.psm1** módulo e, em seguida, use o **Register AzsEnvironment** cmdlet para se registrar com o Azure, fornecendo o registro de token acabou de criar e um nome exclusivo de registro:  

  ```PowerShell  
  $registrationToken = "<your registration token>"
  $RegistrationName = "<unique registration name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Como alternativa, você pode usar o **Get-Content** cmdlet para apontar para um arquivo que contém seu token de registro:

  ```PowerShell  
  $registrationToken = Get-Content -Path '<path>\<registration token file>'
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Salve o token de registro e o nome do recurso de registro para referência futura.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Recuperar uma chave de ativação do recurso de registro do Azure

Ainda usando o computador conectado à internet, recupere uma chave de ativação do recurso do registro criado quando você registrou com o Azure.

Para obter a chave de ativação, execute os seguintes comandos do PowerShell, use o mesmo valor de nome exclusivo de registro que você forneceu quando registrou com o Azure na etapa anterior:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```

A chave de ativação é salvo no arquivo especificado para *$KeyOutputFilePath*. Você pode alterar o caminho do arquivo ou nome de arquivo a seu critério.

### <a name="create-an-activation-resource-in-azure-stack"></a>Criar um recurso de ativação no Azure Stack

Retornar para o ambiente do Azure Stack com o arquivo ou o texto da chave de ativação criado a partir **Get-AzsActivationKey**. Execute os seguintes comandos do PowerShell para criar um recurso de ativação no Azure Stack usando essa chave de ativação:   

  ```Powershell
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Como alternativa, você pode usar o **Get-Content** cmdlet para apontar para um arquivo que contém seu token de registro:

  ```Powershell
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = Get-Content -Path '<path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

## <a name="verify-the-registration-was-successful"></a>Verifique se que o registro foi bem-sucedido
Siga estas etapas para verificar se o registro ASDK com o Azure foi bem-sucedida.

1. Entrar para o [portal de administração do Azure Stack](https://adminportal.local.azurestack.external).

2. Clique em **gerenciamento do Marketplace** > **adicione do Azure**.

    ![](media/asdk-register/2.PNG)

3. Se você vir uma lista de itens disponíveis do Azure, a ativação foi bem-sucedida.

    ![](media/asdk-register/3.PNG)

## <a name="move-a-registration-resource"></a>Mover um recurso de registro
Mover um recurso de registro entre grupos de recursos na mesma assinatura **é** com suporte. Para obter mais informações sobre como mover recursos para um novo grupo de recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Próximas etapas
[Adicionar um item do marketplace do Azure Stack](.\.\azure-stack-marketplace.md)
