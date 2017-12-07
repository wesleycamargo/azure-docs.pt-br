---
title: "Tutorial de SaaS multilocatário- Banco de Dados SQL do Azure | Microsoft Docs"
description: "Implante e explore o aplicativo Wingtip Tickets SaaS multi-tenant, que demonstra o Database per Tenant e outros padrões de SaaS usando o Banco de Dados SQL do Azure."
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
ms.date: 11/10/2017
ms.author: sstein
ms.openlocfilehash: f91ddff81e51e7cc3d1561dc799013764530924b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Implantar e explorar um aplicativo SaaS multilocatário que usa o padrão por locatário do banco de dados com o Banco de Dados SQL do Azure

Neste tutorial, você implanta e explora o aplicativo Wingtip Tickets SaaS Database per Tenant. O aplicativo usa um padrão de banco de dados por locatário para armazenar os dados de vários locatários. O aplicativo foi projetado para demonstrar os recursos do Banco de dados SQL do Azure que habilitam de forma simples cenários SaaS.

Cinco minutos depois de clicar no botão *Implantar no Azure* abaixo, você terá um aplicativo SaaS multilocatário, usando o Banco de Dados SQL, em funcionamento na nuvem. O aplicativo é implantado com três locatários de exemplo, cada um com seu próprio banco de dados, todos implantados em um pool elástico do SQL. O aplicativo é implantado na sua assinatura do Azure, dando a você acesso completo para explorar e trabalhar com os componentes individuais do aplicativo. O código-fonte do aplicativo e os scripts de gerenciamento estão disponíveis no repositório WingtipTicketsSaaS-DbPerTenant do GitHub.


Neste tutorial, você aprende:

> [!div class="checklist"]

> * Como implantar o aplicativo SaaS do Wingtip
> * Onde obter o código-fonte do aplicativo e scripts de gerenciamento
> * Sobre os servidores, pools e bancos de dados que constituem o aplicativo
> * Quantos locatários são mapeados para seus dados com o *catálogo*
> * Como provisionar um novo locatário
> * Como monitorar a atividade de locatário no aplicativo

Para explorar os vários padrões de design e gerenciamento de SaaS, uma [série de tutoriais relacionados](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) que aproveita essa implantação inicial está disponível. Ao percorrer os tutoriais, aprofunde-se nos scripts fornecidos e examine como os diferentes padrões de SaaS são implementados. Percorra os scripts em cada tutorial para obter uma maior compreensão de como implementar os diversos recursos de Banco de Dados SQL que simplificam o desenvolvimento de aplicativos SaaS.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Implantar o aplicativo Wingtip Tickets SaaS

Implante o aplicativo:

1. Clicar no botão **Implantar no Azure** abre o modelo de implantação Wingtip Tickets SaaS Database per Tenant no portal do Azure. O modelo requer dois valores de parâmetros; um nome para um novo grupo de recursos e um nome de usuário que distingue esta implantação de outras implantações do aplicativo Wingtip Tickets SaaS Database per Tenant. A próxima etapa fornece detalhes para definir esses valores.

   Certifique-se de observar os valores exatos que usa, pois você precisará inseri-los em um arquivo de configuração.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Insira os valores de parâmetros necessários para a implantação:

    > [!IMPORTANT]
    > Algumas autenticações e firewalls de servidor estão intencionalmente desprotegidos para fins de demonstração. **Crie um novo grupo de recursos**. Não use grupos de recursos, servidores ou pools existentes. Não use esse aplicativo, scripts ou qualquer recurso implantado para produção. Exclua esse grupo de recursos quando tiver terminado com o aplicativo para interromper a cobrança relacionada.

    * **Grupo de recursos** – selecione **Criar novo** e forneça um **Nome** para o grupo de recursos. 
    * **Localização** - selecione uma **Localização** na lista suspensa.
    * **Usuário** - Alguns recursos exigem nomes que são globalmente exclusivos. Para garantir a exclusividade, toda vez que você implanta, o aplicativo fornece um valor para diferenciar os recursos que você cria dos recursos criados por outros usuários que estão implantando o aplicativo Wingtip. Recomenda-se usar um nome de **Usuário** curto, como suas iniciais mais algum número (por exemplo, *af1*) e, em seguida, usá-lo no nome do grupo de recursos (por exemplo, *wingtip-af1*). O parâmetro **Usuário** só pode conter letras, números e hifens (sem espaços). O primeiro e último caracteres devem ser uma letra ou um número (recomenda-se todas em minúsculas).


