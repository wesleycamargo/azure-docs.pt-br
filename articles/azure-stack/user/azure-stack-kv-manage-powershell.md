---
title: Gerenciar o Cofre de chaves na pilha do Azure usando o PowerShell | Microsoft Docs
description: Saiba como gerenciar o Cofre de chaves na pilha do Azure usando o PowerShell
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: e920ee20268f5f43592e5a27fe82dcf27cb85af1
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2017
---
# <a name="manage-key-vault-in-azure-stack-by-using-powershell"></a>Gerenciar o Cofre de chaves na pilha do Azure usando o PowerShell

Este artigo ajuda você a começar a criar e gerenciar Cofre de chaves na pilha do Azure usando o PowerShell. Cmdlets do PowerShell do Cofre de chave descritos neste artigo estão disponíveis como parte do SDK do PowerShell do Azure. As seções a seguir descrevem os cmdlets do PowerShell que são necessários para:
   - Crie um cofre. 
   - Armazene e gerencie os segredos e chaves de criptografia. 
   - Autorize usuários ou aplicativos para invocar operações no cofre. 

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

Se sua assinatura está habilitada para operações do cofre, a saída mostra "RegistrationState" é igual a "Registrado" para todos os tipos de recursos de um cofre de chaves.

![Estado do registro](media/azure-stack-kv-manage-powershell/image1.png)

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

A saída desse comando mostra as propriedades do cofre da chave que você criou. Quando um aplicativo acessa o cofre, ele usa o **URI do cofre** propriedade mostrada na saída. Por exemplo, o identificador de recurso uniforme (URI) do cofre nesse caso é "https://vault01.vault.local.azurestack.external". Aplicativos que interagem com este cofre de chaves por meio da API REST devem usar esse URI.

Nos serviços de Federação do Active Directory (AD FS)-implantações com base, quando você cria uma chave de cofre usando o PowerShell, você poderá receber um aviso que diz "política de acesso não está definida. Nenhum usuário ou aplicativo tem permissão de acesso para usar este cofre." Para resolver esse problema, defina uma política de acesso para o cofre usando o [Set-AzureRmKeyVaultAccessPolicy](azure-stack-kv-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) comando:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value 

#Set the key vault access policy
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

* Use um "chaves/https://vault10.vault.local.azurestack.external:443/key01" Obtenha sempre a versão atual. 
* Use "https://vault010.vault.local.azurestack.external:443/chaves/key01/d0b36ee2e3d14e9f967b8b6b1d38938a" para obter a versão específica.

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

