<properties
	pageTitle="Criar uma máquina virtual que executa o Windows no Azure"
	description="Crie uma máquina virtual do Windows no Portal do Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="cynthn"/>

# Criar uma máquina virtual executando o Windows no portal do Azure

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell: Resource Manager deployment](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell: Classic deployment](virtual-machines-ps-create-preconfigure-windows-vms.md)


Este tutorial mostra como é fácil criar uma VM (máquina virtual) do Azure no Portal do Azure. Vamos usar uma imagem do Windows Server como exemplo, mas ela é apenas uma das muitas imagens que o Azure oferece. Observe que as opções de imagem dependem de sua assinatura. Por exemplo, imagens de desktop podem estar disponíveis para assinantes do MSDN.

Você também pode criar VMs usando [suas próprias imagens](virtual-machines-create-upload-vhd-windows-server.md). Para saber mais sobre esse e outros métodos, consulte [Diferentes maneiras de criar uma máquina virtual do Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Passo a passo em vídeo

Aqui está um passo a passo deste tutorial.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Como criar a máquina virtual

Esta seção mostra como usar a opção **Da Galeria** no portal do Azure para criar a máquina virtual. Esta opção fornece mais escolhas de configuração que a opção **Criação rápida**. Por exemplo, se desejar entrar em uma máquina virtual em uma rede virtual, será necessário usar a opção **Da galeria**.

> [AZURE.NOTE]Você também pode experimentar usar o [Portal de Visualização do Azure](https://portal.azure.com), que é mais sofisticado e personalizável, para criar uma máquina virtual, usar monitoramento avançado e diagnóstico, usar armazenamento Premium e muito mais. As opções disponíveis para configurar uma máquina virtual nos dois portais se repetem, mas não são as mesmas. Por exemplo, use o portal de visualização para configurar uma máquina virtual com armazenamento Premium.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## Próximas etapas

- Faça logon na máquina virtual. Para obter instruções, consulte [Como fazer logon em uma máquina virtual que executa o Windows Server](virtual-machines-log-on-windows-server.md).

- Anexe um disco para armazenar dados. Você pode anexar tanto discos vazios como discos que contenham dados. Para obter instruções, consulte o [tutorial Anexar um disco de dados](storage-windows-attach-disk.md).

<!---HONumber=Sept15_HO3-->