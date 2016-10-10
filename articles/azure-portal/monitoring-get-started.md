<properties
	pageTitle="Introdução ao Azure Monitor | Microsoft Azure"
	description="Começar a usar o Azure Monitor para obter informações sobre a operação de seus recursos e tomada de ação com base nos dados."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/26/2016"
	ms.author="johnkem"/>

# Introdução ao Azure Monitor

O Azure Monitor é um novo serviço de plataforma que fornece uma única fonte para monitorar os recursos do Azure. Com o Azure Monitor, você pode visualizar, consultar, rotear, arquivar e tomar ações nas métricas e nos logs provenientes dos recursos do Azure. Você pode trabalhar com esses dados usando a folha do portal do Monitor, Cmdlets do PowerShell do Insights, CLI de Plataforma Cruzada ou APIs REST do Azure Insights. Neste artigo, veremos alguns dos principais componentes do Azure Monitor.

1. No portal, navegue até **Mais serviços** e localize a opção **Monitor**. Clique no ícone de estrela para adicionar essa opção à lista de favoritos para que fique sempre facilmente acessível na barra de navegação à esquerda.

    ![Monitor na lista de serviços](./media/monitoring-get-started/monitor-more-services.png)

2. Clique na opção **Monitor** para abrir a folha **Monitor**. Esta folha reúne todas as suas configurações e dados de monitoramento em uma exibição consolidada. Ela abre primeiro na seção **Log de atividades**.

    ![Navegação da folha Monitor](./media/monitoring-get-started/monitor-blade-nav.png)

    > [AZURE.WARNING] As opções **Notificações de serviço** e **Grupos de notificação** exibidas são uma Visualização Particular e aparecem somente para aqueles que se associaram à Visualização Particular.

    O Azure Monitor tem três categorias básicas de monitoramento de dados: log de atividades, métricas e logs de diagnóstico.

3. Clique em **Log de atividades** para garantir que a seção do log de atividades seja exibida.

    ![Folha Log de Atividades](./media/monitoring-get-started/monitor-act-log-blade.png)

    O **log de atividades** descreve todas as operações executadas nos recursos em sua assinatura. Usando o Log de Atividades, você pode determinar 'o que, quem e quando' para quaisquer operações de gravação nos recursos em sua assinatura. Por exemplo, o Log de Atividades informa quando um aplicativo Web foi interrompido e quem o interrompeu. Os eventos do Log de Atividades são armazenados na plataforma por 90 dias.
   
    Você pode criar e salvar consultas para os filtros comuns e fixar as consultas mais importantes em um painel do portal para que sempre saiba se ocorreram eventos que atendem aos seus critérios.

4. Filtre a exibição para um determinado grupo de recursos na última semana, então, clique no botão **Salvar**.

    ![Salvar consulta do log de atividades](./media/monitoring-get-started/monitor-act-log-save.png)

5. Agora, clique no **Fixar**.

    ![Clicar para fixar o log de atividades](./media/monitoring-get-started/monitor-act-log-pin.png)

    A maioria das exibições neste passo a passo pode ser fixada em um painel. Isso ajuda a criar uma única fonte de informações para os dados operacionais em seus serviços.

6. Volte para o painel. Agora, você pode ver que a consulta (e o número de resultados) é exibida no painel.

    ![Log de atividades fixado no painel](./media/monitoring-get-started/monitor-act-log-db.png)

7. Volte para o bloco **Monitor** e clique na seção **Métricas**. Primeiro, você precisa selecionar um recurso filtrando e selecionando com as opções na parte superior da seção.

    ![Recurso de filtro para métricas](./media/monitoring-get-started/monitor-met-filter.png)

    Todos os recursos do Azure emitem métricas. Essa exibição une todas elas em um único painel.

8. Depois de selecionar um recurso, todas as métricas disponíveis aparecerão no lado esquerdo da folha. Você pode representar graficamente várias métricas de uma vez selecionando as métricas e modificando o tipo de gráfico e o intervalo de tempo. Também pode exibir todos os alertas de métrica definidos nesse recurso.

    ![Lâmina Métrica](./media/monitoring-get-started/monitor-metric-blade.png)

    > [AZURE.NOTE] Algumas métricas só estão disponíveis habilitando o [Application Insights](../application-insights/app-insights-overview.md) em seu recurso.

9. Quando você estiver satisfeito com seu gráfico, poderá usar o botão **Fixar** para fixar em seu painel.

10. Volte para a folha **Monitor** e clique em **Logs de diagnóstico**.

    ![Folha Logs de Diagnóstico](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    Os logs de diagnóstico são logs emitidos por um recurso, fornecendo dados sobre a operação desse determinado recurso. Por exemplo, os Contadores de Regras do Grupo de Segurança da Rede e os Logs do Fluxo de Trabalho do Aplicativo Lógico são dois tipos de logs de diagnóstico. Esses logs podem ser armazenados em uma conta de armazenamento, transmitidos para um Hub de Eventos e/ou enviados para o [Log Analytics](../log-analytics/log-analytics-overview.md), o produto de inteligência operacional da Microsoft, para uma pesquisa avançada e alertas.
   
    No portal, você pode exibir e filtrar uma lista de todos os recursos em sua assinatura para identificar se eles tiverem os logs de diagnóstico habilitados.

11. Clique em um recurso na folha dos logs de diagnóstico. Se os logs de diagnóstico estiverem sendo armazenados em uma conta de armazenamento, você verá uma lista dos registros por hora que podem ser baixados diretamente. Você também pode clicar em "Ativar/desativar diagnóstico", que permite configurar o arquivamento em uma conta de armazenamento, transmitir os Hubs de Eventos ou enviar para um espaço de trabalho do Log Analytics.

    ![Logs de diagnóstico para um recurso](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    Se você configurou os logs de diagnóstico para o Log Analytics, poderá pesquisá-los na seção **Pesquisar Log** do portal.

12. Navegue até a seção **Alertas** da folha Monitor.

    ![folha de alertas para o público](./media/monitoring-get-started/monitor-alerts-nopp.png)

    Aqui, você pode gerenciar todos os alertas nos recursos do Azure. Isso inclui alertas sobre as métricas, eventos do log de atividades (visualização), testes da Web do Application Insights (Locais) e diagnóstico proativo do Application Insights. Os alertas podem disparar o envio de um email ou POST para um webhook.
   
13. Clique em **Adicionar alerta da métrica** para criar um alerta.

    ![adicionar alerta de métrica](./media/monitoring-get-started/monitor-alerts-add.png)

    Em seguida, você pode fixar um alerta no painel para ver facilmente seu estado a qualquer momento.

14. A seção Monitor também inclui links para os aplicativos [Application Insights](../application-insights/app-insights-overview.md) e as soluções de gerenciamento do [Log Analytics](../log-analytics/log-analytics-overview.md). Esses outros produtos da Microsoft têm uma profunda integração com o Azure Monitor.

15. Se você não estiver usando o Application Insights nem o Log Analytics, as chances são de que o Azure Monitor tem uma parceria com o monitoramento, log e alerta de produtos atuais. Consulte nossa [página de parceiros](./monitoring-partners.md) para ver uma lista completa e ter instruções sobre como integrar.

Seguindo essas etapas e fixando todos os blocos relevantes em um painel, você pode criar exibições detalhadas de seus aplicativos e da infraestrutura assim:

![Painel do Azure Monitor](./media/monitoring-get-started/monitor-final-dash.png)


## Próximas etapas
- Leia a [Visão Geral do Azure Monitor](./monitoring-overview.md)

<!---HONumber=AcomDC_0928_2016-->