<properties
	pageTitle="Criar uma máquina virtual que executa o Windows no Azure"
	description="Crie uma VM (máquina virtual) do Windows no portal do Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/05/2015"
	ms.author="kathydav"/>

# Criar uma máquina virtual executando o Windows no portal do Azure

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service management](virtual-machines-ps-create-preconfigure-windows-vms.md)


Este tutorial mostra como é fácil criar uma VM (máquina virtual) do Azure no Portal do Azure. Vamos usar uma imagem do Windows Server como exemplo, mas ela é apenas uma das muitas imagens que o Azure oferece. Observe que as opções de imagem dependem de sua assinatura. Por exemplo, imagens de desktop podem estar disponíveis para assinantes do MSDN.

Você também pode criar VMs usando [suas próprias imagens](virtual-machines-create-upload-vhd-windows-server.md). Para saber mais sobre esse e outros métodos, consulte [Diferentes maneiras de criar uma máquina virtual do Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Passo a passo em vídeo

Aqui está um passo a passo deste tutorial.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Como criar a máquina virtual

Esta seção mostra como usar a opção **Da Galeria** no portal do Azure para criar a máquina virtual. Esta opção fornece mais escolhas de configuração que a opção **Criação rápida**. Por exemplo, se desejar entrar em uma máquina virtual em uma rede virtual, será necessário usar a opção **Da galeria**.

> [AZURE.NOTE]Você também pode experimentar o [Portal de Visualização do Azure](https://portal.azure.com), mais avançado e personalizável, para criar uma máquina virtual, automatizar a implantação de modelos de aplicativo de várias máquinas virtuais, usar os recursos de diagnóstico e monitoramento de VM aperfeiçoados e muito mais. Os opções de configuração da VM disponível nos dois Portais sobrepõem-se bastante, mas não são idênticos.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## Próximas etapas

- Faça logon na máquina virtual. Para obter instruções, consulte [Como fazer logon em uma máquina virtual que executa o Windows Server](virtual-machines-log-on-windows-server.md).

- Anexe um disco para armazenar dados. Você pode anexar tanto discos vazios como discos que contenham dados. Para obter instruções, consulte o [tutorial Anexar um disco de dados](storage-windows-attach-disk.md).

## Recursos adicionais

Para saber mais sobre o que você pode configurar para uma VM e quando pode fazê-lo, consulte [Sobre definições de configuração de VM do Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx).

<!---HONumber=August15_HO6-->