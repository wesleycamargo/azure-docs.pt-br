<properties
	pageTitle="VM do Oracle WebLogic Server e Banco de dados| Microsoft Azure"
	description="Criar um imagem do Oracle WebLogic Server 12c e Oracle Database 12c do Azure em execução no Windows Server 2012, usando o modelo de implantação do Gerenciador de Recursos."
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="05/17/2016"
	ms.author="rickstercdn" />

#Criar uma máquina virtual Oracle WebLogic Server 12c e Oracle Database 12c no Azure

[AZURE.INCLUDE [virtual-machines-common-oracle-support](../../includes/virtual-machines-common-oracle-support.md)]

Este artigo mostra como criar um Oracle WebLogic Server 12c e o banco de dados Oracle Database 12c em uma imagem que você criou anteriormente com o software Oracle instalado no Windows Server 2012 no Azure.

##Para criar o banco de dados hospedado nesta máquina virtual

Siga as instruções em [Criar uma máquina virtual Oracle Database 12c no Azure](virtual-machines-windows-classic-create-oracle-database.md), começando com a seção **Para criar o banco de dados usando a máquina virtual Oracle Database 12c no Azure**.

##Para configurar o Oracle WebLogic Server 12c hospedado nesta máquina virtual
Siga as instruções em [Criar uma máquina virtual Oracle WebLogic Server 12c no Azure](virtual-machines-windows-create-oracle-weblogic-server-12c.md), começando com a seção **Para configurar a máquina virtual Oracle WebLogic Server 12c no Azure**.

##Recursos adicionais
[Considerações diversas sobre imagens de máquinas virtuais do Oracle](virtual-machines-windows-classic-oracle-considerations.md)

[Lista de imagens de máquinas virtuais do Oracle](virtual-machines-linux-classic-oracle-images.md)

[Conexão ao Oracle Database de um aplicativo Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Oracle WebLogic Server 12c usando o Linux no Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_0601_2016-->