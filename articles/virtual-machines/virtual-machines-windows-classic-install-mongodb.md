<properties
	pageTitle="Instalar o MongoDB em uma VM do Windows | Microsoft Azure"
	description="Saiba como instalar o MongoDB em uma VM do Azure criada com o modelo de implantação clássico que executa o Windows Server."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="iainfou"/>

#Instalar o MongoDB em uma VM do Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[O MongoDB][MongoDB] é um popular banco de dados NoSQL de código-fonte aberto e de alto desempenho. Este artigo orienta você pela criação de uma nova VM (máquina virtual) do Windows Server usando o [portal clássico do Azure][AzurePortal], criando e anexando um disco de dados à máquina virtual e, em seguida, instalar e configurar o MongoDB. Se tiver uma VM existente no Azure que você deseja usar, você poderá ir diretamente para [instalar e configurar o MongoDB](#install-and-run-mongo-on-win2k8-vm).


## Criar uma máquina virtual na qual o Windows Server está em execução

Siga estas instruções para criar uma máquina virtual.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] Você pode adicionar um ponto de extremidade para o MongoDB enquanto cria a máquina virtual e configurá-la da seguinte maneira: nomeie-a como **Mongo**, use **TCP** como o protocolo e defina as portas pública e privada como **27017**.

## Anexar um disco de dados
Para fornecer um armazenamento para a máquina virtual, anexe um disco de dados e inicialize-o de forma que o Windows possa usá-lo. É possível anexar um disco existente caso você já tenha dados que deseja usar ou anexar um disco vazio.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Para obter instruções sobre como inicializar o disco, veja “Como: inicializar um novo disco de dados no Windows Server” em [Como anexar um disco de dados a uma máquina virtual do Windows](virtual-machines-windows-classic-attach-disk.md).

## Instalar e executar o MongoDB na máquina virtual

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## Resumo
Neste tutorial você aprendeu a criar uma máquina virtual do Windows Server e a se conectar remotamente a ela, além de anexar um disco de dados. Você também aprendeu como instalar e configurar o MongoDB na máquina virtual Windows. Agora você pode acessar o MongoDB na máquina virtual baseada em Windows seguindo os tópicos avançados na [documentação do MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0629_2016-->