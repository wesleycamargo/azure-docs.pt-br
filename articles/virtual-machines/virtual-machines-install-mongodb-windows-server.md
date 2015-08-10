<properties
	pageTitle="Instalar o MongoDB em uma máquina virtual que conta com o Windows Server"
	description="Saiba como instalar o MongoDB em uma máquina virtual do Azure que executa o Windows Server."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="dkshir"/>

#Instalar o MongoDB em uma máquina virtual na qual o Windows Server está em execução

[O MongoDB][MongoDB] é um banco de dados NoSQL de código-fonte aberto e com alto desempenho. Usando o [Portal do Azure][AzureManagementPortal], você pode criar uma máquina virtual que execute o Windows Server da galeria de imagens. Em seguida, você pode instalar e configurar um banco de dados MongoDB na máquina virtual.


## Criar uma máquina virtual na qual o Windows Server está em execução

Siga estas instruções para criar uma máquina virtual.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

> [AZURE.NOTE]Você pode adicionar um ponto de extremidade para o MongoDB enquanto cria a máquina virtual e configurá-la da seguinte maneira: nomeie-a como **Mongo**, use **TCP** como o protocolo e defina as portas pública e privada como **27017**.

## Anexar um disco de dados
Para fornecer um armazenamento para a máquina virtual, anexe um disco de dados e inicialize-o de forma que o Windows possa usá-lo. É possível anexar um disco existente caso você já tenha dados que deseja usar ou anexar um disco vazio.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Para obter instruções sobre como inicializar o disco, consulte "Como inicializar um novo disco de dados no Windows Server" em [Como anexar um disco de dados a uma Máquina Virtual do Windows](storage-windows-attach-disk.md).

## Instalar e executar o MongoDB na máquina virtual

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Resumo
Neste tutorial você aprendeu a criar uma máquina virtual do Windows Server e a se conectar remotamente a ela, além de anexar um disco de dados. Você também aprendeu como instalar e configurar o MongoDB na máquina virtual Windows. Agora, você pode acessar o MongoDB na máquina virtual do Windows, seguindo os tópicos avançados na [Documentação do MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com

<!---HONumber=July15_HO5-->