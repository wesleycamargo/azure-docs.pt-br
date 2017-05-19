---
title: Implantar e explorar o aplicativo WTP (Wingtip Tickets Platform) (aplicativo SaaS de exemplo, usando o Banco de Dados SQL do Azure) | Microsoft Docs
description: Implantar e explorar um aplicativo SaaS de exemplo que usa o Banco de Dados SQL do Azure
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ff4dc19bb6e24ea9ceeed9721cfb3a85b4d10965
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-azure-sql-database"></a>Implantar e explorar um Aplicativo SaaS multilocatário que usa o Banco de Dados SQL do Azure

Neste tutorial, você implanta e explora o aplicativo SaaS WTP (Wingtip Tickets Platform). O aplicativo usa um padrão de aplicativo SaaS de um banco de dados por locatário para atender a vários locatários. O aplicativo foi projetado para demonstrar os recursos do Banco de Dados SQL do Azure que habilitam cenários de SaaS, além de padrões de design e gerenciamento de SaaS.

Cinco minutos depois de clicar no botão *Implantar no Azure* abaixo, você terá um aplicativo SaaS multilocatário, usando o Banco de Dados SQL, em funcionamento na nuvem. O aplicativo é implantado com três locatários de exemplo, cada um com seu próprio banco de dados, todos implantados em um pool elástico do SQL. O aplicativo é implantado na sua assinatura do Azure, dando a você acesso completo para inspecionar e trabalhar com os componentes individuais do aplicativo.

Neste tutorial, você aprende:

> [!div class="checklist"]

> * Como implantar o aplicativo WTP
> * Sobre os servidores, pools e bancos de dados que constituem o aplicativo
> * Que os locatários são mapeados para seus dados com o *catálogo*
> * Como provisionar novos locatários
> * Como exibir a utilização do pool para monitorar a atividade do locatário
> * Como excluir recursos de exemplo para interromper a cobrança relacionada

Para explorar os vários padrões de design e gerenciamento de SaaS, uma [série de tutoriais relacionados](sql-database-wtp-overview.md) que aproveita essa implantação inicial está disponível. Ao percorrer os tutoriais, aprofunde-se nos scripts fornecidos e examine como os diferentes padrões de SaaS são implementados. Percorra os scripts em cada tutorial para obter uma maior compreensão de como implementar os diversos recursos de Banco de Dados SQL que simplificam o desenvolvimento de aplicativos SaaS.

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-tickets-wtp-saas-application"></a>Implantar o Aplicativo SaaS WTP (ingressos Wingtip)

Implante a plataforma de ingressos Wingtip em menos de cinco minutos:

1. Clique para implantar:

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Insira os valores de parâmetros necessários para a implantação:

    > [!IMPORTANT]
    > Algumas autenticações e firewalls de servidor estão intencionalmente desprotegidos para fins de demonstração. **Crie um novo grupo de recursos** e não use grupos de recursos, servidores ou pools existentes e também não esse aplicativo ou quaisquer recursos que ele cria, para produção. Exclua esse grupo de recursos quando tiver terminado com o aplicativo para interromper a cobrança relacionada.

    * **Grupo de recursos** – Selecione **Criar novo** e forneça um **Nome** e **Local**.
    * **Usuário** – Alguns recursos exigem nomes que sejam exclusivos em todas as assinaturas do Azure. Para garantir a exclusividade, forneça um valor para diferenciar os recursos que você cria dos recursos criados por outros usuários que estão implantando o aplicativo Wingtip. Recomenda-se usar um nome de **Usuário** curto, como suas iniciais com algum número (por exemplo, *bg1*) e, em seguida, usá-lo no nome do grupo de recursos (por exemplo, *wingtip-bg1*). O parâmetro **User** só pode conter letras, números e hifens. O primeiro e último caracteres devem ser uma letra ou um número (recomenda-se todas em minúsculas).

     ![template](./media/sql-database-saas-tutorial/template.png)

1. **Implantar o aplicativo**.

    * Clique se você concordar com os termos e condições.
    * Selecione **Fixar no painel**.
    * Clique em **Comprar**.

1. Monitore o status da implantação clicando em **Notificações** (o ícone de sino à direita da caixa de pesquisa). A implantação do aplicativo WTP leva aproximadamente quatro minutos.

   ![implantação bem-sucedida](media/sql-database-saas-tutorial/succeeded.png)

## <a name="explore-the-application"></a>Explorar o aplicativo

