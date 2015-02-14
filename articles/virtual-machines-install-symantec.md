<properties 
	pageTitle="Como instalar e configurar o Symantec Endpoint Protection em uma VM do Azure" 
	description="Descreve como instalar e configurar o Symantec Endpoint Protection em uma máquina virtual no Azure" 
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
	ms.date="1/26/2015" 
	ms.author="kathydav"/>

#Como instalar e configurar o Symantec Endpoint Protection em uma VM do Azure

Este artigo mostra como instalar e configurar o cliente do Symantec Endpoint Protection em uma VM (máquina virtual) nova ou existente com o Windows Server em execução. Trata-se do cliente completo, que inclui serviços como proteção contra vírus e spyware, firewall e prevenção de intrusão. 

O cliente é instalado como uma extensão de segurança usando-se o Agente de VM. Em uma máquina virtual nova, você instalará o agente com o cliente do ponto de extremidade. Em uma máquina virtual existente sem o agente, você precisará baixar e instalá-lo primeiro. Este artigo aborda ambas as situações.

Se tiver uma assinatura da Symantec para uma solução local, você poderá usá-la para proteger as máquinas virtuais do Azure. Se ainda não for cliente, você poderá fazer uma assinatura de avaliação. Para obter mais informações sobre essa solução, consulte [Symantec Endpoint Protection na plataforma Azure da Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=403942). Essa página também fornece links para informações de licenciamento e instruções alternativas para instalar o cliente se você já for um cliente da Symantec.

##Instalar o Symantec Endpoint Protection em uma máquina virtual nova

O [Portal de Gerenciamento do Azure](http://manage.windowsazure.com) permite instalar o Agente de VM e a extensão de segurança da Symantec quando você usa a opção **Da Galeria** para criar a máquina virtual. Usar essa abordagem é uma maneira fácil de adicionar proteção da Symantec caso você esteja criando uma única máquina virtual. 

Essa opção **Da Galeria** abre um assistente que ajuda você a configurar a máquina virtual. Você usa a última página do assistente para instalar o Agente de VM e a extensão de segurança da Symantec. 

Para obter instruções gerais, consulte [Criar uma máquina virtual na qual o Windows Server esteja em execução](http://go.microsoft.com/fwlink/p/?LinkId=403943). Quando você chegar à última página do assistente:

1.	Em VM Agent, **Instalar o agente da VM** já devem ser verificados.

2.	Em Extensões de Segurança, marque **Symantec Endpoint Protection**.


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	Clique na marca de seleção na parte inferior da página para criar a máquina virtual.

## Instalar o Symantec Endpoint Protection em uma máquina virtual existente

Antes de começar, você precisará do seguinte:

- Módulo PowerShell do Azure, versão 0.8.2 ou mais nova. Para obter instruções e um link para a versão mais recente, consulte [Como instalar e configurar o PowerShell do Azure](http://go.microsoft.com/fwlink/p/?LinkId=320552).  

- O Agente de VM. Para obter instruções e um link para o download, consulte a postagem no blog [VM Agent and Extensions - Part 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Para instalar a extensão de segurança Symantec em uma máquina virtual existente:

1.	Obtenha o nome do serviço de nuvem e o nome da máquina virtual. Se você não souber quais são, use o comando **Get-AzureVM** para exibir essas informações para todas as máquinas virtuais na assinatura atual. Em seguida, substitua tudo dentro de aspas, incluindo os caracteres < e > e execute estes comandos:

	<p>`$servicename = "<YourServiceName>"`
<p>`$name = "<YourVmName>"`
<p>`$vm = Get-AzureVM -ServiceName $servicename -Name $name`
<p>`Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`

2.	Na exibição do comando Get-AzureVMAvailableExtension, observe o número da versão para a propriedade da versão e, em seguida, execute estes comandos:

	<p>`$ver=<version number from the Version property>`
<p>`Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -Version $ver -VM $vm.VM`
<p>`Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

Para verificar se a extensão de segurança Symantec foi instalada e está atualizada:

1.	Faça logon na máquina virtual.
2.	Para Windows Server 2008 R2, clique em **Iniciar > Todos os programas > Symantec Endpoint Protection**. Para o Windows Server 2012, na tela inicial, digite **Symantec**e, em seguida, clique em **Symantec Endpoint Protection**.
3.	Na janela de status, aplique as atualizações, se necessário.

## Recursos adicionais
[Como fazer logon em uma máquina virtual executando o Windows Server]

[Gerenciar extensões]

<!--Link references-->
[Como fazer logon em uma máquina virtual executando o Windows Server]: ../virtual-machines-log-on-windows-server/

[Gerenciar extensões]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!--HONumber=42-->
