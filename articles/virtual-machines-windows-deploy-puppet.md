<properties 
	pageTitle="Sobre o Puppet e as Máquinas Virtuais do Azure" 
	description="Descreve como instalar e configurar o Puppet em uma VM no Azure" 
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
	ms.date="01/23/2015" 
	ms.author="kathydav"/>

#Sobre o Puppet e as Máquinas Virtuais do Azure

<p>Puppet Enterprise é um software de automação para compilar, implantar e gerenciar a infraestrutura. Você pode usá-lo para gerenciar o ciclo de vida da infraestrutura de TI, incluindo: descoberta, provisionamento, sistema operacional e gerenciamento de configuração de aplicativos, orquestração e relatório.   

Puppet é um sistema cliente/servidor. O Puppet Master e o Puppet Enterprise Agent estão disponíveis para instalação por meio do Azure:

- O Puppet Master está disponível como uma imagem pré-configurada, instalada em um servidor Ubuntu. Também é possível instalar o Puppet Enterprise em um servidor existente, mas usando a imagem da maneira mais simples como introdução. Você precisará de informações sobre o servidor para configurar o agente. 
- O Puppet Enterprise Agent está disponível como uma extensão de máquina virtual que você pode instalar ao criar uma máquina virtual ou instalar em uma máquina virtual existente.

Para obter instruções, baixe o "Guia de Introdução" disponível na página [Microsoft Windows e Azure](http://puppetlabs.com/solutions/microsoft).  


##Recursos adicionais
[Novas integrações com o Microsoft Azure e o Visual Studio]

[Como fazer logon em uma máquina virtual executando o Windows Server]

[Como fazer logon em uma máquina virtual que executa o Linux]

[Gerenciar extensões]

<!--Link references-->
[Novas integrações com o Microsoft Azure e o Visual Studio]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
[Como fazer logon em uma máquina virtual executando o Windows Server]: ../virtual-machines-log-on-windows-server/
[Como fazer logon em uma máquina virtual que executa o Linux]: ../virtual-machines-linux-how-to-log-on
[Recursos e extensões de VM do Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409



<!--HONumber=47-->
