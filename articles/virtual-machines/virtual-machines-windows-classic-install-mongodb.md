---
title: Instalar o MongoDB em uma VM do Windows | Microsoft Docs
description: "Saiba como instalar o MongoDB em uma VM do Azure criada com o modelo de implantação clássico que executa o Windows Server."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4095df41-bb69-4bbe-9c1c-70923b0d84ba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: f198d295272c8c860bc455cfa300cad3ff375c5d
ms.openlocfilehash: cb618cbc47748a002351caa3a5935c9824c2bc32


---
# <a name="install-mongodb-on-a-windows-vm"></a>Instalar o MongoDB em uma VM do Windows
> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).  Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para instalar e configurar o MongoDB usando o modelo de implantação do Resource Manager, consulte [este artigo](virtual-machines-windows-install-mongodb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[O O O MongoDB][O O MongoDB] é um popular banco de dados NoSQL de código-fonte aberto e de alto desempenho. Este artigo orientará você a criar uma máquina virtual (VM) do Windows Server usando o [portal clássico do Azure][AzurePortal]. Em seguida, você poderá criar e anexar um disco de dados para a máquina virtual antes de instalar e configurar o MongoDB. Se houver uma VM existente no Azure que você deseja usar, você pode prosseguir diretamente para a [instalação e configuração do MongoDB](#install-and-run-mongodb-on-the-virtual-machine).

## <a name="create-a-virtual-machine-running-windows-server"></a>Criar uma máquina virtual na qual o Windows Server está em execução
Siga estas instruções para criar uma máquina virtual.

[!INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [!NOTE]
> Você pode adicionar um ponto de extremidade para o MongoDB enquanto cria a máquina virtual e configurá-la da seguinte maneira: nomeie-a como **Mongo**, use **TCP** como o protocolo e defina as portas pública e privada como **27017**.
> 
> 

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Para fornecer um armazenamento para a máquina virtual, anexe um disco de dados e inicialize-o de forma que o Windows possa usá-lo. Se já tiver um disco de dados, você poderá anexar esse disco ou um disco vazio.

[!INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Para obter instruções sobre como inicializar o disco, veja “Como: inicializar um novo disco de dados no Windows Server” em [Como anexar um disco de dados a uma máquina virtual do Windows](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Instalar e executar o MongoDB na máquina virtual
[!INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Resumo
Neste tutorial você aprendeu a criar uma máquina virtual do Windows Server e a se conectar remotamente a ela, além de anexar um disco de dados.  Você também aprendeu como instalar e configurar o MongoDB na máquina virtual Windows. Agora você pode acessar o MongoDB na máquina virtual baseada no Windows seguindo os tópicos avançados na [documentação do MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[O O MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com



<!--HONumber=Nov16_HO4-->


