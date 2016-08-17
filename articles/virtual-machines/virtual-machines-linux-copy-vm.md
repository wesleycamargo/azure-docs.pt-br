<properties
	pageTitle="Criar uma cópia de sua VM Linux do Azure | Microsoft Azure"
	description="Saiba como criar uma cópia de uma máquina virtual Linux do Azure no modelo de implantação do Resource Manager"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="cynthn"/>

# Criar uma cópia de uma máquina virtual Linux em execução no Azure


Este artigo mostra como criar uma cópia de sua VM (máquina virtual) do Azure executando o Linux no modelo de implantação do Resource Manager. Primeiro, você copia o sistema operacional e os discos de dados para um novo contêiner e, em seguida, configura os recursos de rede para criar a nova máquina virtual.

Você também pode [carregar e criar uma VM com base em uma imagem de disco personalizada](virtual-machines-linux-upload-vhd.md).


## Antes de começar

Verifique se você atende os seguintes pré-requisitos antes de iniciar as etapas:

- Você tem a [CLI do Azure](../xplat-cli-install.md) baixada e instalada em seu computador.

- Você também precisa de algumas informações sobre sua VM Linux do Azure existente:

| Informações sobre a VM de origem | Onde obter |
|------------|-----------------|
| Nome da VM | `azure vm list` |
| Nome do Grupo de Recursos | `azure vm list` |
| Local | `azure vm list` |
| Nome da Conta de Armazenamento | `azure storage account list -g <resourceGroup>` |
| Nome do contêiner | `azure storage container list -a <sourcestorageaccountname>` |
| Nome do arquivo VHD da VM de origem | `azure storage blob list --container <containerName>` |



- Você precisará fazer algumas escolhas quanto a sua nova VM: <br> -Nome do contêiner <br> -Nome da VM <br> -Tamanho da VM <br> -Nome da rede virtual <br> -Nome da sub-rede <br> -Nome do IP <br> -Nome da NIC
	

## Faça logon e configure sua assinatura

1. Faça logon na CLI.
		
		azure login

2. Certifique-se de estar no modo Resource Manager.
	
		azure config mode arm

3. Configure a assinatura correta. Você pode usar “azure account list” para ver todas as suas assinaturas.

		azure account set <SubscriptionId>



## Pare a VM. 

Pare e desaloque a VM de origem. Você pode usar “azure vm list” para obter uma lista de todas as VMs em sua assinatura e os nomes de seus grupos de recursos.
	
		azure vm stop <ResourceGroup> <VmName>
		azure vm deallocate <ResourceGroup> <VmName>




## Copie o VHD


Você pode copiar o VHD do armazenamento de origem para o destino usando o `azure storage blob copy start`. Neste exemplo, vamos copiar o VHD para a mesma conta de armazenamento, mas para um contêiner diferente.

Para copiar o VHD para outro contêiner na mesma conta de armazenamento, digite:

		azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>
		

## Configure a rede virtual para sua nova VM

Configure uma rede virtual e NIC para sua nova VM.

	azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

	azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

	azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

	azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## Crie a nova VM 

Agora, você pode criar uma VM com base no disco virtual carregado [usando um modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) ou por meio da CLI, especificando o URI para seu disco copiado digitando:

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## Próximas etapas

Para saber como usar a CLI do Azure para gerenciar sua nova máquina virtual, consulte [Comandos da CLI do Azure para o Azure Resource Manager](azure-cli-arm-commands.md).

<!---HONumber=AcomDC_0803_2016-->