<properties
	pageTitle="Implantar um farm do SharePoint Server 2013 | Microsoft Azure"
	description="Implante um farm do SharePoint Server 2013 de alta disponibilidade usando grupos de disponibilidade do AlwaysOn do SQL Server no Azure em cinco fases."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/25/2016"
	ms.author="josephd"/>

# Implantando o SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

Este tópico contém links para instruções passo a passo para implantar um farm do SharePoint 2013 somente para intranet com Grupos de Disponibilidade AlwaysOn do SQL Server. O farm contém estes computadores:

- Dois servidores Web do SharePoint
- Dois servidores de aplicativos do SharePoint
- Dois servidores de banco de dados
- Servidores de nós principais de um cluster
- Dois controladores de domínio

Essa é a configuração, com nomes de espaço reservado para cada servidor.

![](./media/virtual-machines-windows-sp-intranet-overview/workload-spsqlao_05.png)

Duas máquinas para cada função asseguram a alta disponibilidade. Todas as máquinas virtuais estão em uma só região. Cada grupo de máquinas virtuais para uma função específica está em seu próprio conjunto de disponibilidade.

## Lista de materiais

Essa configuração de linha de base exige o seguinte conjunto de componentes e serviços do Azure:

- Nove máquinas virtuais.
- Quatro discos de dados extras para os controladores de domínio e servidores SQL.
- Quatro conjuntos de disponibilidade.
- Uma rede virtual entre locais.
- Uma conta de armazenamento.
- Uma assinatura do Azure.

Aqui estão as máquinas virtuais e seus tamanhos padrão para essa configuração.

Item | Descrição da máquina virtual | Imagem da galeria | Tamanho padrão
--- | --- | --- | ---
1\. | Primeiro controlador de domínio | Windows Server 2012 R2 Datacenter | A2
2\. | Segundo controlador de domínio | Windows Server 2012 R2 Datacenter | A2
3\. | Primeiro servidor de banco de dados | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
4\. | Segundo servidor de banco de dados | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
5\. | Nó principal para o cluster | Windows Server 2012 R2 Datacenter | A1
6\. | Primeiro servidor de aplicativos do SharePoint | Avaliação do Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4
7\. | Segundo servidor de aplicativos do SharePoint | Avaliação do Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4
8\. | Primeiro servidor Web do SharePoint | Avaliação do Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4
9\. | Segundo servidor Web do SharePoint | Avaliação do Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4

Para calcular os custos estimados para essa configuração, confira a [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/).

1. Em **Módulos**, clique em **Computação**, e, em seguida, clique em **Máquinas Virtuais** vezes suficientes para criar uma lista de nove máquinas virtuais.
2. Para cada máquina virtual, selecione:
	- A região desejada
	- **Windows** para o tipo
	- **Standard** para o tipo de preço
	- O tamanho padrão da tabela anterior ou o tamanho desejado para o **Tamanho da instância**

> [AZURE.NOTE] A Calculadora de preços do Azure não inclui os custos adicionais para a licença do SQL Server para as duas máquinas virtuais executando o SQL Server 2014 Enterprise. Consulte [Preços das máquinas virtuais - SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql) para obter mais informações.

## Fases da implantação

Implante essa configuração nas seguintes fases:

- [Fase 1: configurar o Azure](virtual-machines-windows-ps-sp-intranet-ph1.md). Crie uma conta de armazenamento, conjuntos de disponibilidade e uma rede virtual entre os locais.
- [Fase 2: configurar os controladores de domínio](virtual-machines-windows-ps-sp-intranet-ph2.md). Crie e configure os controladores de domínio dos Serviços de Domínio do Active Directory (AD DS).
- [Fase 3: configurar a infraestrutura do SQL Server](virtual-machines-windows-ps-sp-intranet-ph3.md). Crie e configure as máquinas virtuais do SQL Server, prepare-as para uso com o SharePoint e crie o cluster.
- [Fase 4: configurar os servidores do SharePoint](virtual-machines-windows-ps-sp-intranet-ph4.md). Crie e configure as quatro máquinas virtuais do SharePoint.
- [Fase 5: criar o grupo de disponibilidade e adicionar os bancos de dados do SharePoint](virtual-machines-windows-ps-sp-intranet-ph5.md). Prepare os bancos de dados e crie um grupos de disponibilidade AlwaysOn do SQL Server.


Esta implantação do SharePoint com o SQL Server AlwaysOn foi projetada para acompanhar o [Infográfico do SharePoint com SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788) e incorporar as recomendações mais recentes.

Essa configuração é um guia prescritivo de cada fase cujo objetivo é desenvolver uma arquitetura predefinida para criar um farm do SharePoint de intranet funcional e altamente disponível nos serviços de infraestrutura do Azure. Para obter mais orientações de arquitetura sobre a implementação do SharePoint 2013 no Azure, consulte [Arquiteturas do Microsoft Azure para o SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx).

Lembre-se:

- Se você tiver experiência com o SharePoint, fique à vontade para adaptar as instruções das Fases 3 a 5 e criar o farm da maneira que melhor atenda às suas necessidades.
- Se você já tiver uma implantação de nuvem híbrida do Azure, fique à vontade para adaptar ou ignorar as instruções das Fases 1 e 2 para hospedar o novo farm do SharePoint na sub-rede apropriada.
- Todos os servidores estão localizados em uma só sub-rede da rede virtual do Azure. Se você quiser fornecer segurança adicional equivalente ao isolamento de sub-rede, use os [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

Para criar um ambiente de desenvolvimento/teste ou uma verificação de conceito dessa configuração, consulte [Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md).

Para saber mais sobre o SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server, consulte [Configurar Grupos de Disponibilidade AlwaysOn do SQL Server 2012 para o SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## Próxima etapa

- Inicie a configuração desta carga de trabalho com [Fase 1](virtual-machines-windows-ps-sp-intranet-ph1.md).

<!---HONumber=AcomDC_0427_2016-->