O aplicativo demonstra locais, como teatros, clubes de jazz, clubes esportivos, que hospedam eventos. Os locais se registram como os clientes (ou locatários) do WTP (Wingtip Tickets Platform ), para uma maneira fácil de listar eventos e vender ingressos. Cada local obtém um aplicativo Web personalizado para gerenciar e listar seus eventos e vender ingressos, independente e isolado de outros locatários. Nos bastidores, cada locatário obtém um banco de dados SQL implantado em um pool elástico do SQL.

Um **Hub de Eventos** central fornece uma lista de URLs de locatários específica da sua implantação. Todas as URLs de locatário incluem o seu valor específico de *User* e seguem este formato: http://events.wtp.&lt;USER&gt;.trafficmanager.net/*fabrikamjazzclub*. 

1. Abra o _Hub de Eventos_: http://events.wtp.&lt;USER&gt;.trafficmanager.net (substitua pelo seu Nome de usuário):

    ![hub de eventos](media/sql-database-saas-tutorial/events-hub.png)

1. Clique em **Fabrikam Jazz Club** no *Hub de Eventos*.

   ![Eventos](./media/sql-database-saas-tutorial/fabrikam.png)

1. Clique em **Ingressos** e explore a compra de ingressos para um evento.

O aplicativo WTP usa o [*Gerenciador de Tráfego do Azure*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) para controlar a distribuição do tráfego de entrada. As páginas de eventos, que são específicas do locatário, exigem que os nomes de locatário sejam incluídos nas URLs. O aplicativo de eventos analisa o nome do locatário com base na URL e o utiliza para criar uma chave para acessar um catálogo usando o [*gerenciamento de mapa de fragmentos*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management). O catálogo mapeia a chave para o local do banco de dados do locatário. O **Hub de Eventos** usa metadados estendidos no catálogo para recuperar o nome do locatário associado a cada banco de dados.

Em um ambiente de produção, você normalmente criaria um registro DNS CNAME para [*apontar um domínio de Internet da empresa*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) para o perfil do gerenciador de tráfego.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts do aplicativo Wingtip

