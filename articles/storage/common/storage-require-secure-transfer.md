---
title: Requer transferência segura no Armazenamento do Microsoft Azure | Microsoft Docs
description: Saiba mais sobre o recurso "Transferência segura obrigatória" para o Armazenamento do Microsoft Azure e como habilitá-lo.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 06/20/2017
ms.author: fryu
ms.component: common
ms.openlocfilehash: 6f17cb4540feea6400c60c8032d071d6a922c871
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633054"
---
# <a name="require-secure-transfer-in-azure-storage"></a>Requer transferência segura no Armazenamento do Microsoft Azure

A opção "Transferência segura obrigatória" melhora a segurança da sua conta de armazenamento, permitindo apenas solicitações para a conta de conexões seguras. Por exemplo, ao chamar APIs REST para acessar sua conta de armazenamento, será necessário estar conectado utilizando o HTTPS. "Transferência segura obrigatória" rejeita solicitações que usam HTTP.

Quando você usar o serviço de Arquivos do Azure, qualquer conexão sem criptografia falhará quando "Transferência segura obrigatória" estiver habilitada. Isso inclui cenários que usam SMB 2.1, SMB 3.0 sem criptografia e algumas versões do cliente Linux SMB. 

Por padrão, a opção "Transferência segura obrigatória" está desabilitada quando você cria uma conta de armazenamento com o SDK. E ele é habilitado por padrão quando você cria uma conta de armazenamento no portal do Azure.

> [!NOTE]
> Como o armazenamento do Azure não dá suporte a HTTPS para nomes de domínio personalizado, essa opção não será aplicada ao usar um nome de domínio personalizado. Não há suporte para contas de armazenamento clássicas.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Habilitar "Transferência segura obrigatória" no portal do Azure

Você pode habilitar a "Transferência segura obrigatória" ao criar uma conta de armazenamento no [Portal do Azure](https://portal.azure.com). Você também pode habilitá-la para uma conta de armazenamento existente.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Requer transferência segura de uma conta de armazenamento nova

1. Abra o painel **Criar conta de armazenamento** no portal do Azure.
1. Em **Transferência segura obrigatória**, selecione **habilitado**.

  ![Criar uma folha da conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Requer transferência segura de uma conta de armazenamento existente

1. Selecionar uma conta de armazenamento existente no portal do Azure.
1. No painel do menu de conta de armazenamento, em **DEFINIÇÕES**, selecione **Configuração**.
1. Em **Transferência segura obrigatória**, selecione **habilitado**.

  ![Painel do menu da conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Habilitar "Transferência segura obrigatória" Programaticamente

Para exigir a transferência segura programaticamente, use a configuração _supportsHttpsTrafficOnly_ nas propriedades da conta de armazenamento com a API REST, ferramentas ou bibliotecas:

* [API REST](https://docs.microsoft.com/rest/api/storagerp/storageaccounts) (versão: 2016-12-01)
* [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) (versão: 0.7)
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (versão: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (versão: 1.1.0)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (versão: 6.3.0)
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (versão: 1.1.0)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) (versão: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>Habilitar a configuração "Transferência segura obrigatória" com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O exemplo requer o módulo Az do Azure PowerShell, versão 0.7 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

Execute `Connect-AzAccount` para criar uma conexão com o Azure.

 Use a linha de comando a seguir para verificar a configuração:

```powershell
> Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Use a linha de comando a seguir para habilitar a configuração:

```powershell
> Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>Habilitar a configuração "Transferência segura obrigatória" com a CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Use a linha de comando a seguir para verificar a configuração:

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Use a linha de comando a seguir para habilitar a configuração:

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Próximas etapas
O Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que, juntos, permitem aos desenvolvedores criar aplicativos seguros. Para obter mais detalhes, acesse o [Guia de segurança do armazenamento](storage-security-guide.md).
