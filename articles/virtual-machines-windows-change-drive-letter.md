<properties 
	pageTitle="Como alterar a letra da unidade do disco temporário do Windows" 
	description="Descreve como remapear o disco temporário em uma VM do Windows no Azure" 
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
	ms.date="01/15/2015" 
	ms.author="kathydav"/>

#Como alterar a letra da unidade do disco temporário do Windows

Se você precisar usar a unidade D para armazenar dados, siga estas instruções para usar uma unidade diferente para o disco temporário. Nunca use a unidade temporária para armazenar os dados que você precisa manter.

Antes de começar, você precisará de um disco de dados anexado à máquina virtual, permitindo que você armazene o arquivo de paginação (pagefile. sys) do Windows durante esse procedimento. Consulte [Como anexar um disco de dados para uma máquina virtual Windows] se você não tiver um. Para instruções sobre como descobrir quais discos estão conectados, consulte "Gerenciar seus discos" [Sobre discos da máquina virtual no Azure].

Se você quiser usar um disco de dados existente na unidade D, verifique se que você carregou também o VHD para a conta de armazenamento. Para obter instruções, consulte as etapas 3 e 4 em [Criar e carregar um VHD do Windows Server no Azure].

> [AZURE.WARNING] Se você redimensionar uma máquina virtual e ao fazer isso a máquina virtual move para um host diferente, a unidade temporária altera de volta para a unidade D.

##Alterar a letra da unidade

1. Faça logon na máquina virtual. 

2. Mova pagefile.sys da unidade D para outra unidade.

3. Reinicie a máquina virtual.

4. 	Faça logon novamente e altere a letra da unidade de D para E.

5.	No [Portal de Gerenciamento do Azure](http://manage.windowsazure.com), anexe um disco de dados existente ou esvazie o disco de dados.

6.	Faça logon na máquina virtual novamente, inicialize o disco e atribua D como a letra da unidade do disco que você acabou de anexar.

7.	Verifique se E está mapeada para o disco de armazenamento temporário.

8.	Mova pagefile.sys da outra unidade para a unidade E.

##Recursos adicionais
[Como fazer logon em uma máquina virtual executando o Windows Server]

[Como desanexar um disco de dados de uma máquina virtual]

[Sobre contas de armazenamento do Azure]

<!--Link references-->
[Como anexar um disco de dados para uma máquina virtual Windows]: ../storage-windows-attach-disk
[Sobre discos da máquina virtual no Azure]: ../http://msdn.microsoft.com/pt-br/library/azure/dn790303.aspx
[Criar e carregar um VHD do Windows Server no Azure]: ../virtual-machines-create-upload-vhd-windows-server/
[Como fazer logon em uma máquina virtual executando o Windows Server]: ../virtual-machines-log-on-windows-server/
[Como desanexar um disco de dados de uma máquina virtual]: ../storage-windows-detach-disk/
[Sobre contas de armazenamento do Azure]: ../storage-whatis-account/


<!--HONumber=42-->
