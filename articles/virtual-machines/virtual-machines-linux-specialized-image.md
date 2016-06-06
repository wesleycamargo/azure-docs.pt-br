<properties
	pageTitle="Criar uma cópia de sua VM do Linux | Microsoft Azure"
	description="Saiba como criar uma cópia de sua máquina virtual do Azure executando Linux, no modelo de implantação do Gerenciador de Recursos, criando uma * imagem especializada*."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="cynthn"/>

# Criar uma cópia de uma máquina virtual do Linux no modelo de implantação do Azure Resource Manager


Este artigo mostra como criar uma cópia da VM (máquina virtual) do Azure que executam o Linux. Especificamente, ele aborda como fazer isso no modelo de implantação do Resource Manager usando a CLI e o Portal do Azure. Ele mostra como criar uma imagem *especializada* da VM do Azure que mantém as contas de usuário e outros dados de estado da sua VM original. Uma imagem especializada é útil para portar a VM do Linux do modelo de implantação clássico para o modelo de implantação do Resource Manager ou criar uma cópia de backup da VM do Linux criada no modelo de implantação do Resource Manager. Você pode copiar o sistema operacional e os discos de dados dessa maneira e, em seguida, configurar os recursos de rede para criar a nova máquina virtual.

Se precisar criar implantações em massa de VMs do Linux semelhantes, você deverá usar uma imagem *generalizada*. Para tal, consulte [Como capturar uma máquina virtual do Linux](virtual-machines-linux-capture-image.md).



## Antes de começar

Verifique se você atende os seguintes pré-requisitos antes de iniciar as etapas:

- Você tem uma máquina virtual do Azure que executa o Linux, criada usando o modelo de implantação clássico ou o modelo de implantação do Resource Manager. Você configurou o sistema operacional, anexou os discos de dados e fez outras personalizações como instalar os aplicativos necessários. Você usará essa VM para criar a cópia. Se precisar de ajuda para criar essa VM, consulte [Criar uma VM do Linux no Azure](virtual-machines-linux-quick-create-cli.md).

- Você tem a CLI do Azure baixada e instalada em seu computador e entrou na sua assinatura do Azure. Para obter mais informações, consulte [Como instalar a CLI do Azure](../xplat-cli-install.md).

- Você tem um grupo de recursos e uma conta de armazenamento, bem como um contêiner de blob criado nesse grupo de recursos para o qual copiar os VHDs. Para obter mais informações sobre como criar contas de armazenamento e contêineres de blob, consulte [Usando a CLI do Azure com o Armazenamento do Azure](../storage/storage-azure-cli.md).

> [AZURE.NOTE] Etapas semelhantes se aplicam a uma VM criada usando qualquer um dos dois modelos de implantação como a imagem de origem. Quando aplicável, este artigo observa as pequenas diferenças.


## Copiar VHDs para sua conta de armazenamento do Gerenciador de Recursos


