<properties
	pageTitle="Criar farms de servidores do SharePoint | Microsoft Azure"
	description="Criar rapidamente um novo farm do SharePoint 2013 ou SharePoint 2016 no Azure."
	services="virtual-machines-windows"
	documentationCenter=""
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
	ms.date="08/08/2016"
	ms.author="josephd"/>

# Criar farm do SharePoint Server

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modelo clássico.

## Farms do SharePoint 2013

Com o Marketplace do portal do Microsoft Azure, você pode criar rapidamente farms pré-configurados do SharePoint Server 2013. Isso pode economizar muito tempo quando você precisar de um farm do SharePoint básico ou de alta disponibilidade para um ambiente de desenvolvimento/teste, ou se estiver avaliando o SharePoint Server 2013 como uma solução de colaboração para a sua organização.

> [AZURE.NOTE] O item **Farm do SharePoint Server** no Azure Marketplace do portal do Azure foi removido. Ele foi substituído pelos itens **Farm de não HA do SharePoint 2013** e **Farm HA do SharePoint 2013**.

O farm do SharePoint básico consiste em três máquinas virtuais nesta configuração.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

É possível usar esta configuração de farm para uma configuração simplificada para o desenvolvimento do aplicativo SharePoint na sua primeira avaliação do SharePoint 2013.

Para criar o farm básico (três servidores) do SharePoint:

1. Clique [aqui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Clique em **Implantar**.
3. No painel **Farm de não HA do SharePoint 2013**, clique em **Criar**.
4. Especifique as configurações nas etapas do painel **Criar farm não HA do SharePoint 2013** e clique em **Criar**.

O farm do SharePoint de alta disponibilidade consiste em nove máquinas virtuais nesta configuração.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

É possível usar essa configuração de farm para testar cargas de clientes maiores, alta disponibilidade do site externo do SharePoint e Grupos de Disponibilidade AlwaysOn do SQL Server para um farm do SharePoint. Também é possível usar esta configuração para a implementação do aplicativo SharePoint em um ambiente de alta disponibilidade.

Para criar o farm de alta disponibilidade (nove servidores) do SharePoint:

1. Clique [aqui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Clique em **Implantar**.
3. No painel **Farm de HA do SharePoint 2013**, clique em **Criar**.
4. Especifique as configurações nas sete etapas do painel **Criar Farm de HA do SharePoint 2013** e, então, clique em **Criar**.

> [AZURE.NOTE] Não é possível criar o **Farm de não HA do SharePoint 2013** ou o **Farm de HA do SharePoint 2013** com uma Avaliação Gratuita do Azure.

O portal do Azure cria ambos os farms em uma rede virtual somente em nuvem com a presença da web voltada para a Internet. Não há nenhuma conexão de VPN site a site ou de Rota Expressa para a rede da sua organização.

## Farms do SharePoint 2016

Confira [este artigo](https://technet.microsoft.com/library/mt723354.aspx) para obter instruções sobre como criar o farm a seguir de servidor único do SharePoint 2016.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## Gerenciando os farms do SharePoint

É possível administrar os servidores desses farms por meio de conexões da Área de Trabalho Remota. Para saber mais, veja [Fazer logon na máquina virtual](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine).

No site Administração central do SharePoint, é possível configurar o My sites, os aplicativos SharePoint e outra funcionalidade. Para saber mais, confira [Configurar o SharePoint](http://technet.microsoft.com/library/ee836142.aspx).

## Próximas etapas

- Descubra as [configurações do SharePoint](https://technet.microsoft.com/library/dn635309.aspx) adicionais nos serviços de infraestrutura do Azure.

<!---HONumber=AcomDC_0810_2016-->