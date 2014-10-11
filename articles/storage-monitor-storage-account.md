<properties linkid="manage-services-how-to-monitor-a-storage-account" urlDisplayName="How to monitor" pageTitle="How to monitor a storage account | Microsoft Azure" metaKeywords="Azure monitor storage accounts, storage account management portal, storage account dashboard, storage metrics table, storage metrics chart" description="Learn how to monitor a storage account in Azure by using the Management Portal." metaCanonical="" services="storage" documentationCenter="" title="How To Monitor a Storage Account" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# <span id="createstorageaccount"></span></a>Como monitorar uma conta de armazenamento

Você pode monitorar suas contas de armazenamento no Portal de Gerenciamento da Visualização do Azure. Para cada serviço de armazenamento associado à conta de armazenamento (Blob, Fila e Tabela), você pode escolher o nível de monitoramento – mínimo ou detalhado – e especificar a política de retenção de dados adequada.

Até que você configure o monitoramento para uma conta de armazenamento, nenhum dado de monitoramento é coletado e os gráficos de métricas no painel e na página **Monitorar** ficam vazios.

<div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>Lembre-se de que h&aacute; custos adicionais associados ao exame de dados de monitoramento no Portal de Gerenciamento. Para obter mais informa&ccedil;&otilde;es, consulte <a href="http://msdn.microsoft.com/en-us/library/windowsazure/hh360997.aspx">An&aacute;lise de armazenamento e cobran&ccedil;a</a>.</p> 
</div>

## Sumário

-   [Como: Configurar o monitoramento para uma conta de armazenamento][]
-   [Como: Personalizar o painel para o monitoramento][]
-   [Como: Personalizar a página de Monitoramento][]
-   [Como: adicionar métricas à tabela de métricas][]
-   [Como: Personalizar o gráfico de métricas na página de Monitoramento][]
-   [Como: Configurar o registro em log][]

## <span id="configurestoragemonitoring"></span></a>Como: Configurar o monitoramento para uma conta de armazenamento

1.  No [Portal de Gerenciamento][], clique em **Armazenamento** e no nome da conta de armazenamento para abrir o painel.

2.  Clique em **Configurar** e role para baixo até as configurações de **monitoramento** dos serviços de Blob, Tabela e Fila, mostrados a seguir.

    ![Opções de monitoramento][]

3.  Em **monitoramento**, defina o nível de monitoramento e a política de retenção de dados para cada serviço:

-   Para definir o nível de monitoramento, selecione uma das seguintes opções:

    **Mínimo** - Coleta métricas como entrada/saída, disponibilidade, latência e porcentagens de êxitos, que são agregadas aos serviços de Blob, Tabela e Fila.

    **Detalhado** - Além das métricas mínimas, coleta o mesmo conjunto de métricas para cada operação de armazenamento na API do Serviço de Armazenamento do Azure. As métricas no modo detalhado permitem uma análise mais próxima dos problemas que ocorrem durante operações de aplicativo.

    **Desativar** - Desativa o monitoramento. Os dados de monitoramento existentes são mantidos até o final do período de retenção.

-   Para definir a política de retenção de dados, em **Retenção (em dias)**, digite o número de dias que os dados devem ser retidos, de 1 a 365 dias. Se não desejar definir uma política de retenção, digite zero. Se não houver nenhuma política de retenção, cabe a você excluir os dados de monitoramento. É recomendável configurar uma política de retenção com base em quanto tempo deseja manter os dados de analítica do armazenamento da sua conta para que os dados de analítica antigos e não utilizados possam ser excluídos pelo sistema sem custo adicional.

1.  Ao concluir a configuração do monitoramento, clique em **Salvar**.

Você deve começar a visualizar o monitoramento de dados no painel e a página de **Monitoramento** após cerca de uma hora.

As métricas são armazenadas na conta de armazenamento em quatro tabelas intituladas $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue e $MetricsCapacityBlob. Para obter mais informações, consulte [Sobre as Métricas de Analítica de Armazenamento][].

Depois de definir os níveis de monitoramento e as políticas de retenção, você pode escolher quais das métricas disponíveis serão monitoradas no Portal de Gerenciamento e quais métricas serão traçadas nos gráficos de métricas. Um conjunto de métricas padrão é exibido em cada nível de monitoramento. Você pode usar **Adicionar métricas** para adicionar ou remover métricas da lista de métricas.

