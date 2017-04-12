---
title: Criar farms de servidores do SharePoint no Azure | Microsoft Docs
description: Crie rapidamente um novo farm do SharePoint 2013 ou SharePoint 2016 no Azure usando o Marketplace no portal do Azure.
services: virtual-machines-windows
documentationcenter: 
author: JoeDavies-MSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 89b124da-019d-4179-86dd-ad418d05a4f2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: josephd
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 00648ee35962b22fb7eeceaa6d9df7305c801abf
ms.lasthandoff: 03/31/2017


---
# <a name="create-sharepoint-server-farms-using-the-azure-portal-marketplace"></a>Criar farms de servidores do SharePoint usando o Marketplace no portal do Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="sharepoint-2013-farms"></a>Farms do SharePoint 2013
Com o Marketplace do portal do Microsoft Azure, você pode criar rapidamente farms pré-configurados do SharePoint Server 2013. Isso pode economizar muito tempo quando você precisar de um farm do SharePoint básico ou de alta disponibilidade para um ambiente de desenvolvimento/teste, ou se estiver avaliando o SharePoint Server 2013 como uma solução de colaboração para a sua organização.

> [!NOTE]
> O item **Farm do SharePoint Server** no Azure Marketplace do portal do Azure foi removido. Ele foi substituído pelos itens **Farm de não HA do SharePoint 2013** e **Farm HA do SharePoint 2013**.
>
>

O farm do SharePoint básico consiste em três máquinas virtuais nesta configuração.

![sharepointfarm](./media/sharepoint-farm/Non-HAFarm.png)

É possível usar esta configuração de farm para uma configuração simplificada para o desenvolvimento do aplicativo SharePoint na sua primeira avaliação do SharePoint 2013.

Para criar o farm básico (três servidores) do SharePoint:

1. Clique [aqui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Clique em **Implantar**.
3. No painel **Farm de não HA do SharePoint 2013**, clique em **Criar**.
4. Especifique as configurações nas etapas do painel **Criar farm não HA do SharePoint 2013** e clique em **Criar**.

O farm do SharePoint de alta disponibilidade consiste em nove máquinas virtuais nesta configuração.

![sharepointfarm](./media/sharepoint-farm/HAFarm.png)

É possível usar essa configuração de farm para testar cargas de clientes maiores, alta disponibilidade do site externo do SharePoint e Grupos de Disponibilidade AlwaysOn do SQL Server para um farm do SharePoint. Também é possível usar esta configuração para a implementação do aplicativo SharePoint em um ambiente de alta disponibilidade.

Para criar o farm de alta disponibilidade (nove servidores) do SharePoint:

1. Clique [aqui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Clique em **Implantar**.
3. No painel **Farm de HA do SharePoint 2013**, clique em **Criar**.
4. Especifique as configurações nas sete etapas do painel **Criar Farm de HA do SharePoint 2013** e, então, clique em **Criar**.

> [!NOTE]
> Não é possível criar o **Farm de não HA do SharePoint 2013** ou o **Farm de HA do SharePoint 2013** com uma Avaliação Gratuita do Azure.
>
>

O portal do Azure cria ambos os farms em uma rede virtual somente em nuvem com a presença da web voltada para a Internet. Não há nenhuma conexão de VPN site a site ou de Rota Expressa para a rede da sua organização.

> [!NOTE]
> Quando você cria farms básicos ou de alta disponibilidade do SharePoint usando o portal do Azure, não é possível especificar um grupo de recursos existente. Para contornar essa limitação, crie esses farms com o Azure PowerShell. Para obter mais informações, consulte [Criar farms de desenvolvimento/teste do SharePoint 2013 com o Azure PowerShell](https://technet.microsoft.com/library/mt743093.aspx#powershell).
>
>

## <a name="sharepoint-2016-farms"></a>Farms do SharePoint 2016
Confira [este artigo](https://technet.microsoft.com/library/mt723354.aspx) para obter instruções sobre como criar o seguinte farm de servidor único do SharePoint Server 2016.

![sharepointfarm](./media/sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>Gerenciando os farms do SharePoint
É possível administrar os servidores desses farms por meio de conexões da Área de Trabalho Remota. Para saber mais, veja [Fazer logon na máquina virtual](quick-create-portal.md#connect-to-virtual-machine).

No site Administração central do SharePoint, é possível configurar o My sites, os aplicativos SharePoint e outra funcionalidade. Para saber mais, confira [Configurar o SharePoint](http://technet.microsoft.com/library/ee836142.aspx).

## <a name="next-steps"></a>Próximas etapas
* Descubra as [configurações do SharePoint](https://technet.microsoft.com/library/dn635309.aspx) adicionais nos serviços de infraestrutura do Azure.

