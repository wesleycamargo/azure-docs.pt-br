<properties 
	pageTitle="Implantar um aplicativo de linha de negócios | Microsoft Azure" 
	description="Implante um aplicativo de linha de negócios baseado na Web e de alta disponibilidade com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure em cinco fases." 
	documentationCenter=""
	services="virtual-machines-windows" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/08/2016" 
	ms.author="josephd"/>

# Implantar um aplicativo de linha de negócios de alta disponibilidade no Azure

Este artigo contém links para as instruções passo a passo para implantar um aplicativo de linha de negócios de alta disponibilidade, apenas para intranet e baseado na Web com Grupos de Disponibilidade AlwaysOn do SQL Server nos serviços de infraestrutura do Azure. O aplicativo está hospedado nesses computadores:

- Dois servidores Web
- Dois servidores de banco de dados
- Servidores de nós principais de um cluster
- Dois controladores de domínio

Essa é a configuração, com nomes de espaço reservado para cada servidor.

![](./media/virtual-machines-windows-lob-overview/workload-lobapp-phase4.png)
 
Pelo menos duas máquinas para cada função asseguram a alta disponibilidade. Todas as máquinas virtuais estão em um único local do Azure (também conhecido como região). Cada grupo de máquinas virtuais para uma função específica está em seu próprio conjunto de disponibilidade.

## Lista de materiais

Essa configuração de linha de base exige o seguinte conjunto de componentes e serviços do Azure:

- Sete máquinas virtuais
- Quatro discos de dados extras para controladores de domínio e máquinas virtuais que executam o SQL Server
- Três conjuntos de disponibilidade
- Uma rede virtual entre locais
- Duas contas de armazenamento

Aqui estão as máquinas virtuais e seus tamanhos padrão para essa configuração.

Item | Descrição da máquina virtual | Imagem da galeria | Tamanho padrão 
--- | --- | --- | --- 
1\. | Primeiro controlador de domínio | Windows Server 2012 R2 Datacenter | D1
2\. | Segundo controlador de domínio | Windows Server 2012 R2 Datacenter | D1
3\. | Servidor de banco de dados primário | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
4\. | Servidor de banco de dados secundário | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
5\. | Nó principal para o cluster | Windows Server 2012 R2 Datacenter | D1
6\. | Primeiro servidor Web | Windows Server 2012 R2 Datacenter | D3
7\. | Segundo servidor Web | Windows Server 2012 R2 Datacenter | D3

Para calcular os custos estimados para essa configuração, confira a [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/).

1. Em **Módulos**, clique em **Computação**, e, em seguida, clique em **Máquinas Virtuais** vezes suficientes para criar uma lista de sete máquinas virtuais.
2. Para cada máquina virtual, selecione:
	- A região desejada
	- **Windows** para o tipo
	- **Standard** para o tipo de preço
	- O tamanho padrão da tabela anterior ou o tamanho desejado para o **Tamanho da instância**

> [AZURE.NOTE] A Calculadora de preços do Azure não inclui os custos adicionais para a licença do SQL Server para as duas máquinas virtuais executando o SQL Server 2014 Enterprise. Consulte [Preços das máquinas virtuais - SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql) para obter mais informações.

## Fases da implantação

Implante essa configuração nas seguintes fases:

- [Fase 1: configurar o Azure](virtual-machines-windows-ps-lob-ph1.md). Crie uma conta de armazenamento, conjuntos de disponibilidade e uma rede virtual entre os locais.
- [Fase 2: configurar os controladores de domínio](virtual-machines-windows-ps-lob-ph2.md). Crie e configure os controladores de domínio dos Serviços de Domínio do Active Directory (AD DS).
- [Fase 3: configurar a infraestrutura do SQL Server](virtual-machines-windows-ps-lob-ph3.md). Crie e configure as máquinas virtuais que executam o SQL Server, crie o cluster e habilite os Grupos de Disponibilidade AlwaysOn do SQL Server.
- [Fase 4: configurar os servidores Web](virtual-machines-windows-ps-lob-ph4.md). Crie e configure as duas máquinas virtuais do servidor Web.
- [Fase 5: Adicionar os bancos de dados de aplicativo a um Grupo de Disponibilidade do SQL Server Always On](virtual-machines-windows-ps-lob-ph5.md). Prepare os bancos de dados do aplicativo de linha de negócios e adicione-os a um Grupo de Disponibilidade AlwaysOn do SQL Server.

Essa implantação foi projetada para acompanhar o [Plano gráfico da arquitetura de aplicativos de linha de negócios](http://msdn.microsoft.com/dn630664) e incorporar as recomendações mais recentes.

Esta é uma arquitetura prescritiva predefinida. Lembre-se:

- Se você for um implementador experiente de aplicativo de linha de negócios baseado na Web, fique à vontade para adaptar as instruções das fases 3 a 5 e criar a infraestrutura de aplicativos que melhor atenda às suas necessidades. 
- Se você já tiver uma implementação de nuvem híbrida do Azure, fique à vontade adaptar ou ignorar as instruções das fases 1 e 2 para hospedar as máquinas virtuais para o novo aplicativo na sub-rede apropriada.
- Todos os servidores estão localizados em uma só sub-rede da rede virtual do Azure. Se você quiser fornecer segurança adicional equivalente ao isolamento de sub-rede, é possível usar [Grupos de Segurança de Rede](../virtual-network/virtual-networks-nsg.md).

Para criar um ambiente de desenvolvimento/teste ou uma verificação de conceito desta configuração, consulte [Configurar um aplicativo LOB baseado na Web em uma nuvem híbrida para teste](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md).

Para obter informações adicionais sobre a criação de cargas de trabalho de TI para o Azure, consulte [Diretrizes de implementação de serviços de infraestrutura do Azure](virtual-machines-linux-infrastructure-service-guidelines.md).

## Próxima etapa

- Inicie a configuração desta carga de trabalho com [Fase 1](virtual-machines-windows-ps-lob-ph1.md).

<!---HONumber=AcomDC_0601_2016-->