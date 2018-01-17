---
title: Sistemas integrados de registro do Azure para a pilha do Azure | Microsoft Docs
description: "Descreve o processo de registro do Azure para implantações de vários nós conectados do Azure de pilha do Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 03a6ce77eed16cbc5d2fe46094b0b6ac7fbc022e
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2018
---
# <a name="register-azure-stack-with-azure"></a>Registrar a pilha do Azure com o Azure
Você pode registrar a pilha do Azure com o Azure para baixar itens do marketplace do Azure e configurar dados de comércio relatadas à Microsoft. Depois de registrar a pilha do Azure, o uso é relatado para comércio do Azure. Você poderá ver isso na assinatura usado para registro.

> [!IMPORTANT]
> Registro será obrigatório se você escolher o modelo de cobrança de pagamento que você-uso. Caso contrário, será em violação dos termos de licenciamento da implantação da pilha do Azure como o uso de outra forma não será reportado.

## <a name="before-you-register-azure-stack-with-azure"></a>Antes de registrar a pilha do Azure com o Azure
Antes de registrar a pilha do Azure com o Azure, você deve ter:

- A ID de assinatura para uma assinatura do Azure. Para obter a ID, entre no Azure, clique em **mais serviços** > **assinaturas**, clique na assinatura que você deseja usar, e, em **Essentials** você pode encontrar o ID da assinatura. 

  > [!NOTE]
  > Na China, Alemanha e assinaturas de nuvem do governo dos EUA não têm suporte no momento. 

- O nome de usuário e a senha de uma conta que é proprietário da assinatura (há suporte para contas MSA/2FA)
- *Não são necessários a partir da versão de atualização de 1712 de pilha do Azure (180106.1)*: AD do Azure para a assinatura do Azure. Você pode encontrar esse diretório no Azure focalizando seu avatar no canto superior direito do portal do Azure. 
- Registrado o provedor de recursos da pilha do Azure (consulte a seção de registrar o provedor de recursos da pilha do Azure abaixo para obter detalhes)

