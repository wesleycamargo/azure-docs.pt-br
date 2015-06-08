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
Este artigo descreve vários cenários para usar o Portal de visualização do Azure para monitorar e gerenciar a fábrica de dados do Azure.

## <a name="AllDataFactories"></a> Exibir todas as fábricas de dados em uma assinatura do Azure

- Entrar para a [Portal de visualização do Azure][azure-preview-portal].
- Clique em **Procurar** hub esquerda e clique **fábricas de dados**.  

	![Procurar hub -> fábricas de dados][image-data-factory-browse-datafactories]

	Se você não vir **fábricas de dados**, clique em **tudo** e, em seguida, clique em **factorries de dados** no **Procurar** lâmina.

	![Procurar hub -> tudo][image-data-factory-browse-everything]

- Você deve ver todas as fábricas de dados no **fábricas de dados** lâmina.

	![Blade de fábricas de dados][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Exibir detalhes sobre uma fábrica de dados

Para exibir detalhes sobre uma data factory, siga um destes procedimentos:


- Clique em uma fábrica de dados no **fábricas de dados** lâmina mostrada acima.
- Clique no link para a fábrica de dados sobre o **Startboard**. **Startboard** a lâmina que você vê quando você fazer logon no portal de visualização do Azure. Se você tiver selecionado **Adicionar ao quadro inicial** durante a criação de uma fábrica de dados (opção padrão), você deve ver a fábrica de dados vinculados no quadro inicial, conforme mostrado na imagem a seguir. Neste exemplo, **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** e **LogProcessingFactory** links de fábrica de dados estão disponíveis na **Startboard**.


![Fábrica de dados do quadro inicial][image-data-factory-datafactory-from-startboard]

De qualquer forma, você verá o **DATA FACTORY** blade da fábrica de dados selecionado, conforme mostrado na imagem a seguir.

 ![Página inicial da fábrica de dados][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Modo de exibição de diagrama da fábrica de dados
No **DATA FACTORY** blade da fábrica de dados, clique em **diagrama** lado a lado para ver a exibição do diagrama da fábrica de dados.

![Exibição de diagrama de fábrica de dados][image-data-factory-diagram-view]
 
### Abrir um pipeline na exibição de diagrama
Você pode exibir todas as atividades em um pipeline clicando o pipeline na exibição de diagrama e, em seguida, clicando em **Abrir Pipeline**. Você deve ver as atividades no pipeline com conjuntos de dados de entrada e saídos para as atividades. ![Pipeline aberto](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

Clique em **Data factory** na navegação estrutural no canto superior esquerdo para voltar para o modo de exibição de diagrama. O modo de exibição de diagrama exibe todos os pipelines. Neste exemplo, somente você ter criado um pipeline.

## <a name="DataFactoryLinkedServices"></a> Exibir serviços vinculados em uma fábrica de dados
No **DATA FACTORY** blade da fábrica de dados, clique em **Serviços vinculados** lado a lado para ver todos os serviços vinculados em uma lista.

![Lâmina de serviços vinculados][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Exibir detalhes sobre um serviço vinculado
No **Serviços vinculados** lâmina, clique o serviço vinculado na lista para ver detalhes sobre ele.

![Lâmina de serviço vinculada][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Exibir conjuntos de dados em uma fábrica de dados 
No **DATA FACTORY** blade da fábrica de dados, clique em **conjuntos de dados** lado a lado para ver todas as tabelas na fábrica dados.

![Blade de conjuntos de dados][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> Exibir detalhes sobre um conjunto de dados
Clique na lista de conjuntos de dados na folha CONJUNTOS DE DADOS para visualizar os detalhes sobre o conjunto de dados. Observe que uma tabela é um conjunto de dados retangular que tem um esquema. É o único tipo de conjunto de dados com suporte neste momento.

![Blade de tabela][image-data-factory-table]

No **tabela** lâmina acima, ambos **atualizado recentemente fatias** e **recentemente falha fatias** listas são classificadas pelo **hora da última atualização**. O tempo de atualização de uma fatia é alterado nas seguintes situações.

-  Você atualiza o status da fatia manualmente, por exemplo, usando o **conjunto AzureDataFactorySliceStatus** (ou) clicando em **executar** no **FATIA** blade da fatia.
-  A fatia é alterado devido a uma execução (por exemplo, uma execução de Introdução, uma execução finalizada e falha, uma execução terminou e foi bem-sucedida, etc).
 
	
Para exibir as fatias de dados classificadas pelas horas de início/término fatia em vez disso, clique em **fatias de dados (por hora fatia)** lado a lado.
 
![Fatias de dados por intervalo de tempo][DataSlicesBySliceTime]

Clique no título da lista ou **... (reticências)** Para ver a lista de intervalos de maior.

![Todas as fatias de uma tabela][image-data-factory-all-slices]

No **fatias de dados** lâmina, clique o **filtro** para ver o **filtro** blade que permite que você **filtro** fatias para ver as fatias específicas que você deseja analisar. Você verá a lâmina semelhante ao apresentado a seguir quando você clica em **filtro** no **fatias de dados** lâmina com fatias **classificadas por hora de atualização**.

![Blade de filtro][image-data-factory-filter-blade]

O **filtro** lâmina permite filtrar com base em **atualizada pela última vez** e **fatiar status**. A tabela a seguir descreve todos os status de fatia e sua descrição.
 
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
RetryValidation | Repetindo a validação da fatia.
FailedValidation | Falha na validação da fatia.
LongRetry | Uma fatia será esse status se LongRetry for especificado na tabela JSON e repetições regulares da fatia falharem.
ValidationInProgress | A validação da fatia (com base em políticas definidas na tabela JSON) está sendo executada.

Quando você clica em **filtro** no **fatias de dados** lâmina com fatias **classificados por tempo a fatia**, você verá um tipo diferente de **filtro** lâmina.

![Blade de filtro 2][image-data-factory-filter-blade-2]


Quando você iniciar o **filtro** lâmina, o **para** campo será definido automaticamente como a hora mais recente (arredondada) para limitar o número de registros retornados. O **de** campo é automaticamente configurado assim. Você pode alterar o **de** data clicando o **calendário** botão. O **para** data será alterada automaticamente quando você altera o **de** Data.

Você pode clicar em **anterior**/ * * botões Avançar * * para exibir fatias no anterior período/próximo período. O intervalo de tempo **anterior** e **próximo** botões é definido com base na frequência fatia e intervalo conforme mostrado na tabela a seguir.

Frequência | Intervalo de valores de intervalo | Parte de hora resultante
----------| -------------------- | --------------------
Minuto | 1-4 | 6 horas
Minuto | 5-29 | 1 dia
Minuto | 30-180 | 7 dias
Minuto | 180 + | 28 dias (aproximado. mês)
Hora | 1-3 | 7 dias
Hora | 4-11 | 28 dias (aproximado. mês)
Hora | 12 a 72 | 182 dias (aproximados. 6 meses)
Hora | 73 + | 1 ano
Dia | 1-6 | 1 ano
Dia | 7-20 | 5 anos
Dia | 21 + | 10 anos
Semana | 1-3 | 5 anos
Semana | 4 + | 10 anos
Mês | qualquer | 10 anos
 
Por exemplo, se você definir **freqüência** como **hora** e **intervalo** de **2**, clicando o **próximo**/ * * anterior * * botões Mover o intervalo de tempo **7 dias** em qualquer direção. Essa lógica se aplica a folha de filtro se você estiver exibindo todas as fatias fatias/problema fatias/recentes.




## <a name="DataFactorySlice"></a> Exibir detalhes sobre uma fatia
Clique em uma fatia na lista de fatias no **tabela** lâmina ou **fatias de dados** lâmina para ver detalhes sobre essa fatia.

![Fatia de dados][image-data-factory-dataslice]

Se a fatia não está no **pronto** estado, você pode ver as fatias upstream que não estão prontos e estão bloqueando a fatia atual da execução no **fatias Upstream que não estão prontas** lista.

### <a name="DataFactoryActivtyRuns"></a> Exibir todas as execuções de atividade para uma fatia
Para uma fatia, pode haver mais de uma execução. Por exemplo, quando uma fatia falha, o serviço pode tentar novamente por algumas vezes. Também é possível executar novamente uma fatia que falhou em todas as tentativas. Você pode ver todas a atividade é executada no * * fatia de dados * * lâmina na lista na parte inferior.

## <a name="DataFactoryActivtyRunDetails"></a> Exibir detalhes sobre uma atividade execute
Clique em uma atividade seja executada na lista de execuções no **fatia de dados** lâmina para ver detalhes sobre a atividade seja executada.

![Detalhes da execução de atividade][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Operações Lente - eventos da semana anterior
No **DATA FACTORY** blade (ou página inicial) para a fábrica de dados, clique em **eventos da semana anterior** na **operações** lente para ver os eventos da semana anterior. Isso ajuda a ter uma visão geral das operações realizadas pelo data factory na última semana. Também ajuda a solucionar quaisquer erros com o processamento/movimentação de dados.

![Eventos de fábrica de dados][image-data-factory-events]


## Consulte também

Artigo | Descrição
------ | ---------------
[Monitorar e gerenciar um Azure Data Factory usando o PowerShell][monitor-manage-using-powershell] | Este artigo descreve como monitorar uma fábrica de dados do Azure usando cmdlets do PowerShell do Azure. 


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

<!---HONumber=GIT-SubDir-->