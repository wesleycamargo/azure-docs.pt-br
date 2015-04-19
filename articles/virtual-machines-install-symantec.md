<properties 
	pageTitle="Como instalar e configurar o Symantec Endpoint Protection em uma VM do Azure" 
	description="Descreve como instalar e configurar a extensão de segurança Symantec Endpoint Protection em uma VM nova ou existente no Azure" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="kathydav"/>

#Como instalar e configurar o Symantec Endpoint Protection em uma VM do Azure

Este artigo mostra como instalar e configurar o cliente do Symantec Endpoint Protection em uma VM (máquina virtual) nova ou existente com o Windows Server em execução. Trata-se do cliente completo, que inclui serviços como proteção contra vírus e spyware, firewall e prevenção de intrusão. 

O cliente é instalado como uma extensão de segurança usando-se o Agente de VM. Em uma máquina virtual nova, você instalará o agente com o cliente do ponto de extremidade. Em uma máquina virtual existente sem o agente, você precisará baixar e instalá-lo primeiro. Este artigo aborda ambas as situações.

Se tiver uma assinatura da Symantec para uma solução local, você poderá usá-la para proteger as máquinas virtuais do Azure. Se ainda não for cliente, você poderá fazer uma assinatura de avaliação. Para obter mais informações sobre essa solução, consulte [Symantec Endpoint Protection na plataforma Azure da Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=403942). Essa página também fornece links para informações de licenciamento e instruções alternativas para instalar o cliente se você já for um cliente da Symantec.

##Instalar o Symantec Endpoint Protection em uma máquina virtual nova

O [Portal de Gerenciamento do Azure](http://manage.windowsazure.com) permite instalar o Agente de VM e a extensão de segurança da Symantec quando você usa a opção **Da Galeria** para criar a máquina virtual. Usar essa abordagem é uma maneira fácil de adicionar proteção da Symantec caso você esteja criando uma única máquina virtual. 

Essa opção **Da Galeria** abre um assistente que ajuda você a configurar a máquina virtual. Você usa a última página do assistente para instalar o Agente de VM e a extensão de segurança da Symantec. 

Para obter instruções gerais, consulte [Criar uma máquina virtual na qual o Windows Server esteja em execução](virtual-machines-windows-tutorial.md). Quando você chegar à última página do assistente:

1.	Em VM Agent, **Instalar o agente da VM** já devem ser verificados.

2.	Em Extensões de Segurança, marque **Symantec Endpoint Protection**.


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	Clique na marca de seleção na parte inferior da página para criar a máquina virtual.

## Instalar o Symantec Endpoint Protection em uma máquina virtual existente

Antes de começar, você precisará do seguinte:

- Módulo PowerShell do Azure, versão 0.8.2 ou mais nova. Você pode verificar a versão do PowerShell do Azure instalado com o comando **Get-Module azure | format-table version**. Para obter instruções e um link para a versão mais recente, consulte [Como instalar e configurar o PowerShell do Azure](install-configure-powershell.md).  

- O Agente de VM. 

Primeiramente, verifique se que o agente de VM já está instalado. Preencha o nome do serviço de nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos em um prompt de comando do PowerShell do Azure com nível de administrador. Substitua tudo entre aspas, incluindo os caracteres < e >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

Se você não souber o nome da máquina virtual e serviço de nuvem, execute **Get-AzureVM** para exibir essas informações para todas as máquinas virtuais em sua assinatura atual.

Se o comando **write-host** exibe **True**, o agente de VM está instalado. Se ele exibir **False**, consulte as instruções e um link para download na postagem do blog do Azure [Agente de VM e extensões - parte 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Se o Agente de VM Agent estiver instalado, execute estes comandos para instalar o agente Symantec Endpoint Protection.

	$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection
	Set-AzureVMExtension -Publisher Symantec -Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Para verificar se a extensão de segurança Symantec foi instalada e está atualizada:

1.	Faça logon na máquina virtual. Para obter mais informações, consulte [Como fazer logon em uma Máquina Virtual executando Windows Server](virtual-machines-log-on-windows-server.md).
2.	Para Windows Server 2008 R2, clique em **Iniciar > Symantec Endpoint Protection**. Para o Windows Server 2012 ou Windows Server 2012 R2, na tela inicial, digite **Symantec** e, em seguida, clique em **Symantec Endpoint Protection**.
3.	Na guia **Status** da janela de **Status do Symantec Endpoint Protection**, aplique atualizações ou reinicie se necessário.

## Recursos adicionais

[Como fazer logon em uma máquina virtual executando o Windows Server](virtual-machines-log-on-windows-server.md)

[Gerenciar extensões](https://msdn.microsoft.com/library/dn606311.aspx)


<!--HONumber=47-->
