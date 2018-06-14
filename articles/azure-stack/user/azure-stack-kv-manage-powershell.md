---
title: Gerenciar o Cofre de chaves na pilha do Azure usando o PowerShell | Microsoft Docs
description: Saiba como gerenciar o Cofre de chaves na pilha do Azure usando o PowerShell
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.openlocfilehash: 5e9de401f64a835c286c226bfac88caf5168b96e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075758"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Gerenciar cofre de chaves na pilha do Azure usando o PowerShell

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode gerenciar o Cofre de chaves na pilha do Azure usando o PowerShell. Saiba como usar os cmdlets do PowerShell do Cofre de chave para:

* Crie um cofre da chave.
* Armazene e gerencie os segredos e chaves de criptografia.
* Autorize usuários ou aplicativos para invocar operações no cofre.

>[!NOTE]
>Os cmdlets PowerShell do Cofre de chave descritos neste artigo são fornecidas no SDK do PowerShell do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Você deve assinar uma oferta que inclui o serviço de Cofre de chaves do Azure.
* [Instale o PowerShell para Azure pilha](azure-stack-powershell-install.md).
* [Configurar o ambiente do PowerShell do usuário do Azure pilha](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Habilite sua assinatura de locatário para operações do Cofre de chaves

Antes de poder emitir todas as operações em um cofre de chaves, você precisa garantir que sua assinatura de locatário está habilitada para operações do cofre. Para verificar se as operações do cofre estão habilitadas, execute o seguinte comando:

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**Saída**

Se sua assinatura está habilitada para operações do cofre, a saída mostra "RegistrationState" é "registrado" para todos os tipos de recursos de um cofre de chaves.

![Estado do cofre da chave de registro](media/azure-stack-kv-manage-powershell/image1.png)

Se as operações do Cofre não estiverem habilitadas, invocar o comando a seguir para registrar o serviço de Cofre de chaves em sua assinatura:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Saída**

Se o registro for bem-sucedido, será retornada a seguinte saída:

![Registrar](media/azure-stack-kv-manage-powershell/image2.png) quando você invoca os comandos de Cofre de chaves, você poderá receber um erro, como "a assinatura não está registrada para usar o namespace 'Microsoft.KeyVault'." Se você receber um erro, verifique se você tem [habilitado o provedor de recursos do Cofre de chaves](#enable-your-tenant-subscription-for-vault-operations) seguindo as instruções que foram mencionadas anteriormente.

## <a name="create-a-key-vault"></a>Criar um cofre de chave

Antes de criar um cofre de chaves, crie um grupo de recursos para que todos os recursos relacionados ao Cofre de chave existem em um grupo de recursos. Use o comando a seguir para criar um novo grupo de recursos:

```PowerShell
New-AzureRmResourceGroup -Name “VaultRG” -Location local -verbose -Force

```

**Saída**

![novo grupo de recursos](media/azure-stack-kv-manage-powershell/image3.png)

Agora, use o **AzureRMKeyVault novo** comando para criar um cofre de chaves no grupo de recursos que você criou anteriormente. Este comando lê a três parâmetros obrigatórios: nome do grupo de recursos, o nome do Cofre de chaves e a localização geográfica.

Execute o seguinte comando para criar um cofre de chaves:

```PowerShell
New-AzureRmKeyVault -VaultName “Vault01” -ResourceGroupName “VaultRG” -Location local -verbose
```

**Saída**

![Novo cofre de chaves](media/azure-stack-kv-manage-powershell/image4.png)

A saída desse comando mostra as propriedades do cofre da chave que você criou. Quando um aplicativo acessa o cofre, ele deverá usar o **URI do cofre** propriedade, que é "https://vault01.vault.local.azurestack.external" neste exemplo.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Implantação do Active Directory Federation Services (AD FS)

Em uma implantação do AD FS, você poderá receber este aviso: "política de acesso não está definida. Nenhum usuário ou aplicativo tem permissão de acesso para usar este cofre." Para resolver esse problema, defina uma política de acesso para o cofre usando o [Set-AzureRmKeyVaultAccessPolicy](azure-stack-kv-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) comando:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Gerenciar chaves e segredos

Depois de criar um cofre, use as etapas a seguir para criar e gerenciar chaves e segredos no cofre.

### <a name="create-a-key"></a>Criar uma chave

Use o **Add-AzureKeyVaultKey** comando para criar ou importar uma chave protegida por software em um cofre de chaves.

```PowerShell
Add-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01” -verbose -Destination Software
```

O **destino** parâmetro é usado para especificar que a chave está protegida de software. Quando a chave é criada com êxito, o comando exibe os detalhes da chave criada.

**Saída**

![Nova chave](media/azure-stack-kv-manage-powershell/image5.png)

Agora você pode fazer referência a chave criada usando seu URI. Se você cria ou importa uma chave que tem o mesmo nome de uma chave existente, a chave original é atualizada com os valores especificados na nova chave. Você pode acessar a versão anterior usando o URI específico da versão da chave. Por exemplo: 

* Use "https://vault10.vault.local.azurestack.external:443/keys/key01" sempre obter a versão atual.
* Use "https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a" para obter a versão específica.

### <a name="get-a-key"></a>Obter uma chave

Use o **Get-AzureKeyVaultKey** comando ler uma chave e seus detalhes.

```PowerShell
Get-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01”
```

### <a name="create-a-secret"></a>Criar um segredo

Use o **AzureKeyVaultSecret conjunto** comando para criar ou atualizar um segredo em um cofre. Um segredo é criado se ainda não existir. Uma nova versão do segredo é criada se ele já existe.

```PowerShell
$secretvalue = ConvertTo-SecureString “User@123” -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01” -SecretValue $secretvalue
```

**Saída**

![Criar um segredo](media/azure-stack-kv-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Obter um segredo

Use o **AzureKeyVaultSecret Get** comando ler um segredo em um cofre de chaves. Este comando pode retornar todos ou versões específicas de um segredo.

```PowerShell
Get-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01”
```

Depois de criar as chaves e segredos, você pode autorizar aplicativos externos para usá-los.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Autorizar um aplicativo para usar uma chave ou segredo

Use o **Set-AzureRmKeyVaultAccessPolicy** comando para autorizar um aplicativo para acessar uma chave ou segredo no cofre de chaves.
No exemplo a seguir, é o nome do cofre *ContosoKeyVault* e o aplicativo que você deseja autorizar tiver uma ID de cliente *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*. Para autorizar o aplicativo, execute o seguinte comando. Opcionalmente, você pode especificar o **PermissionsToKeys** para definir permissões para um usuário, aplicativo ou um grupo de segurança.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Se você deseja autorizar o mesmo aplicativo para ler segredos em seu cofre, execute o seguinte cmdlet:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Próximas etapas

* [Implantar uma VM com uma senha armazenada no cofre de chaves](azure-stack-kv-deploy-vm-with-secret.md)
* [Implantar uma VM com um certificado armazenado no cofre de chaves](azure-stack-kv-push-secret-into-vm.md)
