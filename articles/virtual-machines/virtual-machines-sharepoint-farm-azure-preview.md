<properties
	pageTitle="Criar farms de servidores do SharePoint | Microsoft Azure"
	description="Crie rapidamente um novo farm básico ou altamente disponível do SharePoint Server 2013 usando o Marketplace do portal de Visualização do Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="josephd"/>

# Criar farm do SharePoint Server

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo clássico.

Com o Marketplace do portal de Visualização do Microsoft Azure, você pode criar rapidamente farms pré-configurados do SharePoint Server 2013. Isso pode economizar muito tempo quando necessitar de um farm SharePoint básico ou de alta disponibilidade para um ambiente de desenvolvimento e teste, ou se estiver avaliando o SharePoint Server 2013 como uma solução de colaboração para a sua organização.

> [AZURE.NOTE]O item **Farm do SharePoint Server** no Azure Marketplace do portal de Visualização do Azure foi removido.

O farm do SharePoint básico consiste em três máquinas virtuais nesta configuração.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/Non-HAFarm.png)

É possível usar esta configuração de farm para uma configuração simplificada para o desenvolvimento do aplicativo SharePoint na sua primeira avaliação do SharePoint 2013.

Para criar o farm básico (três servidores) do SharePoint:

1. Clique [aqui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Clique em **Implantar**.
3. No painel **Farm de não HA do SharePoint 2013**, clique em **Criar**.
4. Especifique as configurações nas sete etapas do painel **Criar Farm de não HA do SharePoint 2013** e clique em **Criar**.

O farm do SharePoint de alta disponibilidade consiste em nove máquinas virtuais nesta configuração.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/HAFarm.png)

É possível usar esta configuração de farm para testar cargas de clientes maiores, alta disponibilidade do site externo do SharePoint e o SQL Server AlwaysOn para um farm do SharePoint. Também é possível usar esta configuração para a implementação do aplicativo SharePoint em um ambiente de alta disponibilidade.

Para criar o farm de alta disponibilidade (nove servidores) do SharePoint:

1. Clique [aqui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Clique em **Implantar**.
3. No painel **Farm de HA do SharePoint 2013**, clique em **Criar**.
4. Especifique as configurações nas sete etapas do painel **Criar Farm de HA do SharePoint 2013** e clique em **Criar**.

> [AZURE.NOTE]A Microsoft lançou o SharePoint Server 2016 IT Preview. Para facilitar a instalação e teste desta visualização, você pode usar uma imagem da galeria de máquinas virtuais do Azure com o SharePoint Server 2016 IT Preview e seus pré-requisitos pré-instalados. Para saber mais, veja [Testar o SharePoint Server 2016 IT Preview no Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Gerenciando os farms do SharePoint

É possível administrar os servidores desses farms por meio de conexões da Área de Trabalho Remota. Para saber mais, veja [Fazer logon na máquina virtual](virtual-machines-windows-tutorial.md#log-on-to-the-virtual-machine).

No site Administração central do SharePoint, é possível configurar o My sites, os aplicativos SharePoint e outra funcionalidade. Para obter mais informações, consulte [Configurar o SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx).

> [AZURE.NOTE]O Portal de visualização do Azure cria ambos os farms em uma rede virtual somente em nuvem com a presença da web voltada para a Internet. Não há nenhuma conexão de VPN site a site ou de Rota Expressa para a rede da sua organização.

## Recursos adicionais

[Farms do SharePoint hospedados nos serviços de infraestrutura do Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Arquiteturas do Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

<!---HONumber=Nov15_HO4-->