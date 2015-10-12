<properties
	pageTitle="Instalar o Trend Micro Deep Security em uma VM | Microsoft Azure"
	description="Este artigo descreve como instalar e configurar o Trend Micro security em uma VM criada com o modelo clássico de implantação no Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="dkshir"/>


# Como instalar e configurar o Trend Micro Deep Security as a Service em uma VM do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda como instalar aplicativos em uma máquina virtual criada com o modelo de implantação clássico.

Este artigo mostra como instalar e configurar o Trend Micro Deep Security as a Service em uma VM (máquina virtual) nova ou existente com o Windows Server em execução. O Deep Security as a Service inclui proteção anti-malware, firewall, sistema de prevenção de intrusões e monitoramento de integridade.

O cliente é instalado como uma extensão de segurança via Agente de VM. Em uma máquina virtual nova, você instalará o Agente de VM com o Deep Security Agent. Em uma máquina virtual existente sem o Agente de VM, você precisará baixar e instalá-lo primeiro. Este artigo aborda ambas as situações.

Se tiver uma assinatura existente da Trend Micro para uma solução local, você poderá usá-la para ajudar a proteger as máquinas virtuais do Azure. Se ainda não for cliente, você poderá fazer uma assinatura de avaliação. Para saber mais sobre essa solução, confira a postagem no blog da Trend Micro [Extensão do agente de VM do Microsoft Azure para segurança profunda](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## Instalar o Deep Security Agent em uma máquina virtual nova

O [portal do Azure](http://manage.windowsazure.com) permite que você instale o agente de VM e a extensão de segurança Trend Micro, quando usa a opção **Da galeria** para criar a máquina virtual. Usar essa abordagem é uma maneira fácil de adicionar proteção da Trend Micro caso você esteja criando uma única máquina virtual.

Essa opção **Da Galeria** abre um assistente que ajuda você a configurar a máquina virtual. Você usa a última página do assistente para instalar o Agente de VM e a extensão de segurança da Trend Micro. Para obter instruções gerais, consulte [Criar uma máquina virtual executando o Windows no portal do Azure](virtual-machines-windows-tutorial-classic-portal.md). Quando chegar à última página do assistente, faça o seguinte:

1.	Em **Agente de VM**, marque **Instalar o agente de VM**.

2.	Em **Extensões de segurança**, marque **Trend Micro Deep Security Agent**.

	![Instalar o Agente de VM e o Deep Security Agent](./media/virtual-machines-install-trend/InstallVMAgentandTrend.png)

3.	Clique na marca de seleção para criar a máquina virtual.

## Instalar o Deep Security Agent em uma máquina virtual existente

Para isso, você precisará do seguinte:

- O módulo do Azure PowerShell versão 0.8.2 ou mais recente instalado no computador local. Você pode verificar a versão do Azure PowerShell que tem instalada usando o comando **Get-Module azure | format-table version**. Para obter instruções e um link para a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell](../install-configure-powershell.md).

- O agente de VM instalado na máquina virtual de destino.

Primeiramente, verifique se que o agente de VM já está instalado. Preencha o nome do serviço de nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos em um prompt de comando do Azure PowerShell com nível de administrador. Substitua tudo entre aspas, incluindo os caracteres < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
	write-host $vm.VM.ProvisionGuestAgent

Se você não souber o nome da máquina virtual e serviço de nuvem, execute **Get-AzureVM** para exibir essas informações para todas as máquinas virtuais em sua assinatura atual.

Se o comando **write-host** exibir **True**, o agente de VM está instalado. Se ele retornar **False**, confira as instruções e um link para download na postagem do blog do Azure [Agente de VM e extensões - parte 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Se o Agente de VM estiver instalado, execute estes comandos.

	$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

	Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## Próximas etapas

Leva alguns minutos para o agente abrir quando ele está instalado. Depois disso, você precisará ativar o Deep Security na máquina virtual de forma que ele possa ser gerenciado por um Deep Security Manager. Consulte os itens a seguir para obter instruções adicionais:

- Artigo de tendência sobre essa solução, [Segurança na nuvem Instant-On do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
- Um [script do Windows PowerShell de exemplo](http://go.microsoft.com/fwlink/?LinkId=404100) para configurar a máquina virtual
- [Instruções](http://go.microsoft.com/fwlink/?LinkId=404099) para o exemplo

## Recursos adicionais

[Como fazer logon em uma máquina virtual executando o Windows Server]

[Recursos e extensões de VM do Azure]


<!--Link references-->
[Como fazer logon em uma máquina virtual executando o Windows Server]: virtual-machines-log-on-windows-server.md
[Recursos e extensões de VM do Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=Oct15_HO1-->