<properties
	pageTitle="Perguntas frequentes sobre VMs Linux | Microsoft Azure"
	description="Fornece respostas para algumas das perguntas mais comuns sobre as máquinas virtuais Linux criadas com o modelo do Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="cynthn"/>

# Perguntas frequentes sobre as Máquinas Virtuais Linux 


Este artigo trata de algumas das perguntas mais comuns feitas pelos usuários sobre as máquinas virtuais Linux criadas no Azure com o modelo de implantação do Resource Manager. Para a versão deste tópico para o Windows, veja [Perguntas frequentes sobre Máquinas Virtuais do Windows](virtual-machines-windows-faq.md)

## O que eu posso executar em uma VM do Azure?

Todos os assinantes podem executar software para servidores em uma máquina virtual do Azure. Para saber mais, veja [Linux em distribuições endossadas pelo Azure](virtual-machines-linux-endorsed-distros.md)


## Quanto armazenamento eu posso usar com uma máquina virtual?

Cada disco de dados pode ter até 1 TB. O número de discos de dados que você pode usar depende do tamanho da máquina virtual. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md).

Uma conta de armazenamento do Azure fornece armazenamento para o disco do sistema operacional e quaisquer discos de dados. Cada disco é um arquivo .vhd armazenado como um blob de páginas. Para obter detalhes sobre preços, veja [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).



## Como posso acessar minha máquina virtual?

Você precisa estabelecer uma conexão remota para fazer logon na máquina virtual, usando SSH (Secure Shell). Veja as instruções sobre como conectar [do Windows](virtual-machines-linux-ssh-from-windows.md) ou [do Linux e do Mac](virtual-machines-linux-ssh-from-linux.md). Por padrão, o SSH permite um máximo de 10 conexões simultâneas. Você pode aumentar esse número editando o arquivo de configuração.


Se você estiver tendo problemas, confira [Solucionar problemas de conexões SSH (Secure Shell)](virtual-machines-linux-troubleshoot-ssh-connection.md).

## Posso usar o disco temporário (/dev/sdb1) para armazenar os dados?

Você não deve usar o disco temporário (/dev/sdb1) para armazenar os dados. Ele só existe para armazenamento temporário, você se arriscaria a perder dados que não podem ser recuperados.

## Posso copiar ou clonar uma VM do Azure existente?

Sim. Para obter instruções, veja [Como criar uma cópia de uma máquina virtual do Linux no modelo de implantação do Resource Manager](virtual-machines-linux-specialized-image.md).

## Por que não vejo as regiões Central e Leste do Canadá por meio do Azure Resource Manager?

As duas novas regiões, Central e Leste, do Canadá não são automaticamente registradas para a criação de máquinas virtuais para as assinaturas existentes do Azure. Esse registro será feito automaticamente quando uma máquina virtual for implantada por meio do portal do Azure para qualquer outra região usando o Azure Resource Manager. Depois que uma máquina virtual é implantada em qualquer outra região do Azure, as novas regiões devem estar disponíveis para máquinas virtuais subsequentes.

<!---HONumber=AcomDC_0518_2016-->