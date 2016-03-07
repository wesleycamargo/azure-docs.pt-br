<properties
	pageTitle="Definir políticas de laboratório | Microsoft Azure"
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
	ms.date="02/18/2016"
	ms.author="tarcher"/>

# Definir políticas de laboratório

> [AZURE.NOTE] Clique no link a seguir para exibir o vídeo que acompanha este artigo: [como definir criar artefatos personalizados](/documentation/videos/how-to-set-vm-policies-in-a-devtest-lab)

## Visão geral

Com o Laboratório de Desenvolvimento/Testes, você pode especificar as principais políticas que controlam como o seu laboratório e suas VMs são usados. Essas políticas incluem limites de custo, tamanhos permitidos de VM, número máximo de VMs por usuário e um desligamento automático para as VMs no seu laboratório.

## Acessar as políticas do laboratório

Para exibir (e alterar) as políticas de um laboratório, siga estas etapas:

1. Entre no [Portal de visualização do Azure](https://portal.azure.com).

1. Toque em **Procurar** e toque em **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, toque no laboratório desejado.

1. Toque em **Configurações**.

	![Configurações](./media/devtest-lab-set-lab-policy/lab-blade-settings.png)

1. Na folha **Configurações**, existe um agrupamento de configurações denominado **Políticas**.

	![Configurações](./media/devtest-lab-set-lab-policy/policies.png)

	Toque na política desejada na lista abaixo para saber mais sobre como configurá-la:

	- Limites de custo: atualmente não existe suporte a essa política.

	- [Tamanhos de VM permitidos](#set-allowed-vm-sizes): selecione a lista de tamanhos de VM permitidos no laboratório. O usuário só pode criar VMs a partir dessa lista.

	- [Número máximo de VMs](#set-maximum-vms): especifique o número máximo de VMs que podem ser criadas para um laboratório, bem como o número máximo de VMs que podem ser criadas por um usuário.

	- [Desligamento automático](#set-auto-shutdown): especifique a hora em que as VMs do laboratório atual devem ser desligadas.

## Definir os tamanhos de VM permitidos

A política para definir os tamanhos de VM permitidos ajuda a minimizar o desperdício de laboratório, permitindo que você especifique quais tamanhos de VM são permitidos no laboratório. Quando essa política é ativada, somente os tamanhos de VM nesta lista podem ser usados para criar VMs.

1. Na folha **Configurações** do laboratório, em **Políticas**, toque em **Tamanhos de VM Permitidos**.

	![Configurações](./media/devtest-lab-set-lab-policy/allowed-vm-sizes-policy.png)
 
1. Toque em **Ativar** para habilitar essa política e em **Desativar** para desabilitá-la.

1. Se você habilitar essa política, toque em um ou mais tamanhos de VM que podem ser criados no seu laboratório.

1. Toque em **Salvar**.

## Definir o número máximo de VMs

A política para o número máximo de VMs permite que você especifique o número máximo de VMs que podem ser criadas para o laboratório atual, bem como o número máximo de VMs que podem ser criadas por um usuário. Se um usuário tentar criar uma nova VM quando o limite de usuários ou o limite de laboratórios for alcançado, uma mensagem de erro indicará que a VM não pode ser criada.

1. Na folha **Configurações** do laboratório, em **Políticas**, toque em **Número Máximo de VMs**.

	![Configurações](./media/devtest-lab-set-lab-policy/max-vms-policies.png)

1. Na seção **Política por Usuário**:
 
	1. Toque em **Ativar** para habilitar essa política e em **Desativar** para desabilitá-la.
	
	1. Se você habilitar essa política, na caixa de texto **Número máximo de VMs permitido por usuário**, digite um valor numérico indicando o número máximo de VMs que podem ser criadas por um usuário. Se você digitar um número que não é válido, a interface do usuário exibirá o número máximo permitido para esse campo.

1. Na seção **Política por Laboratório**:
 
	1. Toque em **Ativar** para habilitar essa política e em **Desativar** para desabilitá-la.
	
	1. Se você habilitar essa política, na caixa de texto **Número máximo de VMs permitido nesse laboratório**, digite um valor numérico indicando o número máximo de VMs que podem ser criadas para o laboratório atual. Se você digitar um número que não é válido, a interface do usuário exibirá o número máximo permitido para esse campo.

1. Toque em **Salvar**.

## Definir o desligamento automático

A política de desligamento automático ajuda a minimizar o desperdício de laboratório, permitindo que você especifique a hora em que as VMs desse laboratório serão desligadas.

1. Na folha **Configurações** do laboratório, em **Políticas**, toque em **Desligamento Automático**.

	![Configurações](./media/devtest-lab-set-lab-policy/auto-shutdown-policy.png)

1. Toque em **Ativar** para habilitar essa política e em **Desativar** para desabilitá-la.

1. Se você habilitar essa política, especifique uma hora para desligar todas as VMs do laboratório atual.

1. Toque em **Salvar**.

<!---HONumber=AcomDC_0224_2016-->