## <span id="customizestoragemonitoring"></span></a>Como: Personalizar o painel para o monitoramento

No painel, você pode escolher até seis métricas para serem traçadas no gráfico de métricas de nove métricas disponíveis. Para cada serviço (Blob, Tabela e Fila), as métricas de Disponibilidade, Porcentagem de Êxitos e Total de Solicitações estão disponíveis. As métricas disponíveis no painel são as mesmas para o monitoramento mínimo ou detalhado.

1.  No [Portal de Gerenciamento][], clique em **Armazenamento** e no nome da conta de armazenamento para abrir o painel.

2.  Para alterar as métricas que são traçadas no gráfico, realize uma das seguintes ações:

-   Para adicionar uma nova métrica ao gráfico, marque a caixa de seleção próxima ao cabeçalho da métrica. Em uma exibição estreita, clique em ***mais* n** para acessar cabeçalhos que não podem ser exibidos na área do cabeçalho.

-   Para ocultar uma métrica que seja traçada no gráfico, desmarque a caixa de seleção próxima ao cabeçalho da métrica.

    ![Monitoring\_nmore][]

1.  Por padrão, o gráfico mostra tendências, exibindo apenas o valor atual de cada métrica (a opção **Relativo** na parte superior do gráfico). Para exibir um eixo Y para que você possa visualizar os valores absolutos, selecione **Absoluto**.

2.  Para alterar o intervalo de tempo de exibição do gráfico de métricas, selecione 6 horas, 24 horas ou 7 dias na parte superior do gráfico.

## <span id="customizemonitorpage"></span></a>Como: Personalizar a página de Monitoramento

Na página de **Monitoramento**, você pode exibir o conjunto completo de métricas da sua conta de armazenamento.

-   Se sua conta de armazenamento tiver a configuração de monitoramento mínimo, métricas como entrada/saída, disponibilidade, latência e porcentagens de êxitos são agregadas aos serviços de Blob, Tabela e Fila.

-   Se sua conta de armazenamento tiver a configuração de monitoramento detalhado, as métricas estarão disponíveis em uma resolução maior das operações de armazenamento individuais, além dos agregados de nível de serviço.

Use os procedimentos a seguir para escolher quais métricas de armazenamento deverão ser exibidas nos gráficos de métricas e na tabela que são exibidos na página de **Monitoramento**. Essas configurações não afetam a coleta, agregação e armazenamento dos dados de monitoramento na conta de armazenamento.

## <span id="addmonitoringmetrics"></span></a>Como: adicionar métricas à tabela de métricas

1.  No [Portal de Gerenciamento][], clique em **Armazenamento** e no nome da conta de armazenamento para abrir o painel.

2.  Clique em **Monitoramento**.

    A página de **Monitoramento** é exibida. Por padrão, a tabela de métricas exibe um subconjunto de métricas que estão disponíveis para o monitoramento. A ilustração mostra a exibição de Monitoramento padrão para uma conta de armazenamento com a configuração de monitoramento detalhado para os três serviços. Use **Adicionar métricas** para selecionar as métricas que você deseja monitorar de todas as métricas disponíveis.

    ![Monitoring\_VerboseDisplay][]

    <div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>Considere os custos ao selecionar as m&eacute;tricas. Existem transa&ccedil;&otilde;es e custos de sa&iacute;da associados &agrave; atualiza&ccedil;&atilde;o das exibi&ccedil;&otilde;es do monitoramento. Para obter mais informa&ccedil;&otilde;es, consulte <a href="http://msdn.microsoft.com/en-us/library/windowsazure/hh360997.aspx">An&aacute;lise de armazenamento e cobran&ccedil;a</a>.</p> 
</div>

3.  Clique em **Adicionar Métricas**.

    As métricas agregadas que estão disponíveis no monitoramento mínimo estão localizadas na parte superior da lista. Se a caixa de seleção estiver selecionada, a métrica é exibida na lista de métricas.

    ![AddMetricsInitialDisplay][]

4.  Passe o mouse sobre lado direito da caixa de diálogo para exibir uma barra de rolagem que você pode arrastar para rolar e exibir métricas adicionais.

    ![AddMetricsScrollbar][]

