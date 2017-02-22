---
title: "Configurar o Key Vault para máquinas virtuais Linux no Azure Resource Manager | Microsoft Docs"
description: "Como configurar um Cofre de Chaves para uso com uma máquina virtual do Azure Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 7ccb810fa3178528eb6f41ac7eae842d017d8bbc
ms.openlocfilehash: 0f24791cf6fc8668b83cfc2eb479f5f13362e217


---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurar o Cofre de Chaves para máquinas virtuais no Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Na pilha do Azure Resource Manager, os certificados/segredos são modelados como recursos que são fornecidos pelo provedor de recursos do Cofre de Chaves. Para saber mais sobre o Cofre de Chaves do Azure, consulte [O que é o Cofre de Chaves do Azure?](../key-vault/key-vault-whatis.md)

Para que um Cofre de Chaves seja usado com máquinas virtuais do Azure Resource Manager, a propriedade *EnabledForDeployment* no Cofre de Chaves deverá ser definida como true. Você pode fazer isso em vários clientes.”

## <a name="use-cli-to-set-up-key-vault"></a>Usar a CLI para configurar o Cofre de Chaves
Para criar um cofre de chaves usando a CLI (interface de linha de comando), consulte [Gerenciar Cofre de Chaves usando a CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

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




<!--HONumber=Jan17_HO4-->


