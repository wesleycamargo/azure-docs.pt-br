<properties 
	pageTitle="Monitorar e gerenciar o Azure Data Factory utilizando o Portal de visualização do Azure" 
	description="Saiba como usar o portal de gerenciamento do Azure para monitorar e gerenciar as fábricas de dados do Azure que você criou." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Monitorar o Data Factory do Azure utilizando o Portal de visualização do Azure
Este artigo descreve diversos cenários de uso do Portal de Visualização do Azure para monitorar e gerenciar a Azure Data Factory.

## <a name="AllDataFactories"></a> Exibir todas as data factories em uma assinatura do Azure

- Entre no [Portal de Visualização do Azure][azure-preview-portal].
- Clique no hub **PROCURAR** à esquerda e clique em **Data factories**.  

	![Hub PROCURAR -> Data Factories][image-data-factory-browse-datafactories]

	Caso não visualize as **Data factories**, clique em **Tudo** e, em seguida, clique em **Data factories** na folha **Procurar**.

	![Hub PROCURAR -> Tudo][image-data-factory-browse-everything]

- Você deve ver todas as data factories na folha **Data factories**.

	![Folha Data factories][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Exibir detalhes sobre uma data factory

Para exibir detalhes sobre uma data factory, siga um destes procedimentos:


- Clique em uma data factory na folha **Data factories** mostrada acima.
- Clique no link da data factory no **Quadro Inicial**. O **Quadro Inicial** é a folha exibida quando você faz logon no Portal de Visualização do Azure. Se tiver selecionado **Adicionar ao Quadro Inicial** durante a criação de uma data factory (opção padrão), você deverá ver o link da data factory no Quadro Inicial, conforme mostrado na imagem a seguir. Neste exemplo, os links de data factory **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** e **LogProcessingFactory** estão disponíveis no **Quadro Inicial**.


![Data factory do Quadro Inicial][image-data-factory-datafactory-from-startboard]

De qualquer forma, você verá a folha **DATA FACTORY** da data factory selecionada, conforme mostrado na imagem a seguir.

 ![Página inicial da Data Factory][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Exibir modo de exibição de diagrama da data factory
Na folha **DATA FACTORY** da data factory, clique no bloco **Diagrama** para ver o modo de exibição de diagrama da data factory.

![Modo de Exibição de Diagrama da Data Factory][image-data-factory-diagram-view]
 
### Abrir um pipeline no Modo de Exibição de Diagrama
É possível exibir todas as atividades em um pipeline clicando com o botão direito do mouse no pipeline no Modo de Exibição de Diagrama e, em seguida, clicando em **Abrir Pipeline**. Você deve ver as atividades no pipeline juntamente com conjuntos de dados de entrada e saída das atividades. ![Pipeline aberto](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

Clique em **Data factory** na trilha do canto superior esquerdo para voltar para o modo de exibição de diagrama. O modo de exibição de diagrama exibe todos os pipelines. Neste exemplo, você criou somente um pipeline.

## <a name="DataFactoryLinkedServices"></a> Exibir serviços vinculados em uma data factory
Na folha **DATA FACTORY** da data factory, clique no bloco **Serviços Vinculados** para visualizar todos os serviços vinculados em uma lista.

![Folha Serviços Vinculados][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Exibir detalhes sobre um serviço vinculado
Na folha **SERVIÇOS VINCULADOS**, clique no serviço vinculado na lista para visualizar detalhes sobre ele.

![Folha Serviço Vinculado][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Exibir conjuntos de dados em uma data factory 
Na folha **DATA FACTORY** da data factory, clique no bloco **Conjunto de dados** para visualizar todas as tabelas na data factory.

![Folha Conjuntos de Dados][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> Exibir detalhes sobre um conjunto de dados
Clique na lista de conjuntos de dados na folha CONJUNTOS DE DADOS para visualizar os detalhes sobre o conjunto de dados. Observe que uma tabela é um conjunto de dados retangular que tem um esquema. É o único tipo de conjunto de dados com suporte neste momento.

![Folha Tabela][image-data-factory-table]

Na folha **TABELA** acima, ambas as listas **Fatias atualizadas recentemente** e **Fatias com falha recente** são classificadas pela **HORA DA ÚLTIMA ATUALIZAÇÃO**. A hora de atualização de uma fatia é alterada nas situações a seguir.

-  Você atualiza o status da fatia manualmente, por exemplo, usando o **Set-AzureDataFactorySliceStatus** (ou) clicando em **EXECUTAR** na folha **FATIA** da fatia.
-  A fatia é alterada devido a uma execução (por exemplo, uma execução iniciada, uma execução finalizada e com falha, uma execução finalizada e bem-sucedida, etc).
 
	
Para exibir as fatias de dados classificadas pelas horas de início/término da fatia, clique no bloco **Fatias de dados (por hora da fatia)**.
 
![Fatias de Dados por Hora da Fatia][DataSlicesBySliceTime]

Clique no título das listas ou em **... (reticências)** para ver a lista maior de fatias.

![Todas as fatias de uma tabela][image-data-factory-all-slices]

Na folha **Fatias de Dados**, clique no botão **Filtro** para ver a folha **Filtro** que permite **filtrar** as fatias para ver as fatias específicas que deseja analisar. Você verá a folha semelhante à seguinte quando clicar em **Filtro** na folha **Fatias de Dados** com fatias **classificadas por hora de atualização**.

![Folha Filtro][image-data-factory-filter-blade]

A folha **Filtro** permite filtrar com base na **hora da última atualização** e no **status da fatia**. A tabela a seguir descreve todos os status de fatia e suas descrições.
 
Status da fatia | Descrição
------------ | ------------
PendingExecution | O processamento de dados ainda não foi iniciado.
InProgress | O processamento de dados está em andamento.
Ready | O processamento de dados foi concluído e a fatia de dados está pronta.
Failed | Falha na execução da execução que produz a fatia.
Skip | Ignorar o processamento da fatia.
Retry | Repetir a execução que produz a fatia.
Timed Out | O processamento de dados da fatia expirou.
PendingValidation | A fatia de dados está aguardando a validação em relação as políticas de validação antes de serem processadas.
RetryValidation | Repetir a validação da fatia.
FailedValidation | Falha na validação da fatia.
LongRetry | Uma fatia será esse status se LongRetry for especificado na tabela JSON e repetições regulares da fatia falharem.
ValidationInProgress | A validação da fatia (com base em políticas definidas na tabela JSON) está sendo executada.

Ao clicar em **Filtro** na folha **Fatias de Dados** com fatias **classificadas por hora da fatia**, você vê um tipo diferente da folha **Filtro**.

![Folha Filtro 2][image-data-factory-filter-blade-2]


Ao iniciar a folha **Filtro**, o campo **Até** é definido automaticamente com a hora mais recente (arredondada) para limitar o número de registros retornados. O campo **A partir de** também é configurado automaticamente. Você pode alterar a data **A partir de** clicando no botão **Calendário**. A data **Até** será alterada automaticamente quando você alterar a data **A partir de**.

É possível clicar nos botões **Anterior**/**Próximo** para exibir as fatias no período anterior/próximo período. O intervalo de tempo dos botões **Anterior** e **Próximo** é definido com base na frequência e no intervalo da fatia, conforme mostrado na tabela a seguir.

Frequência | Faixa de valores de intervalo | Período de tempo resultante
----------| -------------------- | --------------------
Minuto | 1-4 | 6 horas
Minuto | 5-29 | 1 dia
Minuto | 30-180 | 7 dias
Minuto | Mais de 180 | 28 dias (aproximadamente mês do calendário)
Hora | 1-3 | 7 dias
Hora | 4-11 | 28 dias (aproximadamente mês do calendário)
Hora | 12 a 72 | 182 dias (aproximadamente 6 meses)
Hora | Mais de 73 | 1 ano
Dia | 1-6 | 1 ano
Dia | 7-20 | 5 anos
Dia | Mais de 21 | 10 anos
Semana | 1-3 | 5 anos
Semana | Mais de 4 | 10 anos
Mês | qualquer | 10 anos
 
Por exemplo, se você definir **frequency** como **Hour** e **interval** como **2**, clicar nos botões **Próximo**/**Anterior** mudará o intervalo de tempo em **7 dias**, em qualquer direção. Essa lógica se aplica a folha de filtro se você estiver exibindo todas as fatias fatias/problema fatias/recentes.




## <a name="DataFactorySlice"></a> Exibir detalhes sobre uma fatia
Clique em uma fatia na lista de fatias da folha **TABELA** ou da folha **Fatias de Dados** para visualizar detalhes sobre essa fatia.

![Fatia de Dados][image-data-factory-dataslice]

Quando a fatia não está no estado **Pronto**, você pode ver as fatias upstream que não estão Prontas e estão impedindo a execução da fatia atual na lista **Fatias upstream que não estão prontas**.

### <a name="DataFactoryActivtyRuns"></a> Exibir todas as execuções de atividade de uma fatia
Para uma fatia, pode haver mais de uma execução. Por exemplo, quando uma fatia falha, o serviço pode tentar novamente por algumas vezes. Também é possível executar novamente uma fatia que falhou em todas as tentativas. É possível visualizar toda as execuções de atividade na folha **Fatia de Dados** na lista da parte inferior.

## <a name="DataFactoryActivtyRunDetails"></a> Exibir detalhes sobre uma execução de atividade
Clique em uma execução de atividade na lista de execuções na folha **Fatia de Dados** para visualizar os detalhes sobre a execução da atividade.

![Detalhes da execução da atividade][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Lentes de Operações — Eventos da semana anterior
Na folha **DATA FACTORY** (ou página inicial) da data factory, clique em **Eventos da semana anterior** em Lentes de **Operações** para ver os eventos da semana anterior. Isso ajuda a ter uma visão geral das operações realizadas pelo data factory na última semana. Também ajuda a solucionar quaisquer erros com o processamento/movimentação de dados.

![Eventos de Data Factory][image-data-factory-events]


## Consulte também

Artigo | Descrição
------ | ---------------
[Monitorar e gerenciar a Azure Data Factory usando o PowerShell][monitor-manage-using-powershell] | Este artigo descreve como monitorar uma Azure Data Factory usando cmdlets do PowerShell do Azure. 


[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

[image-data-factory-filter-blade-2]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade2.png


[image-data-factory-browse-everything]: ./media/data-factory-monitor-manage-using-management-portal/BrowseEverything.png

[image-data-factory-browse-datafactories]: ./media/data-factory-monitor-manage-using-management-portal/BrowseDataFactories.png

[image-data-factory-datafactories-blade]: ./media/data-factory-monitor-manage-using-management-portal/DataFactories.png

[image-data-factory-datafactory-from-startboard]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryFromStartboard.png

[image-data-factory-datafactory-home-page]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryHomePage.png

[image-data-factory-diagram-view]: ./media/data-factory-monitor-manage-using-management-portal/DiagramView.png

[image-data-factory-linked-services]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServicesBlade.png

[image-data-factory-linked-service]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServiceBlade.png

[image-data-factory-datasets]: ./media/data-factory-monitor-manage-using-management-portal/Datasets.png

[image-data-factory-table]: ./media/data-factory-monitor-manage-using-management-portal/Table.png

[image-data-factory-all-slices]: ./media/data-factory-monitor-manage-using-management-portal/AllSlices.png

[image-data-factory-filter]: ./media/data-factory-monitor-manage-using-management-portal/Filter.png

[image-data-factory-dataslice]: ./media/data-factory-monitor-manage-using-management-portal/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-monitor-manage-using-management-portal/ActivityRunDetails.png

[image-data-factory-events]: ./media/data-factory-monitor-manage-using-management-portal/Events.png
[DataSlicesBySliceTime]: ./media/data-factory-monitor-manage-using-management-portal/DataSlicesBySliceTime.png

<!---HONumber=July15_HO3-->