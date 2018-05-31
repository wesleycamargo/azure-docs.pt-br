---
title: Use a MSI atribuída pelo usuário de VM do Windows para acessar o Azure Resource Manager
description: Um tutorial que orienta o processo de uso de uma MSI (Identidade de Serviço Gerenciado pelo Usuário) em uma VM do Windows, para acessar o Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: a2225409e4cb50d91c09207ee70b76df12925192
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301204"
---
# <a name="tutorial-use-a-user-assigned-managed-service-identity-msi-on-a-windows-vm-to-access-azure-resource-manager"></a>Tutorial: Usar uma MSI (Identidade de Serviço Gerenciada) atribuída pelo usuário em uma VM do Windows para acessar o Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Este tutorial explica como criar a MSI (Identidade do Serviço Gerenciado), atribuí-la a uma VM (Máquina Virtual) do Windows e, em seguida, usar essa identidade para acessar a API do Azure Resource Manager. Identidade do Serviço Gerenciado são gerenciadas automaticamente pelo Azure. Elas permitem que a autenticação para serviços compatíveis com a autenticação do Azure AD, sem a necessidade de inserir as credenciais em seu código. 

Você aprenderá como:

> [!div class="checklist"]
> * Criar uma VM do Windows 
> * Criar uma identidade atribuída pelo usuário
> * Atribuir sua identidade atribuída pelo usuário à sua VM do Windows
> * Conceder o acesso atribuído pelo usuário a um grupo de recursos no Azure Resource Manager 
> * Obtenha um token de acesso usando a identidade atribuída pelo usuário e use-a para chamar o Azure Resource Manager 
> * Leia as propriedades de um grupo de recursos

## <a name="prerequisites"></a>pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a seção de [visão geral](overview.md). **Lembre-se de analisar as [diferenças entre as identidades atribuídas pelo sistema e pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar as etapas necessárias de criação de recursos e gerenciamento de funções neste tutorial, sua conta precisa das permissões de "Proprietário" no escopo apropriado (sua assinatura ou grupo de recursos). Caso você precise de ajuda com a atribuição de função, consulte [Usar o controle de acesso baseado em função para gerenciar o acesso aos recursos de assinatura do Azure](/azure/role-based-access-control/role-assignments-portal).

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.7 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="create-resource-group"></a>Criar grupo de recursos

No exemplo a seguir, um grupo de recursos chamado *myResourceGroupVM* é criado na região *EastUS*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

## <a name="create-virtual-machine"></a>Criar máquina virtual

Depois que o grupo de recursos é criado, crie uma VM do Windows.

Defina o nome de usuário e a senha necessários para a conta de administrador na máquina virtual com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```
Crie a máquina virtual com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="create-a-user-assigned-identity"></a>Criar uma identidade atribuída pelo usuário

Uma identidade atribuída pelo usuário é criada como um recurso autônomo do Azure. Usando o [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity), o Azure cria uma identidade em seu locatário do Azure AD que pode ser atribuído a uma ou mais instâncias de serviço do Azure.

> [!IMPORTANT]
> A criação de identidades atribuídas pelo usuário oferece suporte somente a caracteres alfanuméricos e hífen (0-9 ou a-z ou A-Z ou -). Além disso, o nome deve ter um limite de 24 caracteres para que a atribuição a VM/VMSS funcione corretamente. Procure novamente por atualizações. Para mais informações, consulte [Perguntas frequentes e problemas conhecidos](known-issues.md)

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

A resposta contém detalhes para a identidade atribuída pelo usuário criada, semelhante ao exemplo a seguir. Tome nota do valor `Id` para sua identidade atribuída pelo usuário, pois ela será usada na próxima etapa:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Atribuir a identidade atribuída pelo usuário a uma VM do Windows

Uma identidade atribuída pelo usuário pode ser usada por clientes em vários recursos do Azure. Use os comandos a seguir para atribuir a identidade atribuída pelo usuário a uma única VM. Use a propriedade `Id` retornada na etapa anterior para o valor do parâmetro `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Conceder o acesso à MSI atribuída pelo usuário a um grupo de recursos no Azure Resource Manager 

A MSI (Managed Service Identity, identidade de serviço gerenciado) fornece identidades que seu código pode usar para solicitar tokens de acesso para autenticação em APIs de recursos que dão suporte à autenticação do AD do Azure. Neste tutorial, seu código acessará a API do Azure Resource Manager. 

Antes que seu código possa acessar a API, você precisa conceder o acesso de identidade a um recurso no Azure Resource Manager. Nesse caso, o grupo de recursos no qual a VM está contida. Atualize os valores de `<SUBSCRIPTION ID>` conforme apropriado para seu ambiente.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

A resposta contém detalhes para a atribuição de função criada, semelhante ao exemplo a seguir:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Obter um token de acesso utilizando a identidade da VM e usá-la para chamar o Resource Manager 

Para o restante do tutorial, você trabalhará a partir da VM que criamos anteriormente.

1. Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

2. No portal, navegue até **Máquinas Virtuais** e vá para a máquina virtual do Windows e em **Visão geral**, clique em **Conectar**.

3. Insira o seu **Nome de usuário** e **Senha** que você adicionou quando criou a VM do Windows.

4. Agora que você criou uma **Conexão de Área de Trabalho Remota** com a máquina virtual, abra o **PowerShell** na sessão remota.

5. Usando o `Invoke-WebRequest` do PowerShell, faça uma solicitação ao terminal do MSI local para obter um token de acesso para o Azure Resource Manager.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=73444643-8088-4d70-9532-c3a0fdc190fz&resource=https://management.azure.com' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Leia as propriedades de um grupo de recursos

Use o token de acesso recuperado na etapa anterior para acessar o Azure Resource Manager e leia as propriedades do Grupo de Recursos que você concedeu ao seu acesso de identidade atribuído pelo usuário. Substituir <SUBSCRIPTION ID> com a id da assinatura do seu ambiente.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
A resposta contém as informações de grupo de recursos específicas, semelhantes ao exemplo a seguir:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/TestRG","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](overview.md).