1. **Implantar o aplicativo**.

    * Clique se você concordar com os termos e condições.
    * Clique em **Comprar**.

1. Monitore o status da implantação clicando em **Notificações** (o ícone de sino à direita da caixa de pesquisa). A implantação do aplicativo Wingtip Tickets SaaS leva aproximadamente cinco minutos.

   ![implantação bem-sucedida](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Baixar e desbloquear os scripts de gerenciamento de Wingtip Tickets

Enquanto o aplicativo for implantado, baixe os scripts de gerenciamento e de código-fonte.

> [!IMPORTANT]
> Conteúdos executáveis (scripts, dlls) podem ser bloqueados pelo Windows quando arquivos zip são baixados de uma fonte externa e extraídos. Ao extrair os scripts de um arquivo zip, siga as etapas abaixo para desbloquear o arquivo. zip antes da extração. Isso garante que os scripts podem ser executados.

1. Navegue até o [repositório WingtipTicketsSaaS-DbPerTenant do GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
1. Clique em **Clonar ou baixar**.
1. Clique em **Baixar ZIP** e salve o arquivo.
1. Clique com o botão direito do mouse no arquivo **WingtipTicketsSaaS-DbPerTenant-master.zip** e selecione **Propriedades**.
1. Na guia **Geral**, selecione **Desbloquear** e clique em **Aplicar**.
1. Clique em **OK**.
1. Extraia os arquivos.

Os scripts estão localizados na pasta *.\\WingtipTicketsSaaS-DbPerTenant-master\\Módulos de Aprendizado*.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Atualizar o arquivo de configuração para esta implantação

Antes de executar os scripts, atualize os valores *grupo de recursos* e *usuário* em **UserConfig.psm1**. Defina essas variáveis como os valores definidos durante a implantação.

1. No *ISE do PowerShell*, abra ...\\Módulos de Aprendizado\\*UserConfig.psm1* 
1. Atualize *ResourceGroupName* e *Nomeie* com os valores específicos para sua implantação (somente nas linhas 10 e 11).
1. Salve as alterações!

Esses valores são referenciados em quase todos os scripts.

## <a name="run-the-application"></a>Executar o aplicativo

O aplicativo apresenta os locais, como salas de concertos, clubes de jazz e clubes esportivos, que recebem os eventos. Os locais se registram como clientes (ou locatários) do Wingtip Tickets, para uma maneira fácil de listar eventos e vender ingressos. Cada local obtém um site personalizado para gerenciar e listar seus eventos e vender ingressos, independente e isolado de outros locatários. Nos bastidores, cada locatário obtém um banco de dados SQL implantado em um pool elástico do SQL.

Uma página central do **Hub de Eventos** fornece uma lista de links para os locatários em sua implantação.

1. Abra o _Hub de Eventos_ em seu navegador da Web: http://events.wingtip-dpt.&lt;USUÁRIO&gt;.trafficmanager.net (substitua &lt;USUÁRIO&gt; pelo seu nome de usuário da implantação):

    ![hub de eventos](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. Clique em **Fabrikam Jazz Club** no *Hub de Eventos*.

   ![Eventos](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)


Para controlar a distribuição das solicitações de entrada, o aplicativo usa o [*Gerenciador de Tráfego do Azure*](../traffic-manager/traffic-manager-overview.md). As páginas de eventos, que são específicas do locatário, exigem que os nomes de locatário sejam incluídos nas URLs. Todas as URLs de locatário incluem o seu valor específico de *Usuário* e seguem este formato: http://events.wingtipp-dpt.&lt;USUÁRIO&gt;.trafficmanager.net/*fabrikamjazzclub*. O aplicativo de eventos analisa o nome do locatário com base na URL e o utiliza para criar uma chave para acessar um catálogo implementado usando o [*gerenciamento de mapa de fragmentos*](sql-database-elastic-scale-shard-map-management.md). O catálogo mapeia a chave para o local do banco de dados do locatário. O **Hub de Eventos** usa metadados estendidos no catálogo para recuperar o nome do locatário associado a cada banco de dados para fornecer a lista de URLs.

Em um ambiente de produção, você normalmente criaria um registro DNS CNAME para [*apontar um domínio de Internet da empresa*](../traffic-manager/traffic-manager-point-internet-domain.md) para o perfil do gerenciador de tráfego.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniciar a geração de carga nos bancos de dados de locatário

Agora que o aplicativo está implantado, vamos colocá-lo em funcionamento! O script do PowerShell *Demo-LoadGenerator* começa uma execução de carga de trabalho em todos os bancos de dados de locatário. A carga real em um aplicativo SaaS é normalmente esporádica e imprevisível. Para simular esse tipo de carga, o gerador produz uma carga distribuída em todos os locatários com intermitências aleatórias em cada locatário, que ocorrem em intervalos aleatórios. Por isso, levará vários minutos para que o padrão de carga surja, portanto, é melhor permitir que o gerador seja executado por pelo menos três ou quatro minutos antes de monitorar a carga.

1. No *ISE do PowerShell*, abra o script ...\\Módulos de Aprendizado\\Utilitários\\*Demo-LoadGenerator.ps1*.
1. Pressione **F5** para executar o script e iniciar o gerador de carga (deixe os valores de parâmetro padrão por enquanto).

> [!IMPORTANT]
> Para executar outros scripts, abra uma nova janela do ISE do PowerShell. O gerador de carga está sendo executado como uma série de trabalhos em sua sessão local do PowerShell. O script *Demo-LoadGenerator.ps1* inicia o script do gerador de carga real, que é executado como uma tarefa em primeiro plano além de uma série de trabalhos de geração de cargas em segundo plano. Um trabalho do gerador de carga é chamado para cada banco de dados registrado no catálogo. Os trabalhos estão em execução na sua sessão local do PowerShell, então fechar a sessão do PowerShell interrompe todos os trabalhos. Se você suspender seu computador, a geração de carga será pausada e continuará quando você ativá-lo novamente.

Depois que o gerador de carga invoca os trabalhos de geração de carga para cada locatário, a tarefa em primeiro plano permanece em um estado de invocação de trabalho, onde ela inicia trabalhos de plano de fundo adicionais para quaisquer novos locatários que forem provisionados subsequentemente. Você pode usar *Ctrl-C* ou pressionar o botão *Parar* para interromper a tarefa em primeiro plano, mas trabalhos em segundo plano existentes continuarão gerando carga em cada banco de dados. Se você precisar monitorar e controlar os trabalhos em segundo plano, use *Get-Job*, *Receive-Job* e *Stop-Job*. Enquanto a tarefa em primeiro plano está em execução, não é possível usar a mesma sessão do PowerShell para executar outros scripts. Para executar outros scripts, abra uma nova janela do ISE do PowerShell.

Se deseja reiniciar a sessão do gerador de carga, por exemplo, com parâmetros diferentes, você pode interromper a tarefa em primeiro plano e, em seguida, executar novamente o script *Demo-LoadGenerator.ps1*. Executando novamente o *Demo-LoadGenerator.ps1*, primeiro ele para os trabalhos atualmente em execução e, em seguida, reinicia o gerador, que inicia um novo conjunto de trabalhos usando os parâmetros atuais.

Por enquanto, deixe o gerador de carga em execução no estado de invocação de trabalho.


## <a name="provision-a-new-tenant"></a>Provisionar um novo locatário

A implantação inicial cria três locatários de exemplo, mas vamos criar outro locatário para ver como isso afeta o aplicativo implantado. O fluxo de trabalho de locatários de provisionamento do Wingtip Tickets SaaS é descrito em detalhes no [tutorial Provisionamento e catálogo](saas-dbpertenant-provision-and-catalog.md). Nesta etapa, você cria rapidamente um novo locatário.

1. No *ISE do PowerShell*, abra ...\\Módulos de Aprendizado\Provisionamento e Catálogo\\*Demo-ProvisionAndCatalog.ps1*.
1. Pressione **F5** para executar o script (mantenha os valores padrão por enquanto).

   > [!NOTE]
   > Vários scripts Wingtip SaaS usam *$PSScriptRoot* para navegar em pastas para chamar funções em outros scripts. Essa variável é avaliada apenas quando o script completo é executado ao pressionar **F5**.  Realçar e executar uma seleção (**F8**) pode resultar em erros, então pressione **F5** ao executar scripts.

O novo banco de dados de locatário é criado em um pool elástico do SQL, inicializado e registrado no catálogo. Após o provisionamento bem-sucedido, o novo site *Evento s* do locatário aparecerá no seu navegador:

![Novo locatário](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Atualize o *Hub de Eventos* e o novo locatário agora está na lista.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorar os servidores, os pools e os bancos de dados de locatário

Agora que você começou a executar uma carga na coleção de locatários, vamos analisar alguns dos recursos que foram implantados:

1. No [portal do Azure](http://portal.azure.com), navegue até sua lista de servidores SQL e abra o servidor **catalog-dpt-&lt;USER&gt;**. O servidor de catálogo contém dois bancos de dados, o **tenantcatalog** e o **basetenantdb** (um banco de dados modelo que é copiado para criar novos locatários).

   ![databases](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. Volte para sua lista de servidores SQL e abra o servidor **tenants1-dpt-&lt;USER&gt;** que contém os bancos de dados de locatário. Cada banco de dados de locatário é um banco de dados _elástico padrão_ em um pool padrão de 50 eDTU. Observe também que há um banco de dados _Red Maple Racing_, o banco de dados de locatário que você provisionou anteriormente.

   ![Servidor](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorar o pool

Se o gerador de carga estiver sendo executado por vários minutos, dados suficientes deverão estar disponíveis para começar a examinar alguns dos recursos de monitoramento incorporados em pools e bancos de dados.

Navegue até **tenants1-dpt-&lt;USER&gt;** do servidor e clique em **Pool1** para exibir a utilização de recursos para o pool (o gerador de carga foi executado por uma hora nos gráficos a seguir):

   ![monitorar pool](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

O primeiro gráfico mostra a utilização do eDTU do pool, e o segundo gráfico mostra a utilização do eDTU dos cinco principais bancos de dados no pool.  O que esses dois gráficos bem ilustram, é como os pools elásticos e o Banco de Dados SQL são bem adequados para cargas de trabalho de aplicativos SaaS. Quatro bancos de dados que estão, cada um, atingindo picos de até 40 eDTUs tem suporte facilmente em um pool de 50 eDTU. Se eles foram provisionados como bancos de dados independentes, cada um deles precisa ser um S2 (50 DTU) para dar suporte a intermitências. O custo de 4 bancos de dados independentes S2 seria de quase 3 vezes o preço do pool e o pool ainda teria bastante espaço para muitos bancos de dados mais. Em situações reais, os clientes do Banco de dados SQL estão executando atualmente até 500 bancos de dados em 200 pools de eDTU. Para obter mais informações, consulte o [tutorial de monitoramento de desempenho](saas-dbpertenant-performance-monitoring.md).


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu:

> [!div class="checklist"]

> * Como implantar o aplicativo Wingtip Tickets SaaS
> * Sobre os servidores, pools e bancos de dados que constituem o aplicativo
> * Que os locatários são mapeados para seus dados com o *catálogo*
> * Como provisionar novos locatários
> * Como exibir a utilização do pool para monitorar a atividade do locatário
> * Como excluir recursos de exemplo para interromper a cobrança relacionada

Agora, tente fazer o [Tutorial para provisionar e catalogar](saas-dbpertenant-provision-and-catalog.md).



## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo Wingtip Tickets SaaS Database per Tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* Para saber mais sobre pools elásticos, consulte [*O que é um pool elástico do SQL do Azure*](sql-database-elastic-pool.md)
* Para saber mais sobre os trabalhos elásticos, consulte [*Gerenciando bancos de dados de nuvem com escalonamento horizontal*](sql-database-elastic-jobs-overview.md)
* Para saber mais sobre aplicativos SaaS multilocatários, consulte [*Padrões de design para aplicativos SaaS multilocatários*](saas-tenancy-app-design-patterns.md)
