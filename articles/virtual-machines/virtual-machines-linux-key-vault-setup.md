<properties
	pageTitle="Configurar o Cofre de Chaves para máquinas virtuais no Azure Resource Manager | Microsoft Azure"
	description="Como configurar um Cofre de Chaves para uso com uma máquina virtual do Azure Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="singhkays"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="singhkay"/>

# Configurar o Cofre de Chaves para máquinas virtuais no Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implantação clássico

Na pilha do Azure Resource Manager, os certificados/segredos são modelados como recursos que são fornecidos pelo provedor de recursos do Cofre de Chaves. Para saber mais sobre o Cofre de Chaves do Azure, consulte [O que é o Cofre de Chaves do Azure?](../key-vault/key-vault-whatis.md)

Para que um Cofre de Chaves seja usado com máquinas virtuais do Azure Resource Manager, a propriedade *EnabledForDeployment* no Cofre de Chaves deverá ser definida como true. Você pode fazer isso em vários clientes.”

## Usar a CLI para configurar o Cofre de Chaves
Para criar um cofre de chaves usando a CLI (interface de linha de comando), consulte [Gerenciar Cofre de Chaves usando a CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

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

Para encontrar outras opções que você pode configurar ao criar um cofre de chaves usando modelos, consulte [Criar um cofre de chaves](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).

<!---HONumber=AcomDC_0817_2016-->