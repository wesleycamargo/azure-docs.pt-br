<properties
	pageTitle="Perguntas frequentes sobre VMs do Windows | Microsoft Azure"
	description="Fornece respostas para algumas das perguntas mais comuns sobre as máquinas virtuais do Windows criadas com o modelo do Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="cynthn"/>

# Perguntas frequentes sobre as Máquinas Virtuais do Windows 


Este artigo trata de algumas das perguntas mais comuns sobre as máquinas virtuais do Windows criadas no Azure com o modelo de implantação do Gerenciador de Recursos. Para a versão deste tópico relativa ao Linux, veja [Perguntas frequentes sobre as Máquinas Virtuais Linux](virtual-machines-linux-faq.md)

## O que eu posso executar em uma VM do Azure?

Todos os assinantes podem executar software para servidores em uma máquina virtual do Azure. Para saber mais sobre a política de suporte para software de servidor Microsoft em execução no Azure, veja [Suporte a software de servidor Microsoft para Máquinas Virtuais do Azure](https://support.microsoft.com/kb/2721672)

Determinadas versões do Windows 7 e Windows 8.1 estão disponíveis para assinantes de benefícios do Azure na MSDN e assinantes do modelo "pré-pago" do Desenvolvimento e Teste da MSDN, para tarefas de teste e desenvolvimento. Para obter detalhes, incluindo instruções e limitações, veja [Imagens do Windows Client para assinantes do MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).


## Quanto armazenamento eu posso usar com uma máquina virtual?

Cada disco de dados pode ter até 1 TB. O número de discos de dados que você pode usar depende do tamanho da máquina virtual. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-windows-sizes.md).

Uma conta de armazenamento do Azure fornece armazenamento para o disco do sistema operacional e quaisquer discos de dados. Cada disco é um arquivo .vhd armazenado como um blob de páginas. Para obter detalhes sobre preços, veja [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).


## Como posso acessar minha máquina virtual?

Você precisa estabelecer uma conexão remota usando RDP (Conexão de Área de Trabalho Remota) para uma VM do Windows. Para obter instruções, consulte [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](virtual-machines-windows-connect-logon.md). Há suporte para o máximo de duas conexões simultâneas, a menos que o servidor esteja configurado como um host de sessão dos Serviços de Área de Trabalho Remota.


Se você estiver tendo problemas com a Área de Trabalho Remota, veja [Solucionar problemas de conexões da Área de Trabalho Remota para uma Máquina Virtual Azure baseada no Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

Se estiver familiarizado com o Hyper-V, você pode estar em busca de uma ferramenta semelhante ao VMConnect. O Azure não oferece uma ferramenta semelhante porque não há suporte para o acesso de console a uma máquina virtual.

## Posso usar o disco temporário (a unidade d:, por padrão) para armazenar dados?

Você não deve usar o disco temporário para armazenar dados. Ele é apenas um armazenamento temporário, você se arriscaria a perder dados que não podem ser recuperados. Isso pode ocorrer quando a máquina virtual é movida para um host diferente. O redimensionamento de uma máquina virtual, atualização do host ou uma falha de hardware no host são algumas das razões que podem levar à mudança de uma máquina virtual.

Se tiver um aplicativo que precise usar a letra da unidade D:, você poderá reatribuir as letras da unidade para que o disco temporário use algo diferente de D:. Para obter instruções, veja [Alterar a letra da unidade do disco temporário do Windows](virtual-machines-windows-classic-change-drive-letter.md).

## Como eu posso alterar a letra da unidade do disco temporário?

Você pode alterar a letra da unidade movendo o arquivo de paginação e reatribuindo as letras de unidade, mas precisará certificar-se de seguir as etapas em uma ordem específica. Para obter instruções, veja [Alterar a letra da unidade do disco temporário do Windows](virtual-machines-windows-classic-change-drive-letter.md).

## Posso adicionar uma VM existente a um conjunto de disponibilidade?

Não. Se você quiser que sua VM faça parte de um conjunto de disponibilidade, precisará criar a VM no conjunto. Atualmente, não há uma maneira de adicionar uma VM a um conjunto de disponibilidade após ela ter sido criada.

## Posso carregar uma máquina virtual no Azure?

Sim. Para obter instruções, consulte [Carregar uma imagem de VM do Windows no Azure ](virtual-machines-windows-upload-image.md)

## Posso redimensionar o disco do sistema operacional?

Sim. Para obter instruções, veja [Como expandir a unidade do sistema operacional de uma Máquina Virtual em um Grupo de Recursos do Azure](virtual-machines-windows-expand-os-disk.md).

## Posso copiar ou clonar uma VM do Azure existente?

Sim. Para obter instruções, veja [Como criar uma cópia de uma máquina virtual do Windows no modelo de implantação do Resource Manager](virtual-machines-windows-specialized-image.md).

## Por que não vejo as regiões Central e Leste do Canadá por meio do Azure Resource Manager?

As duas novas regiões, Central do Canadá e Leste do Canadá, não são registradas automaticamente para a criação de máquinas virtuais para as assinaturas existentes do Azure. Esse registro é feito automaticamente quando uma máquina virtual é implantada por meio do Portal do Azure para qualquer outra região usando o Azure Resource Manager. Depois que uma máquina virtual é implantada em qualquer outra região do Azure, as novas regiões devem estar disponíveis para máquinas virtuais subsequentes.

## O Azure oferece suporte a VMs Linux?

Sim. Para criar rapidamente uma VM Linux para experimentá-la, veja [Criar uma VM Linux no Azure usando o Portal](virtual-machines-linux-quick-create-portal.md).

## Posso adicionar um NIC à minha VM depois que ela é criada?

Não. A adição de uma NIC só pode ser feita no momento da criação.

<!---HONumber=AcomDC_0817_2016-->