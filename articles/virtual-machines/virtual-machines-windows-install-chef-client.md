<properties 
	pageTitle="Sobre o Chef e as Máquinas Virtuais do Azure" 
	description="Descreve como instalar e configurar o Chef em uma VM no Azure" 
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
	ms.date="05/20/2015" 
	ms.author="kathydav"/>

#Sobre o Chef e as Máquinas Virtuais do Azure

Chef é um sistema de automação para compilar, implantar e gerenciar a infraestrutura. Os recursos são gerenciados usando-se receitas, que são definições reutilizáveis que fornecem instruções para tarefas como configurar um servidor Web.

Chef é um sistema cliente/servidor. Para saber as opções sobre como usar um servidor do Chef, consulte [Escolher a instalação](http://www.getchef.com/chef/choose-your-version/). Você precisará de informações sobre o servidor Chef para configurar o cliente.

Para instalar o cliente Chef em uma máquina virtual do Azure, você tem estas opções:

- Use o Portal de Gerenciamento do Azure para instalar o cliente Chef ao criar uma máquina virtual na qual o Windows Server 2012 ou o Windows Server 2012 R2 está em execução. Para obter instruções, consulte [Portal do Microsoft Azure](https://docs.chef.io/azure_portal.html).
- Use o PowerShell do Azure para instalar o cliente Chef em uma máquina virtual existente. Um [script](https://gist.github.com/kaustubh-d/cea1aa75baebd3615609) de exemplo está disponível no Github.
- Use um plug-in do Chef, [knife-azure](http://docs.getchef.com/plugin_knife_azure.html), para criar uma instância de máquina virtual e instalar o cliente do Chef.   


##Recursos adicionais
[Chef e Microsoft Azure]

[Como fazer logon em uma máquina virtual executando o Windows Server]

[Como fazer logon em uma máquina virtual executando o Linux]

[Gerenciar extensões]

<!--Link references-->
[Chef e Microsoft Azure]: http://www.getchef.com/solutions/azure/
[Como fazer logon em uma máquina virtual executando o Windows Server]: virtual-machines-log-on-windows-server.md
[Como fazer logon em uma máquina virtual executando o Linux]: virtual-machines-linux-how-to-log-on.md
[Gerenciar extensões]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409


 

<!---HONumber=July15_HO3-->