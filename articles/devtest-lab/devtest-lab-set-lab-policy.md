<properties
	pageTitle="Definir políticas de laboratório no Azure DevTest Labs| Microsoft Azure"
	description="Aprenda a definir as políticas do laboratório, como os tamanhos das VMs, o número máximo de VMs por usuário e o desligamento automático."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="tarcher"/>

# Definir políticas de laboratório no Azure DevTest Labs

> [AZURE.VIDEO how-to-set-vm-policies-in-a-devtest-lab]

O Azure DevTest Labs permite que você especifique as políticas principais que ajudam a controlar os custos e a minimizar o desperdício nos laboratórios. Essas políticas de laboratório incluem o número máximo de VMs criadas por usuário e por laboratório e várias opções de início e desligamento automáticos.

## Acessar as políticas de laboratório no Azure DevTest Labs

As etapas a seguir orientam você durante a definição de políticas para um laboratório no Azure DevTest Labs:

Para exibir (e alterar) as políticas de um laboratório, siga estas etapas:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Mais Serviços** e selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.

1. Selecione **Configurações de política**.

1. A folha **Configurações de política** contém um menu de configurações que você pode especificar:

	![Folha de configurações de política](./media/devtest-lab-set-lab-policy/policies.png)

	Para saber mais sobre como configurar uma política, selecione-a na lista a seguir:

	- [Tamanhos de máquina virtual permitidos](#set-allowed-virtual-machine-sizes) – selecione a lista de tamanhos de VM permitidos no laboratório. O usuário só pode criar VMs a partir dessa lista.

	- [Máquinas virtuais por usuário](#set-virtual-machines-per-user) – especifique o número máximo de VMs que podem ser criadas por um usuário.

	- [Máquinas virtuais por laboratório](#set-virtual-machines-per-lab) – especifique o número máximo de VMs que podem ser criadas para um laboratório.

	- [Desligamento automático](#set-auto-shutdown) – especifique a hora em que as VMs de um laboratório atual serão desligadas automaticamente.

	- [Início automático](#set-auto-start) – especifique a hora em que as VMs de um laboratório atual serão iniciadas automaticamente.

## Definir tamanhos de máquina virtual permitidos

A política para definir os tamanhos de VM permitidos ajuda a minimizar o desperdício de laboratório, permitindo que você especifique quais tamanhos de VM são permitidos no laboratório. Quando essa política é ativada, somente os tamanhos de VM nesta lista podem ser usados para criar VMs.

1. Na folha **Configurações de política**, selecione **Tamanhos de máquinas virtuais permitidos**.

	![Tamanhos de máquinas virtuais permitidos](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
 
1. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.

1. Se você habilitar essa política, selecione um ou mais tamanhos de VM que podem ser criados no laboratório.

1. Selecione **Salvar**.

## Conjunto de máquinas virtuais por usuário

A política de **Máquinas virtuais por usuário** permite que você especifique o número máximo de VMs que podem ser criadas por um usuário individual. Se um usuário tentar criar uma VM quando o limite de usuários for atingido, uma mensagem de erro indicará que a VM não poderá ser criada.

1. Na folha **Configurações de política**, selecione **Máquinas virtuais por usuário**.

	![Máquinas virtuais por usuário](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.

1. Se você habilitar essa política, digite um valor numérico indicando o número máximo de VMs que podem ser criadas por um usuário. Se você digitar um número que não é válido, a interface do usuário exibirá o número máximo permitido para esse campo.

1. Selecione **Salvar**.

## Conjunto de máquinas virtuais por laboratório

A política de **Máquinas virtuais por laboratório** permite que você especifique o número máximo de VMs que podem ser criadas para o laboratório atual. Se um usuário tentar criar uma VM quando o limite de laboratórios for alcançado, uma mensagem de erro indicará que a VM não pode ser criada.

1. Na folha **Configurações de política**, selecione **Máquinas virtuais por laboratório**.

	![Máquinas virtuais por laboratório](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)

1. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.

1. Se você habilitar essa política, digite um valor numérico indicando o número máximo de VMs que podem ser criadas para o laboratório atual. Se você digitar um número que não é válido, a interface do usuário exibirá o número máximo permitido para esse campo.

1. Selecione **Salvar**.

## Definir desligamento automático

A política de desligamento automático ajuda a minimizar o desperdício de laboratório, permitindo que você especifique a hora em que as VMs desse laboratório serão desligadas.

1. Na folha **Configurações de política**, selecione **Desligamento automático**.

	![Desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.

1. Se você habilitar essa política, especifique o horário local para desligar todas as VMs do laboratório atual.

1. Selecione **Salvar**.

1. Por padrão, uma vez habilitada, essa política se aplicará a todas as VMs do laboratório atual. Para remover essa configuração de uma VM específica, abra a folha da VM e altere sua configuração de **Desligamento Automático**

## Definir início automático

A política de início automático permite que você especifique quando as VMs do laboratório atual deverão ser iniciadas.

1. Na folha **Configurações de política**, selecione **Início automático**.

	![Início automático](./media/devtest-lab-set-lab-policy/auto-start.png)

1. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.

1. Se você habilitar esta política, especifique o horário local de início agendado e os dias da semana para os quais o horário se aplica.

1. Selecione **Salvar**.

1. Quando habilitada, essa política não será aplicada automaticamente a quaisquer máquinas virtuais do laboratório atual. Para aplicar essa configuração a uma VM específica, abra a folha da VM e altere sua configuração de **Início automático**

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Próximas etapas

Depois de definir e aplicar as várias configurações da política de VM em seu laboratório, aqui estão algumas opções para você tentar em seguida:

- [Configurar gerenciamento de custos](./devtest-lab-configure-cost-management.md) — Ilustra como usar o gráfico **Tendência de custo estimado mensal** para exibir o custo estimado até a data do mês atual e o custo projetado do final do mês.
- [Criar imagem personalizada](./devtest-lab-create-template.md) — durante a criação de uma VM, você especifica uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um arquivo VHD.
- [Configurar imagens do Marketplace](./devtest-lab-configure-marketplace-images.md) — Os Azure DevTest Labs dão suporte à criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais imagens (caso haja alguma) do Azure Marketplace podem ser usadas durante a criação de VMs em um laboratório.
- [Criar uma VM em um laboratório](./devtest-lab-add-vm-with-artifacts.md) — ilustra como criar uma VM de uma imagem base (personalizada ou do Marketplace) e como trabalhar com artefatos em sua VM.

<!---HONumber=AcomDC_0914_2016-->