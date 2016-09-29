<properties
	pageTitle="Diretrizes de máquinas virtuais Linux | Microsoft Azure"
	description="Saiba mais sobre as principais diretrizes de design e implementação referentes à implantação de máquinas virtuais Linux no Azure"
	documentationCenter=""
	services="virtual-machines-linux"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="iainfou"/>

# Diretrizes de máquinas virtuais

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Este artigo destaca as noções básicas sobre as etapas de planejamento necessárias para criar e gerenciar VMs (máquinas virtuais) em seu ambiente do Azure.

## Diretrizes de implementação de VMs
Decisões:

- Quantas VMs são necessárias para suas várias camadas de aplicativos e componentes de sua infraestrutura?
- De quais recursos da CPU e da memória cada VM precisa e quais são os requisitos de armazenamento?

Tarefas:

- Definir as cargas de trabalho para seu aplicativo e os recursos de que as VMs precisam.
- Alinhar as demandas de recursos para cada VM com os tipos de VM e de armazenamento apropriados.
- Definir os grupos de recursos para as diferentes camadas e componentes de sua infraestrutura.
- Definir sua convenção de nomenclatura de VM.
- Crie suas VMs usando a CLI do Azure, o portal da Web, ou os modelos do Resource Manager.

## Máquinas virtuais

Um dos principais componentes em seu ambiente do Azure provavelmente consiste em VMs. É nelas em que você executa aplicativos, bancos de dados, serviços de autenticação etc.

É importante entender os [diferentes tamanhos de VM](virtual-machines-linux-sizes.md) para dimensionar corretamente seu ambiente de uma perspectiva de desempenho e custo. Se as VMs não tiverem núcleos de CPU ou memória suficiente, o desempenho do aplicativo será afetado, independentemente de como ele for projetado e desenvolvido. Examine as sugestões de cargas de trabalho para cada série de VM como ponto de partida ao decidir o tamanho da VM a ser usado para cada componente em sua infraestrutura. É possível [alterar o tamanho de uma VM](virtual-machines-linux-change-vm-size.md) após a implantação.

O armazenamento desempenha um papel fundamental no desempenho da VM. Você pode usar o armazenamento Standard que use discos giratórios regulares ou o armazenamento Premium para altas cargas de trabalho de E/S e o desempenho de pico que usam os discos SSD. Assim como ocorre com o tamanho da VM, há considerações de custo para selecionar a mídia de armazenamento. Leia o [artigo sobre diretrizes de infraestrutura de armazenamento](virtual-machines-linux-infrastructure-storage-solutions-guidelines.md) para entender como projetar o armazenamento apropriado para o desempenho ideal de suas VMs.


## Grupos de recursos
Componentes como VMs são agrupados logicamente para facilitar o gerenciamento e a manutenção usando os [Grupos de Recursos do Azure](../resource-group-overview.md). Com os grupos de recursos, você pode criar, gerenciar e monitorar todos os recursos que compõem determinado aplicativo. Você também pode implementar [controles de acesso baseado em função](../active-directory/role-based-access-control-what-is.md) para conceder acesso a outras pessoas de sua equipe apenas aos recursos necessários. Reserve tempo para planejar os grupos de recursos e as atribuições de função. Há diferentes abordagens para, de fato, criar e implementar grupos de recursos; portanto, não deixe de ler o [artigo sobre diretrizes de grupos de recursos](virtual-machines-linux-infrastructure-resource-groups-guidelines.md) para entender a melhor maneira de criar suas VMs.


## Modelos 
É possível criar modelos, definidos por arquivos JSON declarativos, para criar suas VMs. Normalmente, os modelos também criam o armazenamento, a rede, as interfaces de rede, o endereçamento IP necessários etc., juntamente com as VMs em si. Você pode usar modelos para criar ambientes consistentes e reproduzíveis para fins de desenvolvimento e teste, a fim de replicar com facilidade os ambientes de produção e vice-versa. Leia mais sobre como [criar e usar modelos](../resource-group-overview.md#template-deployment) para entender como usá-los para criar e implantar suas VMs.


## Próximas etapas
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->