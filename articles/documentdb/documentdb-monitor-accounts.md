<properties
	pageTitle="Monitorar as solicitações e armazenamento do Banco de Dados de Documentos | Microsoft Azure"
	description="Saiba como monitorar sua conta do Banco de Dados de Documentos para métricas de desempenho, como solicitações e erros de servidor, e as métricas de uso, como o consumo de armazenamento."
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2016"
	ms.author="mimig"/>

# Monitorar as solicitações, uso e armazenamento do Banco de Dados de Documentos

Você pode monitorar as contas do seu DocumentDB no [portal do Azure](https://portal.azure.com/). Para cada conta do Banco de Dados de Documentos, tanto as métricas de desempenho (como as solicitações e erros de servidor) quanto as métricas de utilização (como consumo de armazenamento) estão disponíveis.

## Visualizar métricas de desempenho no portal 
1.	Em uma nova janela, abra o [portal do Azure](https://portal.azure.com/), clique em **Mais Serviços**, **DocumentDB (NoSQL)** e clique no nome da conta do DocumentDB cujas métricas de desempenho você deseja exibir.
2.	A lente de **Monitoramento** exibe os seguintes blocos por padrão:
	*	Solicitações totais para o dia de hoje.
	*	Armazenamento usado.

	Se sua tabela exibir **Não há dados disponíveis** e você acreditar que há dados no banco de dados, confira a seção [Solucionando problemas](#troubleshooting).

	![Captura de tela da lente de Monitoramento que mostra as solicitações e o uso de armazenamento](./media/documentdb-monitor-accounts/documentdb-total-requests-and-usage.png)


3.	Clicar no bloco **Solicitações** ou **Armazenamento** abre uma folha **Métrica** detalhada.
4.	A folha **Métrica** mostra detalhes das métricas que você selecionou. Na parte superior da folha há um gráfico de solicitações por hora e, abaixo dele, há uma tabela que mostra os valores de agregação para solicitações limitadas e totais. A folha Métrica também exibe a lista de alertas que foram definidos, filtrados para as métricas que aparecem na folha de métricas atual (desse modo, se você possui um determinado número de alertas, você verá apenas aqueles relevantes apresentados aqui).

	![Captura de tela da folha Métricas que inclui solicitações limitadas](./media/documentdb-monitor-accounts/documentdb-metric-blade.png)


## Personalizar exibições de métricas de desempenho no portal

1.	Para personalizar as métricas exibidas em um gráfico específico, clique no gráfico para abri-l-o na folha **Métrica** e clique em **Editar gráfico**. ![Captura de tela dos controles da folha Métricas, com Editar gráfico realçado](./media/documentdb-monitor-accounts/madocdb3.png)

2.	Na folha **Editar Gráfico**, há opções para modificar as métricas que são exibidas no gráfico, além de seu intervalo de tempo. ![Captura de tela da folha Editar gráfico](./media/documentdb-monitor-accounts/madocdb4.png)

3.	Para alterar as métricas exibidas na parte, basta marcar ou desmarcar as métricas de desempenho disponíveis e clicar em **OK** na parte inferior da folha.
4.	Para alterar o intervalo de tempo, escolha um intervalo diferente (por exemplo, **Personalizado**) e clique em **OK** na parte inferior da folha.

	![Captura de tela da parte do Intervalo de Tempo da folha Editar gráfico mostrando como inserir um intervalo de tempo personalizado](./media/documentdb-monitor-accounts/madocdb5.png)


## Criar gráficos lado a lado no portal
O Portal do Azure permite que você crie gráficos de métricas lado a lado.

1.	Primeiramente, clique com o botão direito do mouse no gráfico que você deseja copiar e escolha **Personalizar**.

	![Captura de tela do gráfico Total de Solicitações com a opção Personalizar realçada](./media/documentdb-monitor-accounts/madocdb6.png)

2.	Clique em **Clonar** no menu para copiar a parte e clique em **Personalização concluída**.

	![Captura de tela do gráfico Total de Solicitações com as opções de personalização Clonar e Concluído realçadas](./media/documentdb-monitor-accounts/madocdb7.png)


Agora, você pode tratar essa parte como qualquer outra parte de métrica, personalizando as métricas e o intervalo de tempo exibidos na parte. Fazendo isso, você pode ver um gráfico com duas métricas diferentes lado a lado simultaneamente. ![Captura de tela do gráfico Total de Solicitações e o novo gráfico Total de solicitações na última hora](./media/documentdb-monitor-accounts/madocdb8.png)

## Configurar alertas no Portal
1.	No [portal do Azure](https://portal.azure.com/), clique em **Mais Serviços**, em **DocumentDB (NoSQL)** e no nome da conta do DocumentDB para a qual deseja definir alertas de métricas de desempenho.

2.	No menu de recursos, clique em **Regras de Alerta** para abrir a folha Regras de alerta. ![Captura de tela da parte de Regras de alerta selecionada](./media/documentdb-monitor-accounts/madocdb10.5.png)

3.	Na folha **Regras de alerta**, clique em **Adicionar Alerta**. ![Captura de tela da folha Regras de Alerta, com o botão Adicionar alerta realçado](./media/documentdb-monitor-accounts/madocdb11.png)

4.	Na lâmina **Adicionar uma regra de alerta**, especifique:
	*	O nome da regra de alerta que você está configurando.
	*	Uma descrição da nova regra de alerta.
	*	A métrica para a regra de alerta.
	*	A condição, o limite e o período que determinam quando o alerta é ativado. Por exemplo, uma contagem de erros de servidor superior a cinco nos últimos 15 minutos.
	*	Se o administrador de serviços e seus coadministradores recebem ou não emails quando o alerta é enviado.
	*	Endereços de email adicionais para notificações de alerta. ![Captura de tela da folha da regra Adicionar um alerta](./media/documentdb-monitor-accounts/madocdb12.png)

## Monitorar programaticamente o Banco de Dados de Documentos
As métricas no nível da conta disponíveis no portal, como o uso de armazenamento da conta e o total de solicitações, não estão disponíveis por meio das APIs do Banco de Dados de Documentos. No entanto, você pode recuperar os dados de uso no nível da coleção usando as APIs do Banco de Dados de Documentos. Para recuperar os dados do nível de coleção, faça o seguinte:

- Para usar a API REST, [execute um GET na coleção](https://msdn.microsoft.com/library/mt489073.aspx). As informações de cota e de uso da coleção são retornadas nos cabeçalhos x-ms-resource-quota e x-ms-resource-usage na resposta.
- Para usar o SDK do .NET, use o método [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), que retorna uma [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) que contém diversas propriedades de uso como **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** e muito mais.

Para acessar outras métricas, use o [SDK do Azure Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights). As definições de métricas disponíveis podem ser recuperadas chamando:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08 

As consultas para recuperar métricas individuais usam o seguinte formato:

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Para saber mais, confira [Retrieving Resource Metrics via the Azure Insights API (Recuperação de métricas de recursos por meio da API do Azure Insights)](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/).

## Solucionar problemas
Se seus blocos de monitoramento exibirem a mensagem **Não há dados disponíveis** e você enviou solicitações ou adicionou dados ao banco de dados recentemente, é possível editar o bloco para refletir o uso recente.

### Editar um bloco para atualizar os dados atuais
1.	Para personalizar as métricas exibidas em uma parte específica, clique no gráfico para abri-lo na folha **Métrica** e clique em **Editar Gráfico**. ![Captura de tela dos controles da folha Métricas, com Editar gráfico realçado](./media/documentdb-monitor-accounts/madocdb3.png)

2.	Na folha **Editar Gráfico**, na seção **Intervalo de Tempo**, clique em **última hora** e em **OK**. ![Captura de tela da folha Editar Gráfico com a última hora selecionada](./media/documentdb-monitor-accounts/documentdb-no-available-data-past-hour.png)


3.	Seu bloco agora deve ser atualizado para mostrar seus dados atuais e o uso. ![Captura de tela de atualizada do bloco Total de solicitações na última hora atualizado](./media/documentdb-monitor-accounts/documentdb-no-available-data-fixed.png)

## Próximas etapas
Para saber mais sobre a capacidade do Banco de Dados de Documentos, consulte [Gerenciar a capacidade do Banco de Dados de Documentos](documentdb-manage.md).

<!---HONumber=AcomDC_0831_2016-->