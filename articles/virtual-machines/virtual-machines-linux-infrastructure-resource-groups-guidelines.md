<properties
	pageTitle="Diretrizes de Grupos de Recursos| Microsoft Azure"
	description="Saiba mais sobre as principais diretrizes de design e implementação referentes à implantação de Grupos de Recursos em serviços de infraestrutura do Azure."
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
	ms.date="06/22/2016"
	ms.author="iainfou"/>

# Diretrizes de grupos de recursos do Azure

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Este artigo destaca as noções básicas sobre como criar seu ambiente logicamente e agrupar todos os componentes em Grupos de Recursos.


## Diretrizes de implementação de Grupos de Recursos

Decisões:

- Você vai criar Grupos de Recursos pelos componentes básicos da infraestrutura ou pela implantação completa do aplicativo?
- Você precisa restringir o acesso aos Grupos de Recursos usando Controles de Acesso Baseado em Função?

Tarefas:

- Definir quais componentes básicos da infraestrutura serão necessários e se serão usados Grupos de Recursos dedicados.
- Examinar como é possível implementar modelos do Resource Manager para obter implantações consistentes e reproduzíveis.
- Definir quais funções de acesso de usuário serão necessárias para controlar o acesso aos Grupos de Recursos.
- Criar o conjunto de Grupos de Recursos usando a convenção de nomenclatura. Você pode usar a CLI do Azure ou o portal.


## Grupos de recursos

No Azure, é possível agrupar logicamente recursos relacionados, como contas de armazenamento, redes virtuais e VMs (máquinas virtuais) para implantar, gerenciar e mantê-las como uma única entidade. Isso facilita a implantação de aplicativos, ao mesmo tempo que reúne todos os recursos relacionados de uma perspectiva de gerenciamento, ou a concessão de acesso para outros usuários a esse grupo de recursos. Para obter um entendimento mais abrangente de Grupos de Recursos, leia a [Visão geral do Azure Resource Manager](../resource-group-overview.md).

Um recurso importante para os Grupos de Recursos é a capacidade de criar seu ambiente usando um arquivo JSON que declara os recursos de armazenamento, de rede e de computação, juntamente com todas as configurações ou todos os scripts personalizados relacionados a serem aplicados. Com esses modelos JSON, é possível criar implantações consistentes e reproduzíveis para seus aplicativos. Isso facilita a criação de um ambiente em desenvolvimento e usa o mesmo modelo para criar uma implantação de produção, ou vice-versa. Para obter um melhor entendimento sobre como usar modelos, leia o [passo a passo do modelo](../resource-manager-template-walkthrough.md) que explica cada etapa de criação de um modelo JSON.

Há duas abordagens diferentes que você pode adotar ao projetar seu ambiente com Grupos de Recursos:

- Grupos de Recursos para cada implantação de aplicativo que combina as contas de armazenamento, as redes virtuais e sub-redes, as VMs, os balanceadores de carga, etc.
- Grupos de Recursos centralizados que contêm sua rede virtual principal e sub-redes ou contas de armazenamento, com seus aplicativos em seus próprios Grupos de Recursos que contêm apenas VMs, balanceadores de carga, interfaces de rede, etc.

Ao escalar horizontalmente, a criação de Grupos de Recursos centralizados para sua rede virtual e sub-redes facilita a criação de conexões de rede entre instalações para opções de conectividade híbrida, em vez de cada aplicativo ter sua própria rede virtual que exige configuração e manutenção. Os [Controles de Acesso Baseado em Função](../active-directory/role-based-access-control-what-is.md) fornecem uma maneira granular de controlar o acesso aos Grupos de Recursos. Para aplicativos de produção, é possível controlar os usuários que podem acessar esses recursos ou para os recursos de infraestrutura básica, você pode limitar apenas os engenheiros de infraestrutura a trabalhar com eles. Os proprietários de aplicativos terão acesso apenas aos componentes do aplicativo em seus Grupos de Recursos e não à infraestrutura básica do Azure de seu ambiente. Durante o projeto de seu ambiente, considere os usuários que precisam acessar os recursos e projete os Grupos de Recursos de forma condizente.


## Próximas etapas

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0629_2016-->