Os scripts do Wingtip Tickets e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os arquivos de script estão localizados na [pasta de Módulos de Aprendizado](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Baixe a pasta **Módulos de Aprendizado** em seu computador local, mantendo a estrutura de pastas.

## <a name="provision-a-new-tenant"></a>Provisionar um novo locatário

A implantação inicial cria três locatários de exemplo, mas é necessário criar mais locatários para uma melhor experiência neste tutorial. Nesta etapa você cria rapidamente um novo locatário. Mais tarde você poderá aprofundar-se nos detalhes do provisionamento de novos locatários com o [Tutorial para provisionar e catalogar](sql-database-saas-tutorial-provision-and-catalog.md), no qual poderá ver como é simples implementar um componente de registro no aplicativo e provisionar automaticamente locatários conforme os clientes se inscrevem.

### <a name="initialize-the-user-config-file-for-your-deployment"></a>Inicializar o arquivo de configuração do usuário para a sua implantação

Como esse é o primeiro script que você vai executar após a implantação inicial do aplicativo WTP, é necessário atualizar o arquivo de configuração do usuário. Atualize as variáveis com os valores específicos da sua implantação.

   1. Abra ...\\Módulos de aprendizado\\*UserConfig.psm1* no *ISE do PowerShell*
   1. Modifique o _$userConfig.ResourceGroupName_ para o _grupo de recursos_ que foi definido quando você implantou o aplicativo.
   1. Modifique o _$userConfig.Name_ para o nome de _Usuário_ definido quando você implantou o aplicativo.

### <a name="provision-the-new-tenant"></a>Provisionar o novo locatário

1. Abra ...\\Módulos de aprendizado\Provisionar e catalogar\\*Demo-ProvisionAndCatalog.ps1* no *ISE do PowerShell*.
1. Pressione **F5** para executar o script (mantenha os valores padrão do script por enquanto).

O novo locatário é registrado no catálogo. O banco de dados é criado e adicionado ao pool elástico do SQL. Após o provisionamento bem-sucedido, o novo site de vendas de ingresso do locatário aparece no seu navegador:

![Novo locatário](./media/sql-database-saas-tutorial/red-maple-racing.png)

Atualize o Hub de Eventos e verifique se o novo locatário está na lista.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniciar a geração de carga nos bancos de dados de locatário

Agora que temos vários bancos de dados de locatário, vamos colocá-los para trabalhar! É fornecido um script do PowerShell que simula uma carga de trabalho em execução em todos os bancos de dados de locatário. A carga é executada por 60 minutos por padrão. O Wingtip Tickets é um aplicativo SaaS e a carga real em um aplicativo SaaS é normalmente esporádica e imprevisível. Para simular isso, o gerador de carga gera uma carga aleatória distribuída entre todos os locatários. São necessários vários minutos para que o padrão surja, portanto deixe o gerador de carga em execução por cerca de 5 a 10 minutos antes de tentar monitorar a carga nas seções a seguir.

1. Abra ...\\Módulos de aprendizado\\Utilitários\\*Demo-LoadGenerator.psm1* no **ISE do PowerShell**
1. Pressione **F5** para executar o script e iniciar o gerador de carga (deixe os valores de parâmetro padrão por enquanto).

> [!IMPORTANT]
> O gerador de carga é executado como uma série de trabalhos em sua sessão local do PowerShell, portanto mantenha a sessão do PowerShell aberta! Se você fechar o PowerShell ou a guia na qual o gerador de carga foi iniciado ou suspender seu computador, os trabalhos serão interrompidos.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorar os servidores, os pools e os bancos de dados de locatário

Agora que você explorou o aplicativo, provisionou um novo locatário e iniciou a execução de uma carga na coleção de locatários, vamos analisar alguns dos recursos que foram implantados.

1. No [Portal do Azure](http://portal.azure.com), abra o servidor **catalog-&lt;USER&gt;**. O servidor de catálogo contém dois bancos de dados. O **tenantcatalog** e o **basetenantdb** (um BD *golden* vazio que é copiado para criar novos locatários).

   ![databases](./media/sql-database-saas-tutorial/databases.png)

1. Abra o servidor **tenants1-&lt;USER&gt;** que contém os bancos de dados de locatário. Observe que cada banco de dados de locatário é um banco de dados _elástico Standard_ em um pool padrão de 50 eDTU. Observe também que há um banco de dados _Red Maple Racing_, o locatário que você provisionou anteriormente.

   ![server](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>Monitorar o pool

Se o gerador de carga estiver sendo executado por vários minutos, dados suficientes deverão estar disponíveis para começar a examinar alguns dos recursos de monitoramento incorporados em pools e bancos de dados.

1. Navegue até o servidor *tenants1-&lt;USER&gt;* e clique em **Pool1** para exibir a utilização de recursos do pool:

   ![monitorar pool](./media/sql-database-saas-tutorial/monitor-pool.png)

O que esses dois gráficos bem ilustram, é como os pools elásticos e o Banco de Dados SQL são bem adequados para cargas de trabalho de aplicativos SaaS. Quatro bancos de dados que estão, cada um, atingindo picos de até 40 eDTUs tem suporte facilmente em um pool de 50 eDTU. Se cada um fosse provisionado como um banco de dados autônomo, eles teriam que ser um S2 (50 DTU) para dar suporte aos picos, mas o custo de quatro bancos de dados autônomos S2 seria quase o triplo do preço do pool. E o pool ainda tem bastante espaço sobrando para muitos outros bancos de dados. Em situações reais, os clientes estão executando até 500 bancos de dados em pools de 200 eDTUs. Para obter mais informações, consulte o [tutorial de monitoramento de desempenho](sql-database-saas-tutorial-performance-monitoring.md).


## <a name="deleting-the-resources-created-with-this-tutorial"></a>Excluindo os recursos criados com este tutorial

Quando tiver terminado de explorar e trabalhar com o aplicativo WTP, navegue até o grupo de recursos do aplicativo no portal e exclua-o para interromper toda a cobrança relacionada a essa implantação. Se você tiver usado qualquer um dos tutoriais que acompanha este artigo, todos os recursos que foram criados também serão excluídos com o aplicativo.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu:

> [!div class="checklist"]

> * Como implantar o aplicativo WTP
> * Sobre os servidores, pools e bancos de dados que constituem o aplicativo
> * Que os locatários são mapeados para seus dados com o *catálogo*
> * Como provisionar novos locatários
> * Como exibir a utilização do pool para monitorar a atividade do locatário
> * Como excluir recursos de exemplo para interromper a cobrança relacionada

Agora, tente fazer o [Tutorial para provisionar e catalogar](sql-database-saas-tutorial-provision-and-catalog.md)



## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo WTP (Wingtip Tickets Platform)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* Para saber mais sobre pools elásticos, consulte [*O que é um pool elástico do SQL do Azure*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* Para saber mais sobre os trabalhos elásticos, consulte [*Gerenciando bancos de dados de nuvem com escalonamento horizontal*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
* Para saber mais sobre aplicativos SaaS multilocatários, consulte [*Padrões de design para aplicativos SaaS multilocatários*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)

