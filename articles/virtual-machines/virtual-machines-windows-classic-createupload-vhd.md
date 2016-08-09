<properties
	pageTitle="Criar e carregar uma imagem do Windows Server usando o PowerShell | Microsoft Azure"
	description="Saiba como criar e carregar uma imagem do Windows Server generalizada usando o modelo de implantação clássico e o Azure PowerShell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="cynthn"/>

# Criar e carregar um VHD do Windows Server no Azure

Esse artigo mostra como carregar sua própria imagem de VM generalizada como um VHD (disco rígido virtual) para que o use para criar outras máquinas virtuais. Para mais detalhes sobre discos e os VHDs no Microsoft Azure, confira a seção [Sobre discos e VHDs para Máquinas Virtuais](virtual-machines-linux-about-disks-vhds.md).


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. Você também pode [capturar](virtual-machines-windows-capture-image.md) e [carregar](virtual-machines-windows-upload-image.md) uma máquina virtual usando o modelo do Resource Manager.

## Pré-requisitos

Este artigo supõe que você tem:

1. **Uma assinatura do Azure** - se você não tiver uma, você poderá [abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F).

2. **[Microsoft Azure PowerShell](../powershell-install-configure.md)** - você tem o módulo do Microsoft Azure PowerShell instalado e configurado para usar sua assinatura.

3. **Um arquivo .VHD** - sistema operacional Windows para o qual há suporte, armazenado em um arquivo .vhd e anexado a uma máquina virtual.

> [AZURE.IMPORTANT] Não há suporte para o formato VHDX no Microsoft Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o [cmdlet convert-vhd](http://technet.microsoft.com/library/hh848454.aspx). Consulte esta [publicação de blog](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx) para obter detalhes.

## Etapa 1: preparar o VHD 

Antes de carregar o VHD no Azure, ele precisa ser generalizado usando a ferramenta Sysprep. Ela prepara o VHD a ser usado como uma imagem. Para obter detalhes sobre o Sysprep, consulte [Como usar o Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

Na máquina virtual na qual o sistema operacional foi instalado, conclua o procedimento a seguir:

1. Entre no sistema operacional.

2. Abra uma janela de prompt de comando como administrador. Altere o diretório para **%windir%\\system32\\sysprep** e, a seguir, execute `sysprep.exe`.

	![Abrir una janela de Prompt de comando](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.	A caixa de diálogo **Ferramenta de Preparação do Sistema** é aberta.

	![Inicie o Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  Em **Ferramenta de Preparação do Sistema**, selecione **Entrar no Sistema OOBE** e verifique se a opção **Generalizar** está marcada.

5.  Em **Opções de Desligamento**, selecione **Desligar**.

6.  Clique em **OK**.

## Etapa 2: criar uma conta de armazenamento e um contêiner

Você precisa de uma conta de armazenamento no Azure para que você tenha um local para carregar o arquivo .vhd. Esta etapa mostra como criar uma conta, ou obter as informações de que você precisa de uma conta existente. Substitua as variáveis entre &lsaquo; colchetes &rsaquo; por suas próprias informações.

1. Logon

		Add-AzureAccount

1. Defina sua assinatura do Azure.

    	Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. Criar uma nova conta de armazenamento. O nome da conta de armazenamento deve ser exclusivo, contendo 3 a 24 caracteres. O nome pode ser qualquer combinação de letras e números. Você também precisará especificar uma localização, como "Leste dos EUA"
    	
		New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. Defina a nova conta de armazenamento como a padrão.
    	
		Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. Criar um novo contêiner.

    	New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## Etapa 3: Carregar o arquivo .vhd

Você usará o cmdlet [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) para carregar o VHD.

Na janela do Azure PowerShell usada na etapa anterior, digite o comando a seguir e substitua as variáveis entre &lsaquo; colchetes &rsaquo; por suas próprias informações.

		Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## Etapa 4: adicionar a imagem à sua lista de imagens personalizadas

Use o cmdlet [Add-AzureVMImage])(https://msdn.microsoft.com/library/mt589167.aspx) para adicionar a imagem à lista de suas imagens personalizadas.

		Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## Próximas etapas

Agora você pode [criar uma VM personalizada](virtual-machines-windows-classic-createportal.md) usando a imagem carregada.

<!---HONumber=AcomDC_0727_2016-->