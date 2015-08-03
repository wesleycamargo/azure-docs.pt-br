<properties 
	pageTitle="Como monitorar uma conta de armazenamento | Microsoft Azure" 
	description="Saiba como monitorar uma conta de armazenamento no Azure usando o Portal de Gerenciamento." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="tamram"/>

# Monitorar uma conta de armazenamento no Portal de Gerenciamento do Azure

## Visão geral

É possível monitorar sua conta de armazenamento no Portal de Gerenciamento do Azure. Quando você configurar sua conta de armazenamento para o monitoramento por meio do portal, o Armazenamento do Azure usa [Análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343270.aspx) para acompanhar métricas para sua conta e dados de solicitação de log.

> [AZURE.NOTE]Lembre-se de que há custos adicionais associados ao exame de dados de monitoramento no Portal de Gerenciamento. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">Análise de armazenamento e cobrança</a>. <br />

> O serviço de Arquivo do Azure não tem suporte para Análise de Armazenamento no momento.

> Para um guia aprofundado sobre como usar a Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao Armazenamento do Azure, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](../storage-monitoring-diagnosing-troubleshooting/).


## Como configurar o monitoramento para uma conta de armazenamento

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Armazenamento** e no nome da conta de armazenamento para abrir o painel.

2. Clique em **Configurar** e role para baixo até as configurações de **monitoramento** dos serviços de Blob, Tabela e Fila, mostrados a seguir.

	![Opções de monitoramento](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. Em **monitoramento**, defina o nível de monitoramento e a política de retenção de dados para cada serviço:

-  Para definir o nível de monitoramento, selecione uma das seguintes opções:

      **Mínimo** - Coleta métricas como entrada/saída, disponibilidade, latência e porcentagens de êxitos, que são agregadas aos serviços de Blob, Tabela e Fila.

      **Detalhado** - Além das métricas mínimas, coleta o mesmo conjunto de métricas para cada operação de armazenamento na API do Serviço de Armazenamento do Azure. As métricas no modo detalhado permitem uma análise mais próxima dos problemas que ocorrem durante operações de aplicativo.

      **Desativar** - Desativa o monitoramento. Os dados de monitoramento existentes são mantidos até o final do período de retenção.

- Para definir a política de retenção de dados, em **Retenção (em dias)**, digite o número de dias que os dados devem ser retidos, de 1 a 365 dias. Se não desejar definir uma política de retenção, digite zero. Se não houver nenhuma política de retenção, cabe a você excluir os dados de monitoramento. É recomendável configurar uma política de retenção com base em quanto tempo deseja manter os dados de analítica do armazenamento da sua conta para que os dados de analítica antigos e não utilizados possam ser excluídos pelo sistema sem custo adicional.

4. Ao concluir a configuração do monitoramento, clique em **Salvar**.

Você deve começar a visualizar o monitoramento de dados no painel e a página de **Monitoramento** após cerca de uma hora.

Até que você configure o monitoramento para uma conta de armazenamento, nenhum dado de monitoramento é coletado e os gráficos de métricas no painel e na página **Monitorar** ficam vazios.

Depois de definir os níveis de monitoramento e as políticas de retenção, você pode escolher quais das métricas disponíveis serão monitoradas no Portal de Gerenciamento e quais métricas serão traçadas nos gráficos de métricas. Um conjunto de métricas padrão é exibido em cada nível de monitoramento. Você pode usar **Adicionar métricas** para adicionar ou remover métricas da lista de métricas.

As métricas são armazenadas na conta de armazenamento em quatro tabelas intituladas $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue e $MetricsCapacityBlob. Para obter mais informações, consulte [Sobre as Métricas de Analítica de Armazenamento](http://msdn.microsoft.com/library/azure/hh343258.aspx).


## Como personalizar o painel para o monitoramento

No painel, você pode escolher até seis métricas para serem traçadas no gráfico de métricas de nove métricas disponíveis. Para cada serviço (Blob, Tabela e Fila), as métricas de Disponibilidade, Porcentagem de Êxitos e Total de Solicitações estão disponíveis. As métricas disponíveis no painel são as mesmas para o monitoramento mínimo ou detalhado.

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Armazenamento** e no nome da conta de armazenamento para abrir o painel.

2. Para alterar as métricas que são traçadas no gráfico, realize uma das seguintes ações:

	- Para adicionar uma nova métrica ao gráfico, clique na caixa de seleção colorida ao lado do cabeçalho da métrica na tabela abaixo do gráfico.
	
	- Para ocultar uma métrica que esteja plotada no gráfico, desmarque a caixa de seleção colorida ao lado do cabeçalho da métrica.
	
		![Monitoring_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)
  
3. Por padrão, o gráfico mostra tendências, exibindo apenas o valor atual de cada métrica (a opção **Relativo** na parte superior do gráfico). Para exibir um eixo Y para que você possa visualizar os valores absolutos, selecione **Absoluto**.

4. Para alterar o intervalo de tempo de exibição do gráfico de métricas, selecione 1 hora, 24 horas ou 7 dias na parte superior do gráfico.
     

## Como personalizar a página de Monitoramento

Na página de**Monitoramento**, você pode exibir o conjunto completo de métricas da sua conta de armazenamento.

- Se sua conta de armazenamento tiver a configuração de monitoramento mínimo, métricas como entrada/saída, disponibilidade, latência e porcentagens de êxitos são agregadas aos serviços de Blob, Tabela e Fila.

- Se sua conta de armazenamento tiver a configuração de monitoramento detalhado, as métricas estarão disponíveis em uma resolução maior das operações de armazenamento individuais, além dos agregados de nível de serviço.

Use os procedimentos a seguir para escolher quais métricas de armazenamento deverão ser exibidas nos gráficos de métricas e na tabela que são exibidos na página de **Monitoramento**. Essas configurações não afetam a coleta, agregação e armazenamento dos dados de monitoramento na conta de armazenamento.

## Tutorial: Adicionar métricas à tabela de métricas


1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Armazenamento** e no nome da conta de armazenamento para abrir o painel.

2. Clique em **Monitoramento**.

	A página de **Monitoramento** é exibida. Por padrão, a tabela de métricas exibe um subconjunto de métricas que estão disponíveis para o monitoramento. A ilustração mostra a exibição de Monitoramento padrão para uma conta de armazenamento com a configuração de monitoramento detalhado para os três serviços. Use **Adicionar métricas** para selecionar as métricas que você deseja monitorar de todas as métricas disponíveis.


	![Monitoring_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

	> [AZURE.NOTE]Considere os custos ao selecionar as métricas. Existem transações e custos de saída associados à atualização das exibições do monitoramento. Para obter mais informações, consulte [Análise de armazenamento e cobrança](http://msdn.microsoft.com/library/azure/hh360997.aspx).

3. Clique em **Adicionar Métricas**.

	As métricas agregadas que estão disponíveis no monitoramento mínimo estão localizadas na parte superior da lista. Se a caixa de seleção estiver selecionada, a métrica é exibida na lista de métricas.

	![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)
 
4. Passe o mouse sobre lado direito da caixa de diálogo para exibir uma barra de rolagem que você pode arrastar para rolar e exibir métricas adicionais.

	![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. Clique na seta para baixo próxima a uma métrica para expandir uma lista das operações na qual a métrica é delimitada para incluir. Selecione cada uma das operações que você deseja exibir na tabela de métricas no Portal de Gerenciamento.

	Na ilustração a seguir, a métrica de PERCENTUAL DE ERRO DE AUTORIZAÇÃO foi expandida.

	![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. Depois que você selecionar métricas para todos os serviços, clique em OK (marca de seleção) para atualizar a configuração de monitoramento. As métricas selecionadas são adicionadas à tabela de métricas.

7. Para excluir uma métrica da tabela, clique na métrica para selecioná-la e clique em **Excluir Métrica**, conforme demonstrado abaixo.

	![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## Como personalizar o gráfico de métricas na página de Monitoramento

1. Na página de **Monitoramento** da conta de armazenamento, na tabela de métricas, selecione até seis métricas para serem traçadas no gráfico de métricas. Para selecionar uma métrica, clique na caixa de seleção à esquerda. Para remover uma métrica do gráfico, desmarque a caixa de seleção.

2. Para alternar o gráfico entre os valores relativos (somente valor final exibido) e os valores absolutos (eixo Y exibido), selecione **Relativo** ou **Absoluto** na parte superior do gráfico.

3.	Para alterar o intervalo de tempo de exibição do gráfico de métricas, selecione **1 hora**, **24 horas** ou **7 dias** na parte superior do gráfico.



## Como configurar o log

Para cada um dos serviços de armazenamento disponíveis com sua conta de armazenamento (Blob, Tabela e Fila), é possível salvar logs de diagnóstico para as Solicitações de Leitura, Solicitações de Gravação e/ou Solicitações de Exclusão e definir a política de retenção de dados para cada um dos serviços.

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Armazenamento** e no nome da conta de armazenamento para abrir o painel.

2. Clique em **Configurar** e use a seta para baixo no teclado para rolar para baixo até **Registro em log** (mostrado abaixo).

	![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)

 
3. Para cada um dos serviços (Blob, Tabela e Fila), configure o seguinte:

	- Os tipos de solicitação que serão registradas: Solicitações de Leitura, Solicitações de Gravação e Solicitações de Exclusão

	- O número de dias pelos quais os dados registrados serão mantidos. Se não desejar definir uma política de retenção, digite zero. Se você não definir uma política de retenção, cabe a você excluir os registros.

4. Clique em **Salvar**.

Os logs de diagnóstico são salvos em um contêiner de blob denominado $logs em sua conta de armazenamento. Para obter informações sobre como acessar o contêiner $logs, consulte [Sobre o Log de Analítica do Armazenamento](http://msdn.microsoft.com/library/azure/hh343262.aspx)
 

<!---HONumber=July15_HO4-->