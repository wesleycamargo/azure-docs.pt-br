---
title: Configurar o Cofre de Chaves para máquinas virtuais no Azure Resource Manager | Microsoft Docs
description: Como configurar um Cofre de Chaves para uso com uma máquina virtual do Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: singhkay

---
# Configurar o Cofre de Chaves para máquinas virtuais no Azure Resource Manager
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

modelo de implantação clássico

Na pilha do Azure Resource Manager, os certificados/segredos são modelados como recursos que são fornecidos pelo provedor de recursos do Cofre de Chaves. Para saber mais sobre o Cofre de Chaves, consulte [O que é o Cofre de Chaves do Azure?](../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. Para que um Cofre de Chaves seja usado com máquinas virtuais do Azure Resource Manager, a propriedade **EnabledForDeployment** no Cofre de Chaves deverá ser definida como true. Você pode fazer isso em vários clientes.
> 2. O Cofre de Chaves precisa ser criado na mesma assinatura e na mesma localização que a Máquina Virtual.
> 
> 

## Usar o PowerShell para configurar o Cofre de Chaves
Para criar um Cofre de Chaves usando o PowerShell, consulte [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md#vault).

Para novos cofres de chaves, você pode usar este cmdlet do PowerShell:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Para cofres de chaves existentes, você pode usar este cmdlet do PowerShell:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## Usar a CLI para configurar o Cofre de Chaves
Para criar um cofre de chaves usando a CLI (interface de linha de comando), consulte [Gerenciar Cofre da Chave usando a CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Para a CLI, você precisa criar o cofre de chaves antes de atribuir a política de implantação. Faça isso usando este comando:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## Usar modelos para configurar o Cofre de Chaves
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

<!---HONumber=AcomDC_0824_2016-->