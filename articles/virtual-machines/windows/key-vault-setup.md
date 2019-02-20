---
title: Configurar o Key Vault para máquinas virtuais Windows no Azure Resource Manager | Microsoft Docs
description: Como configurar um Cofre de Chaves para uso com uma máquina virtual do Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: bc83a2cde841e7d1e90cb46304c879fcc6cedb72
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105766"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurar o Cofre de Chaves para máquinas virtuais no Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Na pilha do Azure Resource Manager, os certificados/segredos são modelados como recursos que são fornecidos pelo provedor de recursos do Cofre de Chaves. Para saber mais sobre o Cofre de Chaves, consulte [O que é o Cofre de Chaves do Azure?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. Para que um Cofre de Chaves seja usado com máquinas virtuais do Azure Resource Manager, a propriedade **EnabledForDeployment** no Cofre de Chaves deverá ser definida como true. Você pode fazer isso em vários clientes.
> 2. O Cofre de Chaves precisa ser criado na mesma assinatura e na mesma localização que a Máquina Virtual.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Usar o PowerShell para configurar o Cofre de Chaves
Para criar um key vault usando o PowerShell, consulte [Definir e recuperar um segredo do Azure Key Vault usando o PowerShell](../../key-vault/quick-create-powershell.md).

Para novos cofres de chaves, você pode usar este cmdlet do PowerShell:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Para cofres de chaves existentes, você pode usar este cmdlet do PowerShell:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Usar a CLI para configurar o Cofre de Chaves
Para criar um cofre de chaves usando a CLI (interface de linha de comando), consulte [Gerenciar Cofre de Chaves usando a CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Para a CLI, você precisa criar o cofre de chaves antes de atribuir a política de implantação. Faça isso usando este comando:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Usar modelos para configurar o Cofre de Chaves
Ao usar um modelo, você precisa definir a propriedade `enabledForDeployment` como `true` para o recurso de Cofre de Chaves.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Para saber outras opções que você pode configurar ao criar um cofre de chaves usando modelos, consulte [Criar um cofre de chave](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
