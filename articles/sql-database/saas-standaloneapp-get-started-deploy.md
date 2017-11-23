---
title: "Tutorial de SaaS multilocatário- Banco de Dados SQL do Azure | Microsoft Docs"
description: "Implante e explore um aplicativo SaaS autônomo de locatário único que usa o Banco de Dados SQL do Azure."
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: sstein
ms.openlocfilehash: 2ca290dfcb23215ffd727500e76076ae8b14fa6f
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Implantar e explorar um aplicativo autônomo de locatário único que usa o Banco de Dados SQL do Azure

Neste tutorial, você implanta e explora o Aplicativo Autônomo SaaS Wingtip Tickets. O aplicativo foi projetado para demonstrar os recursos do Banco de dados SQL do Azure que habilitam de forma simples cenários SaaS.

O padrão de Aplicativo Autônomo implanta um grupo de recursos do Azure que contém um aplicativo de locatário único e um banco de dados de locatário único para cada locatário.  Várias instâncias do aplicativo podem ser provisionadas para fornecer uma solução de multilocatário.

Embora neste tutorial você implante grupos de recursos para vários locatários em sua assinatura do Azure, esse padrão permite que os grupos de recursos sejam implantados na assinatura do Azure de um locatário.  O Azure tem programas de parceria que permitem que esses grupos de recursos sejam gerenciados por um provedor de serviços em nome do locatário como um administrador na assinatura do locatário.

Na seção de implantação abaixo, há três botões Implantar no Azure, cada um implementando uma instância diferente do aplicativo personalizado para um locatário específico. Quando cada botão é pressionado, o aplicativo correspondente é totalmente implantado após cinco minutos.  Os aplicativos são implantados em sua assinatura do Azure.  Você tem acesso completo para explorar e trabalhar com os componentes individuais do aplicativo.

O código-fonte do aplicativo e os scripts de gerenciamento estão disponíveis no repositório GitHub [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp).


Neste tutorial, você aprende:

> [!div class="checklist"]

> * Como implantar o Aplicativo Autônomo SaaS Wingtip Tickets
> * Onde obter o código-fonte do aplicativo e scripts de gerenciamento
> * Sobre os servidores e bancos de dados que constituem o aplicativo

Outros tutoriais serão liberados em um momento oportuno, que permitirão explorar uma variedade de cenários de gerenciamento com base nesse padrão de aplicativo.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Implantar o Aplicativo Autônomo SaaS Wingtip Tickets

Implante o aplicativo para os três locatários fornecidos:

1. Clique em cada botão **Implantar no Azure** para abrir o modelo de implantação no portal do Azure. Cada modelo exige dois valores de parâmetro: um nome para um novo grupo de recursos e um nome de usuário que distingue essa implantação de outras implantações do aplicativo. A próxima etapa fornece detalhes para definir esses valores.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>     &nbsp;&nbsp;**Contoso Concert Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp;&nbsp;**Dogwood Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>    &nbsp;&nbsp;**Fabrikam Jazz Club**

2. Insira os valores de parâmetro necessários para cada implantação.

    > [!IMPORTANT]
    > Algumas autenticações e firewalls de servidor estão intencionalmente desprotegidos para fins de demonstração. **Crie um novo grupo de recursos** para cada implantação de aplicativo.  Não use um grupo de recursos existente. Não use este aplicativo ou todos os recursos que cria, para a produção. Exclua todos os grupos de recursos quando terminar com os aplicativos para interromper a cobrança relacionada.

    É melhor usar apenas letras minúsculas, números e hifens nos nomes de recursos.
    * Para **Grupo de recursos** – selecione Criar novo e, em seguida, forneça um Nome para o grupo de recursos (diferencia maiúsculas de minúsculas).
        * Recomendamos que todas as letras do nome do grupo de recursos sejam minúsculas.
        * Recomendamos também que você acrescente um traço, seguido por suas iniciais e por um dígito: por exemplo, _wingtip-sa-af1_.
        * Selecione uma Localização na lista suspensa.

    * Para **Usuário** – recomendamos escolher um valor curto de Usuário, como suas iniciais mais um dígito: por exemplo, _af1_.


1. **Implantar o aplicativo**.

    * Clique se você concordar com os termos e condições.
    * Clique em **Comprar**.

1. Monitore o status da implantação das três implantações clicando em **Notificações** (o ícone de sino à direita da caixa de pesquisa). A implantação do aplicativo leva aproximadamente cinco minutos.


## <a name="run-the-application"></a>Executar o aplicativo

O aplicativo apresenta locais, como salas de concertos, clubes de jazz e clubes esportivos, que hospedam eventos. Os locais se registram como clientes (ou locatários) do Wingtip Tickets, para uma maneira fácil de listar eventos e vender ingressos. Cada local obtém um site personalizado para gerenciar e listar seus eventos e vender ingressos, independente e isolado de outros locatários. De maneira oculta, cada locatário obtém uma instância de aplicativo separada e um banco de dados SQL autônomo.

1. Abra a página de eventos de cada um dos três locatários em guias separadas do navegador:

    http://events.contosoconcerthall.&lt;USER&gt;.trafficmanager.net <br>
    http://events.dogwooddojo.&lt;USER&gt;.trafficmanager.net<br>
    http://events.fabrikamjazzclub.&lt;USER&gt;.trafficmanager.net

    (substitua &lt;USER&gt; pelo valor de User da implantação).

   ![Eventos](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)


Para controlar a distribuição das solicitações de entrada, o aplicativo usa o [*Gerenciador de Tráfego do Azure*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview). Cada aplicativo específico ao locatário inclui o nome do locatário como parte do nome de domínio na URL. Todas as URLs de locatário incluem o valor específico de *User* e seguem este formato: http://events.&lt;venuename&gt;.&lt;USER&gt;.trafficmanager.net. O local do banco de dados de cada locatário está incluído nas configurações do aplicativo implantado correspondente.

Em um ambiente de produção, você normalmente criará um registro DNS CNAME para [*apontar um domínio da Internet da empresa*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) para a URL do perfil do gerenciador de tráfego.


## <a name="explore-the-servers-and-tenant-databases"></a>Explorar os servidores e bancos de dados de locatários

Vamos examinar alguns dos recursos que foram implantados:

1. No [portal do Azure](http://portal.azure.com), procure a lista de grupos de recursos.  Você deverá ver o grupo de recursos **wingtip-sa-catalog-&lt;USER&gt;** no qual o servidor **catalog-sa-&lt;USER&gt;** é implantado com o banco de dados **tenantcatalog**. Você também deverá ver os três grupos de recursos de locatários.

1. Abra o grupo de recursos **wingtip-sa-fabrikam-&lt;USER&gt;**, que contém os recursos para a implantação do Fabrikam Jazz Club.  O servidor **fabrikamjazzclub-&lt;USER&gt;** contém o banco de dados **fabrikamjazzclub**.


Cada banco de dados de locatários é um banco de dados _Autônomo_ de 50 DTUs.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu:

> [!div class="checklist"]

> * Como implantar o Aplicativo Autônomo SaaS Wingtip Tickets
> * Sobre os servidores e bancos de dados que constituem o aplicativo
> * Como excluir recursos de exemplo para interromper a cobrança relacionada


## <a name="additional-resources"></a>Recursos adicionais

<!--* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview) -->
* Para saber mais sobre aplicativos SaaS multilocatários, consulte [*Padrões de design para aplicativos SaaS multilocatários*](saas-tenancy-app-design-patterns.md)