1. Libere os VHDs usados pela VM de origem realizando uma das opções a seguir:

	- Se quiser copiar sua máquina virtual de origem, pare e desaloque-a. No portal, clique em **Procurar** > **Máquinas virtuais** ou **Máquinas virtuais (clássicas)** > *sua VM* > **Parar**. Para VMs criadas no modelo de implantação do Resource Manager, você também pode usar o comando `azure vm stop <yourResourceGroup> <yourVmName>` da CLI do Azure seguido por `azure vm deallocate <yourResourceGroup> <yourVmName>`. Observe que o status da VM no portal muda de **Em execução** para **Parado (desalocado)**.

	- Se quiser migrar sua máquina virtual de origem, exclua a VM e use o VHD que restou. Procure sua máquina virtual no [portal](https://portal.azure.com) e clique em **Excluir**.

1. Encontre a chave de acesso da conta de armazenamento que contém o VHD de origem. Para obter mais informações sobre as chaves de acesso, consulte [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

	- Se sua VM de origem tiver sido criada usando o modelo de implantação clássico, clique em **Procurar** > **Contas de armazenamento (clássicas)** > *sua conta de armazenamento* > **Todas as configurações** > **Chaves**. Copie a chave rotulada como **CHAVE DE ACESSO PRIMÁRIA**. Ou então, na CLI do Azure, mude para o modo clássico usando `azure config mode asm` e, em seguida, use `azure storage account keys list <yourSourceStorageAccountName>`.

	- Se sua VM de origem tiver sido criada usando o modelo de implantação do Resource Manager, clique em **Procurar** > **Contas de armazenamento** > *sua conta de armazenamento* > **Todas as configurações** > **Chaves de acesso**. Copie o texto rotulado como **key1**. Ou, na CLI do Azure, verifique se você está no modo do Resource Manager usando `azure config mode arm` e use `azure storage account keys list -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>`.

1. Copie os arquivos do VHD usando os [Comandos da CLI do Azure para armazenamento](../storage/storage-azure-cli.md), conforme descrito nas etapas a seguir. Como alternativa, se preferir uma abordagem de interface do usuário, você poderá usar o [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/). </br>
	1. Configure a cadeia de conexão para a conta de armazenamento de destino. Essa cadeia de conexão conterá a chave de acesso desta conta de armazenamento.

			$azure storage account connectionstring show -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>
			$export AZURE_STORAGE_CONNECTION_STRING=<the_connectionstring_output_from_above_command>

	2. Crie uma [Assinatura de Acesso Compartilhado](../storage/storage-dotnet-shared-access-signature-part-1.md) para o arquivo VHD na conta de armazenamento de origem. Anote a saída da **URL de Acesso Compartilhado** do comando a seguir.

			$azure storage blob sas create  --account-name <yourSourceStorageAccountName> --account-key <SourceStorageAccessKey> --container <SourceStorageContainerName> --blob <FileNameOfTheVHDtoCopy> --permissions "r" --expiry <mm/dd/yyyy_when_you_want_theSAS_to_expire>

	3. Copie o VHD do armazenamento de origem para o destino usando o comando a seguir.

			$azure storage blob copy start <SharedAccessURL_ofTheSourceVHD> <DestinationContainerName>

	4. O arquivo VHD será copiado de maneira assíncrona. Verifique o progresso usando o comando a seguir.

			$azure storage blob copy show <DestinationContainerName> <FileNameOfTheVHDtoCopy>

</br>

>[AZURE.NOTE] Você deve copiar o sistema operacional e discos de dados separadamente, conforme descrito anteriormente.


## Criar uma VM usando o VHD copiado

Usando o VHD copiado nas etapas acima, agora você pode usar a CLI do Azure para criar uma VM do Linux baseada no Resource Manager em uma nova rede virtual. O VHD deve estar presente na mesma conta de armazenamento que a nova máquina virtual que será criada.


Configure uma rede virtual e uma NIC para sua nova VM, semelhante ao script a seguir. Use valores para as variáveis da forma que for apropriada para seu aplicativo.

	$azure network vnet create <yourResourceGroup> <yourVnetName> -l <yourLocation>

	$azure network vnet subnet create <yourResourceGroup> <yourVnetName> <yourSubnetName>

	$azure network public-ip create <yourResourceGroup> <yourIpName> -l <yourLocation>

	$azure network nic create <yourResourceGroup> <yourNicName> -k <yourSubnetName> -m <yourVnetName> -p <yourIpName> -l <yourLocation>


Crie a nova máquina virtual usando os VHDs copiados com o comando a seguir. </br>

	$azure vm create -g <yourResourceGroup> -n <yourVmName> -f <yourNicName> -d <UriOfYourOsDisk> -x <UriOfYourDataDisk> -e <DataDiskSizeGB> -Y -l <yourLocation> -y Linux -z "Standard_A1" -o <DestinationStorageAccountName> -R <DestinationStorageAccountBlobContainer>


As URLs do disco do sistema operacional e dos dados se parecem com esta: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Você pode encontrá-las no portal navegando até o contêiner de armazenamento, clicando no VHD de sistema operacional ou nos dados que foram copiados e copiando o conteúdo da URL.


Se o comando for bem-sucedido, você verá uma saída semelhante a esta:

	$azure vm create -g "testcopyRG" -n "redhatcopy" -f "LinCopyNic" -d https://testcopystore.blob.core.windows.net/testcopyblob/RedHat201631816334.vhd -x https://testcopystore.blob.core.windows.net/testcopyblob/RedHat-data.vhd -e 10 -Y -l "West US" -y Linux -z "Standard_A1" -o "testcopystore" -R "testcopyblob"
	info:    Executing command vm create
	+ Looking up the VM "redhatcopy"
	info:    Using the VM Size "Standard_A1"
	+ Looking up the NIC "LinCopyNic"
	info:    Found an existing NIC "LinCopyNic"
	info:    Found an IP configuration with virtual network subnet id "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourceGroups/testcopyRG/providers/Microsoft.Network/virtualNetworks/LinCopyVnet/subnets/LinCopySub" in the NIC "LinCopyNic"
	info:    This NIC IP configuration has a public ip already configured "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourcegroups/testcopyrg/providers/microsoft.network/publicipaddresses/lincopyip", any public ip parameters if provided, will be ignored.
	+ Looking up the storage account testcopystore
	info:    The storage URI 'https://testcopystore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
	+ Creating VM "redhatcopy"
	info:    vm create command OK

Você deverá ver a VM recém-criada no [Portal do Azure](https://portal.azure.com), em **Procurar** > **Máquinas virtuais**.

Conecte-se à sua nova máquina virtual usando um cliente SSH de sua escolha e use as credenciais da conta da máquina virtual original (por exemplo, `ssh OldAdminUser@<IPaddressOfYourNewVM>`). Para saber mais, confira [Como usar o SSH com o Linux no Azure](virtual-machines-linux-ssh-from-linux.md).


## Próximas etapas

Para saber como usar a CLI do Azure para gerenciar sua nova máquina virtual, consulte [Comandos da CLI do Azure para o Azure Resource Manager](azure-cli-arm-commands.md).

<!---HONumber=AcomDC_0525_2016-->