<properties
	pageTitle="Configurando o Cofre de Chaves para as máquinas virtuais no Azure Resource Manager | Microsoft Azure"
	description="Como configurar um cofre de chaves para uso com uma máquina virtual do Azure Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="singhkay"/>

# Configurando o Cofre de Chaves para as máquinas virtuais no Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico

Na pilha do Azure Resource Manager, certificados/segredos são modelados como recursos que são fornecidos pelo provedor de recursos do cofre de chaves. Para saber mais sobre o cofre de chaves, consulte [O que é o Cofre de Chaves do Azure?](../key-vault/key-vault-whatis.md)

## Configuração
Para que um cofre de chaves seja usado com máquinas virtuais do Azure Resource Manager, a propriedade *EnabledForDeployment* no cofre de chaves deve ser definida como true. Você pode fazer isso em vários clientes, conforme mostrado abaixo.”

## PowerShell
Para criar um cofre de chaves usando o PowerShell, consulte [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md#vault)

Para novos cofres de chaves, você pode usar este cmdlet do PowerShell

	New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Para cofres de chaves existentes, você pode usar este cmdlet do PowerShell

	Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## CLI
Para criar o Cofre de Chaves usando a CLI, consulte [Gerenciar cofre de chaves usando a CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault)

Para a CLI, você precisa primeiro criar o cofre de chaves, depois habilitar a política de implantação. Você pode fazer isso usando o comando a seguir

	azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## Modelos
Ao usar os modelos, você precisa definir a propriedade `enabledForDeployment` como `true` para o recurso de cofre de chaves.

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

Para outras opções que você pode configurar durante a criação de um cofre de chaves por meio de modelos, consulte [aqui](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)

<!---HONumber=AcomDC_0608_2016-->