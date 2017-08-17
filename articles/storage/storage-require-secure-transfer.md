---
title: "Requer transferência segura no Armazenamento do Microsoft Azure | Microsoft Docs"
description: "Saiba mais sobre o recurso \"Requer transferência segura\" para o Armazenamento do Microsoft Azure e como habilitá-lo."
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: 78737c681a91f24f73502a9cc25a301efc9304a4
ms.contentlocale: pt-br
ms.lasthandoff: 08/07/2017

---
# <a name="require-secure-transfer"></a>Requer transferência segura

A opção "Transferência segura obrigatória" melhora a segurança da sua conta de armazenamento, permitindo apenas solicitações para a conta de armazenamento de conexões seguras. Por exemplo, ao chamar APIs REST para acessar sua conta de armazenamento, será necessário estar conectado utilizando o HTTPS. Todas as solicitações utilizando HTTP serão rejeitadas quando a "Transferência segura obrigatória" estiver habilitada.

Quando você estiver usando o serviço de Arquivos do Azure, qualquer conexão sem criptografia falhará quando "Transferência segura obrigatória" estiver habilitada. Isso inclui cenários usando SMB 2.1, SMB 3.0 sem criptografia e alguns tipos do cliente Linux SMB. 

Por padrão, a opção "Transferência segura obrigatória" está desabilitada.

> [!NOTE]
> Como o armazenamento do Azure não dá suporte a HTTPS para nomes de domínio personalizado, essa opção não será aplicada ao usar um nome de domínio personalizado.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Habilitar "Transferência segura obrigatória" no portal do Azure

Você pode habilitar a "Transferência segura obrigatória" configurando ambos ao criar uma conta de armazenamento no [Portal do Azure](https://portal.azure.com)e para contas de armazenamento existentes.

### <a name="require-secure-transfer-when-you-create-a-storage-account"></a>Requerer transferência segura ao criar uma conta de armazenamento

1. Abra a folha **Criar conta de armazenamento** no portal do Azure.
1. Em **Transferência segura obrigatória**, selecione **habilitado**.

  ![captura de tela](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Requer transferência segura de uma conta de armazenamento existente

1. Selecionar uma conta de armazenamento existente no portal do Azure.
1. Selecione **Configuração** em **CONFIGURAÇÕES** na folha do menu da conta de armazenamento.
1. Em **Transferência segura obrigatória**, selecione **habilitado**.

  ![captura de tela](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Habilitar "Transferência segura obrigatória" Programaticamente

O nome da configuração é _supportsHttpsTrafficOnly_ nas propriedades da conta de armazenamento. Você pode habilitar a configuração "Transferência segura obrigatória" com a API REST, ferramentas ou bibliotecas:

* **API REST** (Versão: 2016-12-01): [pacote de lançamento](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts)
* **PowerShell** (Versão: 4.1.0): [pacote de lançamento](https://docs.microsoft.com/en-us/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0)
* **CLI** (Versão: 2.0.11): [pacote de lançamento](https://pypi.python.org/pypi/azure-cli-storage/2.0.11)
* **NodeJS** (Versão: 1.1.0): [pacote de lançamento](https://www.npmjs.com/package/azure-arm-storage/)
* **SDK do .NET SDK** (Versão: 6.3.0): [pacote de lançamento](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview)
* **SDK do Python** (Versão: 1.1.0): [pacote de lançamento](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0)
* **SDK do Ruby** (Versão: 0.11.0): [pacote de lançamento](https://rubygems.org/gems/azure_mgmt_storage)

### <a name="enable-secure-transfer-required-setting-with-rest-api"></a>Habilitar a configuração "Transferência segura obrigatória" com a API REST

Para simplificar o teste com a API REST, você pode usar [ArmClient](https://github.com/projectkudu/ARMClient) para chamar a partir de linha de comando.

 Você pode usar a linha de comando abaixo para verificar a configuração com a API REST:

```
# Login Azure and proceed with your credentials
> armclient login

> armclient GET  /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01
```

Em resposta, você pode encontrar a configuração _supportsHttpsTrafficOnly_. Exemplo:

```Json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}",
  "kind": "Storage",
  ...
  "properties": {
    ...
    "supportsHttpsTrafficOnly": false
  },
  "type": "Microsoft.Storage/storageAccounts"
}
```

Você pode usar a linha de comando abaixo para habilitar a configuração com a API REST:

```
# Login Azure and proceed with your credentials
> armclient login

> armclient PUT /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01 < Input.json
```
Exemplo de Input.json:
```Json
{
  "location": "westus",
  "properties": {
    "supportsHttpsTrafficOnly": true
  }
}
```

## <a name="next-steps"></a>Próximas etapas
O Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que, juntos, permitem aos desenvolvedores criar aplicativos seguros. Para obter mais detalhes, visite o [Guia de segurança do armazenamento](storage-security-guide.md).