Se você não tiver uma assinatura do Azure que atende a esses requisitos, você pode [criar uma conta gratuita do Azure aqui](https://azure.microsoft.com/free/?b=17.06). Registrando a pilha do Azure, incorre em sem custo na sua assinatura do Azure.

### <a name="bkmk_powershell"></a>Instale o PowerShell para a pilha do Azure
Você precisa usar o PowerShell mais recente para a pilha do Azure para registrar o sistema com o Azure.

Se ainda não estiver instalado, [instale o PowerShell para Azure pilha](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Baixar as ferramentas de pilha do Azure
O repositório do GitHub de ferramentas do Azure pilha contém módulos do PowerShell que dão suporte à funcionalidade de pilha do Azure; incluindo a funcionalidade de registro. Durante o registro do processo, você precisará importar e usar o módulo do PowerShell RegisterWithAzure.psm1, encontrado no repositório de ferramentas de pilha do Azure, para registrar sua instância de pilha do Azure com o Azure. 

```powershell
# Change directory to the root directory. 
cd \

# Download the tools archive.
  invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
  expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
  cd AzureStack-Tools-master
```

## <a name="register-azure-stack-in-connected-environments"></a>Registrar a pilha do Azure em ambientes conectados
Ambientes conectados podem acessar a internet e o Azure. Para esses ambientes, você precisa registrar o provedor de recursos da pilha do Azure com o Azure e, em seguida, configurar o modelo de cobrança.

### <a name="register-the-azure-stack-resource-provider"></a>Registrar o provedor de recursos da pilha do Azure
Para registrar o provedor de recursos da pilha do Azure com o Azure, inicie o ISE do Powershell como administrador e use os seguintes comandos do PowerShell. Esses comandos serão:
- Solicitar que você faça logon como proprietário da assinatura do Azure a ser usado e defina o `EnvironmentName` parâmetro **AzureCloud**.
- Registrar o provedor de recursos do Azure **Microsoft.AzureStack**.

PowerShell para executar:

```powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack 
```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Registrar a pilha do Azure com o Azure usando o modelo de cobrança de pagamento que você-uso
Use essas etapas para registrar a pilha do Azure com o Azure usando o modelo de cobrança de pagamento que você-uso.

Iniciar o ISE do PowerShell como administrador e navegue até o **registro** pasta o **mestre de ferramentas AzureStack** diretório criado quando você [baixado as ferramentas do Azure pilha](#bkmk_tools). Importar o **RegisterWithAzure.psm1** módulo usando o PowerShell: 

PowerShell para executar:

```powershell
Import-Module .\RegisterWithAzure.psm1
```
Em seguida, na mesma sessão do PowerShell, execute o **AzsRegistration conjunto** cmdlet. Quando solicitado a fornecer credenciais, especifique o proprietário da assinatura do Azure.  

PowerShell para executar:

```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|Parâmetro|DESCRIÇÃO|
|-----|-----|
|CloudAdminCredential|Objeto do PowerShell que contém informações de credenciais (nome de usuário e senha) para o proprietário da assinatura do Azure.|
|PrivilegedEndpoint|Um pré-configurado console remoto do PowerShell que fornece recursos, como a coleta de log e outra post tarefas de implantação. Para obter mais informações, consulte o [usando o ponto de extremidade com privilégios](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint) artigo.|
|BillingModel|O modelo de cobrança que usa sua assinatura. Valores para esse parâmetro permitidos são: capacidade, PayAsYouUse e desenvolvimento.|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Registrar a pilha do Azure com o Azure usando o modelo de cobrança de capacidade
Siga as mesmas instruções usadas para registrar usando o modelo de cobrança de pagamento que você-uso, mas adicione o número de contrato no qual foi adquirida capacidade e altere o `BillingModel` parâmetro **capacidade**. Todos os outros parâmetros são inalterados.

PowerShell para executar:
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Registrar a pilha do Azure em ambientes desconectados 
*As informações nesta seção se aplica a partir da versão de atualização do Azure pilha 1712 (180106.1) e não é compatível com versões anteriores.*

Se você estiver registrando pilha do Azure em um ambiente desconectado (sem conectividade com a internet), você precisa obter um registro de token do ambiente de pilha do Azure e, em seguida, usar esse token em um computador que pode se conectar ao Azure e tenha [PowerShell para a pilha do Azure instalado](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obter um registro de token do ambiente de pilha do Azure
  1. Para obter o token de registro, execute os seguintes comandos do PowerShell:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > O token de registro é salvo no arquivo especificado para *$env:SystemDrive\RegistrationToken.txt*.

  2. Salve esse token de registro para uso no Azure máquina conectada.


### <a name="connect-to-azure-and-register"></a>Conecte-se ao Azure e ao registro
Iniciar o ISE do PowerShell como administrador e navegue até o **registro** pasta o **mestre de ferramentas AzureStack** diretório criado quando você [baixado as ferramentas do Azure pilha](#bkmk_tools). Importar o **RegisterWithAzure.psm1** módulo: 

PowerShell para executar:
```powershell
Import-Module .\RegisterWithAzure.psm1
```
Em seguida, na mesma sessão do PowerShell, especifique o token de registro para registrar com o Azure:

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
> Salve o nome do recurso de registro ou o token de registro para referência futura.

## <a name="verify-azure-stack-registration"></a>Verificar o registro de pilha do Azure
Use estas etapas para verificar que o Azure pilha foi registrado com êxito com o Azure.
1. Entrar para a pilha do Azure [portal do administrador](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https &#58; / / adminportal. *&lt;região >. &lt;fqdn >*.
2. Clique em **mais serviços** > **Marketplace gerenciamento** > **adicionar do Azure**.

Se você vir uma lista de itens disponíveis do Azure (como o WordPress), a ativação foi bem-sucedida.

> [!NOTE]
> Após a conclusão do registro, o aviso de ativo para não registrar não mais aparecerá.

## <a name="renew-or-change-registration"></a>Renovar ou alterar o registro
Você precisará atualizar ou renovar seu registro nas seguintes circunstâncias:
- Depois que você renovar sua assinatura anual baseado na capacidade.
- Quando você altera o modelo de cobrança.
- Quando você expandir alterações (Adicionar ou remover nós) para cobrança baseado na capacidade.

### <a name="change-the-subscription-you-use"></a>Alterar a assinatura que você usar
Se você quiser alterar a assinatura usar, primeiro você deve executar o **AzsRegistration remover** cmdlet, em seguida, certifique-se de que você está conectado ao contexto correto do PowerShell do Azure e finalmente executar **AzsRegistration conjunto**  com qualquer alterado parâmetros:

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>Alterar os recursos de distribuição ou de modelo de cobrança
Se você quiser alterar o modelo de cobrança ou recursos de distribuição para a instalação, você pode chamar a função de registro para definir os novos valores. Você não precisa primeiro remover o registro atual: 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

## <a name="remove-a-registered-resource"></a>Remover um recurso registrado
Se você deseja remover um registro, você deve usar **UnRegister-AzsEnvironment** cmdlet e passe o nome do recurso de registro ou o registro de token é usado para **Register-AzsEnvironment**.

Para remover um registro usando um nome de recurso:

```Powershell    
UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
```
Para remover um registro usando um token de registro:

```Powershell
$registrationToken = "*Your copied registration token*"
UnRegister-AzsEnvironment -RegistrationToken $registrationToken
```

