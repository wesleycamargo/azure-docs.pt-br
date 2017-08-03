---
title: "Repositórios de Registro de contêiner do Azure | Microsoft Docs"
description: "Como usar os repositórios de Registro de contêiner do Azure para imagens do Docker"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: cristyg
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 2fb060749c203a445196530bb7711d50d83c2923
ms.contentlocale: pt-br
ms.lasthandoff: 06/09/2017


---

# <a name="create-a-private-docker-container-registry-using-the-azure-powershell"></a>Criar um registro de contêiner privado do Docker usando o Azure PowerShell
Use comandos no [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview) para criar um registro de contêiner e gerenciar suas configurações de seu computador com Windows. Você também pode criar e gerenciar registros de contêiner usando o [portal do Azure](container-registry-get-started-portal.md), a [CLI do Azure](container-registry-get-started-azure-cli.md) ou por meio de programação com a [API REST](https://go.microsoft.com/fwlink/p/?linkid=834376) do Registro de Contêiner.


* Para obter mais informações e conceitos, confira a [visão geral](container-registry-intro.md)
* Para obter uma lista completa dos cmdlets com suporte, consulte [Cmdlets de gerenciamento do Registro de Contêiner do Azure](https://docs.microsoft.com/en-us/powershell/module/azurerm.containerregistry/).


## <a name="prerequisites"></a>Pré-requisitos
* **Azure PowerShell**: para instalar e começar a usar o Azure PowerShell, consulte as [instruções de instalação](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps). Faça logon em sua assinatura do Azure executando `Login-AzureRMAccount`. Para obter mais informações, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azurep).
* **Grupo de recursos**: crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) antes de criar um registro de contêiner ou usar um grupo de recursos existente. Verifique se o grupo de recursos está em um local em que o serviço de Registro de Contêiner está [disponível](https://azure.microsoft.com/regions/services/). Para criar um grupo de recursos usando o Azure PowerShell, consulte [Referência do PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps#create-a-resource-group).
* **Conta de armazenamento** (opcional): cria uma [conta de armazenamento](../storage/storage-introduction.md) padrão do Azure para dar suporte ao registro de contêiner no mesmo local. Se você não especificar uma conta de armazenamento ao criar um registro com `New-AzureRMContainerRegistry`, o comando criará um para você. Para criar uma conta de armazenamento usando o PowerShell, consulte [Referência do PowerShell](https://docs.microsoft.com/en-us/powershell/module/azure/new-azurestorageaccount). Atualmente, não há suporte para o Armazenamento Premium.
* **Entidade de serviço** (opcional): quando você cria um registro com o PowerShell, por padrão, ele não é configurado para acesso. Dependendo das suas necessidades, você pode atribuir uma entidade de serviço do Azure Active Directory existente a um registro ou criar e atribuir uma nova. Como alternativa, você pode habilitar a conta de usuário administrador do registro. Confira as seções mais adiante neste artigo. Para obter mais informações sobre o acesso ao registro, confira [Autenticar no registro de contêiner](container-registry-authentication.md).

## <a name="create-a-container-registry"></a>Criar um registro de contêiner
Execute o comando `New-AzureRMContainerRegistry` para criar um registro de contêiner.

> [!TIP]
> Ao criar um registro, especifique um nome de domínio de nível superior exclusivo que contenha apenas letras e números. O nome do registro nos exemplos é `MyRegistry`, mas substitua-o por um nome exclusivo.
>
>

O seguinte comando usa os parâmetros mínimos para criar o registro de contêiner `MyRegistry` no grupo de recursos `MyResourceGroup` no local Centro-Sul dos EUA:

```PowerShell
$Registry = New-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

* `-StorageAccountName` é opcional. Se não for especificada, uma conta de armazenamento será criada com um nome que consiste no nome do registro e em um carimbo de data e hora no grupo de recursos especificado.

## <a name="assign-a-service-principal"></a>Atribuir uma entidade de serviço
Use comandos do PowerShell para atribuir uma [entidade de serviço](../azure-resource-manager/resource-group-authenticate-service-principal.md) do Azure Active Directory a um registro. A entidade de serviço nesses exemplos é atribuída à função de Proprietário, mas você poderá atribuir [outras funções](../active-directory/role-based-access-control-configure.md), se desejar.

### <a name="create-a-service-principal"></a>Criar uma entidade de serviço
No comando a seguir, uma nova entidade de serviço é criada. Especifique uma senha forte com o parâmetro `-Password`.

```PowerShell
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName ApplicationDisplayName -Password "MyPassword"
```

### <a name="assign-a-new-or-existing-service-principal"></a>Atribuir uma entidade de serviço nova ou existente
Você pode atribuir uma entidade de serviço nova ou existente a um registro. Para atribuir acesso de função de Proprietário ao Registro, execute um comando semelhante ao exemplo a seguir:

```PowerShell
New-AzureRMRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Registry.Id
```

##<a name="sign-in-to-the-registry-with-the-service-principal"></a>Entrar no registro com a entidade de serviço
Depois de atribuir a entidade de serviço ao registro, você pode entrar usando o seguinte comando:

```PowerShell
docker login -u $ServicePrincipal.ApplicationId -p myPassword
```

## <a name="manage-admin-credentials"></a>Gerenciar credenciais de administrador
Uma conta de administrador é automaticamente criada para cada registro de contêiner e é desabilitada por padrão. Os exemplos a seguir mostram comandos do PowerShell para gerenciar as credenciais de administrador para seu registro de contêiner.

### <a name="obtain-admin-user-credentials"></a>Obter credenciais de usuário administrador
```PowerShell
Get-AzureRMContainerRegistryCredential -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Habilitar o usuário administrador para um registro existente
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -EnableAdminUser
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Desabilitar o usuário administrador para um registro existente
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -DisableAdminUser
```

## <a name="next-steps"></a>Próximas etapas
* [Enviar por push sua primeira imagem usando a CLI do Docker](container-registry-get-started-docker-cli.md)

