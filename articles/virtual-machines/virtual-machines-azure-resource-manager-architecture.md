<properties
   pageTitle="Arquitetura do Gerenciador de Recursos do Azure"
   description="Saiba mais sobre a arquitetura do Gerenciador de Recursos e as relações entre provedores de recursos de computação, rede e armazenamento."
   services="virtual-machines"
   documentationCenter=""
   authors="davidmu1"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2015"
	ms.author="davidmu"/>

# Arquitetura do Gerenciador de Recursos do Azure

Este artigo fornece uma visão geral de ambas as arquiteturas de Gerenciamento de serviços e Gerenciador de Recursos para a criação de aplicativos baseados em infraestrutura e cargas de trabalho.

## Arquitetura do Gerenciamento de serviços

Antes de discutir a arquitetura do Gerenciador de Recursos do Azure e os vários provedores de recursos, vamos analisar a arquitetura que existe atualmente para o gerenciamento de serviços. No Gerenciamento de Serviços do Azure, os recursos de computação, armazenamento ou rede para hospedar máquinas virtuais são fornecidos por:

- Um serviço de nuvem necessário que atua como contêiner para hospedar máquinas virtuais (computação). Máquinas virtuais são fornecidas automaticamente com uma placa de interface de rede (NIC) e um endereço IP atribuído pelo Azure. Além disso, o serviço de nuvem contém uma instância do balanceador externo de carga, um endereço IP público e pontos de extremidade padrão para permitir o tráfego do PowerShell remoto e de área de trabalho remota para máquinas virtuais baseadas em Windows e tráfego Secure Shell (SSH) para máquinas virtuais baseadas em Linux.
- Uma conta de armazenamento necessária que armazena os VHDs em uma máquina virtual, incluindo sistema operacional e discos de dados temporários e adicionais (armazenamento).
- Uma rede virtual opcional que atua como um contêiner adicional no qual você pode criar uma estrutura de sub-redes e designar a sub-rede na qual a máquina virtual está localizada (rede).

Aqui estão os componentes e suas relações para o Gerenciamento de Serviços do Azure.

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Arquitetura para o Gerenciador de Recursos

Para o Gerenciador de Recursos do Azure, os provedores de recursos oferecem suporte a recursos individuais para criar as máquinas virtuais funcionando na configuração de que você precisa. Para máquinas virtuais, há três provedores de recursos principais:

- Provedor de recursos de computação (CRP): dá suporte a instâncias de máquinas virtuais e conjuntos de disponibilidade opcionais.
- Provedor de recursos de armazenamento (SRP): dá suporte para contas de armazenamento necessárias que armazenam os VHDs para máquinas virtuais, incluindo o sistema operacional e discos de dados adicionais.
- Provedor de recursos de rede (NRP): dá suporte para NICs necessárias, endereços IP de máquinas virtuais e sub-redes em redes virtuais e balanceadores de carga opcionais, endereços IP de balanceador de carga e grupos de segurança de rede.

Além disso, há relações entre os recursos dentro de provedores de recursos:

- Uma máquina virtual depende de uma conta de armazenamento específica definida no SRP para armazenar seus discos no armazenamento de blob (obrigatório).
- Uma máquina virtual faz referência a uma NIC específica definida no NRP (obrigatório) e um conjunto de disponibilidade definido no CRP (opcional).
- Uma NIC faz referência ao endereço IP atribuído à máquina virtual (obrigatório), à sub-rede da rede virtual para a máquina virtual (obrigatório) e a um grupo de segurança de rede (opcional).
- Uma sub-rede em uma rede virtual faz referência a um grupo de segurança de rede (opcional).
- Uma instância do balanceador de carga faz referência ao pool de back-end dos endereços IP que incluem a NIC de uma máquina virtual (opcional) e faz referência a um endereço IP público ou privado de um balanceador de carga (opcional).

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch2.png)

A componentização de recursos permite mais flexibilidade ao configurar a infraestrutura para uma carga de trabalho de TI hospedada no Azure. Modelos do Gerenciador de Recursos do Azure aproveitam essa flexibilidade para criar o conjunto de recursos dependentes necessários para uma configuração específica. Ao executar um modelo, o Gerenciador de Recursos garante que os recursos para uma configuração sejam criados na ordem correta para preservar as dependências e referências. Por exemplo, o Gerenciador de Recursos não criará a NIC para uma máquina virtual até que crie a rede virtual em uma sub-rede e um endereço IP (um grupo de segurança de rede é opcional).

Um grupo de recursos é um contêiner lógico que contém recursos relacionados para um aplicativo, que pode consistir em várias máquinas virtuais, NICs, endereços IP, balanceadores de carga, sub-redes e grupos de segurança de rede. Por exemplo, você pode gerenciar todos os recursos do aplicativo como uma unidade única de gerenciamento. Você pode criar, atualizar e excluir todos eles juntos. Aqui está um exemplo de aplicativo implantado em um grupo de recursos único.

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Esse aplicativo consiste em:

- Duas máquinas virtuais que usam a mesma conta de armazenamento estão no mesmo conjunto de disponibilidade e na mesma sub-rede de uma rede virtual.
- Um único endereço IP da VM e NIC para cada máquina virtual.
- Um balanceador externo de carga que distribui o tráfego de Internet para as NICs de duas máquinas virtuais.

Todos esses recursos do aplicativo são gerenciados por meio do grupo de recursos único que os contém.

Você também pode ver a componentização e os relacionamentos dependentes entre recursos quando cria uma máquina virtual baseada no Gerenciador de Recursos usando o PowerShell do Azure ou a CLI do Azure. Antes de executar o comando que cria a máquina virtual, você deve criar um grupo de recursos, uma conta de armazenamento, uma rede virtual com uma sub-rede e uma NIC com um endereço IP. Para obter mais informações, consulte [Criar e pré-configurar uma máquina virtual do Windows com o Gerenciador de Recursos e o PowerShell do Azure](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md).

## Próximas etapas

[Implantar e gerenciar Máquinas Virtuais usando modelos de Gerenciador de Recursos do Azure e a CLI do Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Implantar e gerenciar máquinas virtuais do Azure usando modelos de Gerenciador de Recursos e o PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

## Recursos adicionais

[Computação do Azure, Provedores de Rede e Armazenamento no Gerenciador de Recursos do Azure](virtual-machines-azurerm-versus-azuresm.md)

[Visão Geral do Gerenciador de Recursos do Azure](resource-group-overview.md)

<!---HONumber=July15_HO4-->