5.  Clique na seta para baixo próxima a uma métrica para expandir uma lista das operações na qual a métrica é delimitada para incluir. Selecione cada uma das operações que você deseja exibir na tabela de métricas no Portal de Gerenciamento.

    Na ilustração a seguir, a métrica de PORCENTAGEM DE ERRO DE AUTORIZAÇÃO foi expandida.

    ![ExpandCollapse][]

6.  Depois que você selecionar métricas para todos os serviços, clique em OK (marca de seleção) para atualizar a configuração de monitoramento. As métricas selecionadas são adicionadas à tabela de métricas.

7.  Para excluir uma métrica da tabela, clique na métrica para selecioná-la e clique em **Excluir Métrica**, conforme demonstrado abaixo.

    ![DeleteMetric][]

## <span id="customizemetricschart"></span></a>Como: Personalizar o gráfico de métricas na página de Monitoramento

1.  Na página de **Monitoramento** da conta de armazenamento, na tabela de métricas, selecione até seis métricas para serem traçadas no gráfico de métricas. Para selecionar uma métrica, clique na caixa de seleção à esquerda. Para remover uma métrica do gráfico, desmarque a caixa de seleção.

2.  Para alternar o gráfico entre os valores relativos (somente valor final exibido) e os valores absolutos (eixo Y exibido), selecione **Relativo** ou **Absoluto** na parte superior do gráfico.

3.  Para alterar o intervalo de tempo de exibição do gráfico de métricas, selecione **1 hora**, **24 horas** ou **7 dias** na parte superior do gráfico.

## <span id="configurelogging"></span></a>Como: Configurar o registro em log

Para cada um dos serviços de armazenamento disponíveis com sua conta de armazenamento (Blob, Tabela e Fila), é possível salvar logs de diagnóstico para as Solicitações de Leitura, Solicitações de Gravação e/ou Solicitações de Exclusão e definir a política de retenção de dados para cada um dos serviços.

1.  No [Portal de Gerenciamento][], clique em **Armazenamento** e no nome da conta de armazenamento para abrir o painel.

2.  Clique em **Configurar** e use a seta para baixo no teclado para rolar para baixo até **Registro em log** (mostrado abaixo).

    ![Storagelogging][]

3.  Para cada um dos serviços (Blob, Tabela e Fila), configure o seguinte:

    -   Os tipos de solicitação a registrar em log: Solicitações de leitura, solicitações de gravação e solicitações de exclusão

    -   O número de dias pelos quais os dados registrados serão mantidos. Se não desejar definir uma política de retenção, digite zero. Se você não definir uma política de retenção, cabe a você excluir os registros.

4.  Clique em **Salvar**.

Os logs de diagnóstico são salvos em um contêiner de blob denominado $logs em sua conta de armazenamento. Para obter informações sobre como acessar o contêiner $logs, consulte [Sobre o Log de Analítica do Armazenamento][]

  [Análise de armazenamento e cobrança]: http://msdn.microsoft.com/en-us/library/windowsazure/hh360997.aspx
  [Como: Configurar o monitoramento para uma conta de armazenamento]: #configurestoragemonitoring
  [Como: Personalizar o painel para o monitoramento]: #customizestoragemonitoring
  [Como: Personalizar a página de Monitoramento]: #customizemonitorpage
  [Como: adicionar métricas à tabela de métricas]: #addmonitoringmetrics
  [Como: Personalizar o gráfico de métricas na página de Monitoramento]: #customizemetricschart
  [Como: Configurar o registro em log]: #configurelogging
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [Opções de monitoramento]: ./media/storage-monitor-storage-account/Storage_MonitoringOptions.png
  [Sobre as Métricas de Analítica de Armazenamento]: http://msdn.microsoft.com/en-us/library/windowsazure/hh343258.aspx
  [Monitoring\_nmore]: ./media/storage-monitor-storage-account/storage_Monitoring_nmore.png
  [Monitoring\_VerboseDisplay]: ./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png
  [AddMetricsInitialDisplay]: ./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png
  [AddMetricsScrollbar]: ./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png
  [ExpandCollapse]: ./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png
  [DeleteMetric]: ./media/storage-monitor-storage-account/Storage_DeleteMetric.png
  [Storagelogging]: ./media/storage-monitor-storage-account/Storage_LoggingOptions.png
  [Sobre o Log de Analítica do Armazenamento]: http://msdn.microsoft.com/en-us/library/windowsazure/hh343262.aspx
