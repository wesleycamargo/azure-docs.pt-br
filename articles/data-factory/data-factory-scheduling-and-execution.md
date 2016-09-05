<properties 
	pageTitle="Agendamento e execução com o Data Factory" 
	description="Aprenda sobre os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory." 
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
	ms.date="08/22/2016" 
	ms.author="spelluru"/>

# Agendamento e execução com o Data Factory
  
Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. Este artigo se baseia nos artigos [Criando pipelines](data-factory-create-pipelines.md) e [Criando conjuntos de dados](data-factory-create-datasets.md) e presume que você compreenda as noções básicas sobre conceitos de modelo de aplicativo do data factory: atividade, pipelines, serviços vinculados e conjuntos de dados.

## Agendando atividades

Na seção **Agendador** na atividade JSON, você pode especificar um agendamento recorrente para a atividade. Por exemplo, você pode agendar uma atividade a cada hora, da seguinte maneira:

	"scheduler": {
		"frequency": "Hour",
	    "interval": 1
	},  
    
![Exemplo do Agendador](./media/data-factory-scheduling-and-execution/scheduler-example.png)

Conforme mostrado no diagrama, especificar uma agenda para a atividade cria uma série de janelas em cascata. Janelas em cascata são uma série de intervalos de tempo de tamanho fixo, não se sobrepostos e contínuos. Essas janelas lógicas em cascata para a atividade são chamadas de **janelas de atividade**.
 
Para a janela de atividade atualmente em execução, o intervalo de tempo associado à janela de atividade pode ser acessado com as variáveis de sistema [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) e [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) na atividade JSON. Você pode usar essas variáveis para diferentes finalidades em sua atividade JSON, por exemplo, para selecionar dados de entrada e conjuntos de dados de saída que representam dados de uma série temporal.

A propriedade **scheduler** dá suporte às mesmas subpropriedades como a propriedade **availability** em um conjunto de dados. Confira o artigo [Disponibilidade do conjunto de dados](data-factory-create-datasets.md#Availability) para obter detalhes. Exemplos: agendamento em um deslocamento de tempo específico, definição do modo para alinhar o processamento no início do intervalo à janela de atividade ou no fim.

A especificação de propriedades de agendador para uma atividade é opcional. Se você especificá-las, elas devem corresponder à cadência que você especificar na definição do conjunto de dados de saída. Atualmente, o conjunto de dados de saída é o que aciona a agenda, então você deve criar um conjunto de dados de saída, mesmo que a atividade não produza qualquer saída. Se a atividade não receber entradas, ignore a criação de conjunto de dados de entrada.

## Conjuntos de dados de série temporal e fatias de dados

Dados de série temporal são uma sequência contínua de pontos de dados normalmente consistindo em sucessivas medidas feitas durante um intervalo de tempo. Exemplos comuns de dados de série temporal incluem dados de sensor, dados de telemetria de aplicativo, etc.

Com o Azure Data Factory, é possível processar dados de série temporal em lote com execuções da atividade. Normalmente, há cadências recorrentes nas quais os dados de entrada chegam e os dados de saída devem ser produzidos. Essa cadência é modelada especificando a seção **disponibilidade** no conjunto de dados da seguinte maneira:

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

Cada unidade de dados consumidos e produzidos por uma execução de atividade é chamada de uma **fatia** de dados. O diagrama a seguir mostra um exemplo de uma atividade com um conjunto de dados de entrada e um conjunto de dados de saída, cada um deles com a disponibilidade definida como frequência por hora.

![Agendador de disponibilidade](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

As fatias de dados por hora para o conjunto de dados de entrada e saída são mostradas no diagrama. O diagrama mostra três fatias de entrada que estão prontas para o processamento e a execução de atividade de 10h-11h em andamento, produzindo a fatia de saída de 10h-11h.

O intervalo de tempo associado à fatia atual que está sendo gerada pode ser acessado no conjunto de dados JSON com as variáveis [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) e [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables).

Atualmente, o data factory exige que a agenda especificada na atividade corresponda exatamente à agenda especificada na disponibilidade do conjunto de dados de saída. Portanto, WindowStart, WindowEnd e SliceStart e SliceEnd sempre são mapeados para o mesmo período de tempo e uma única fatia de saída.

Para obter mais informações sobre propriedades diferentes disponíveis para a seção disponibilidade, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md).

## Exemplo: Atividade de cópia movendo dados do SQL do Azure para o Blob do Azure

Vamos colocar tudo em ação criando um pipeline que copia dados de uma tabela do SQL Azure para Blobs do Azure a cada hora.

**Entrada: conjunto de dados do SQL do Azure**

	{
	    "name": "AzureSqlInput",
	    "properties": {
	        "published": false,
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}


A **frequência** é definida como **Hora** e o **intervalo** é definido como **1** na seção **disponibilidade**.

**Saída: conjunto de dados de Blob do Azure**
	
	{
	    "name": "AzureBlobOutput",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
	            "format": {
	                "type": "TextFormat"
	            },
	            "partitionedBy": [
	                {
	                    "name": "Year",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "yyyy"
	                    }
	                },
	                {
	                    "name": "Month",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
	                    }
	                }
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


A **frequência** é definida como **Hora** e o **intervalo** é definido como **1** na seção **disponibilidade**.



**Atividade: Atividade de cópia**

	{
	    "name": "SamplePipeline",
	    "properties": {
	        "description": "copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "name": "AzureSQLtoBlob",
	                "description": "copy activity",	
	                "typeProperties": {
	                    "source": {
	                        "type": "SqlSource",
	                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 100000,
	                        "writeBatchTimeout": "00:05:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "AzureSQLInput"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOutput"
	                    }
	                ],
	       			"scheduler": {
	          			"frequency": "Hour",
	          			"interval": 1
	        		}
	            }
	        ],
	        "start": "2015-01-01T08:00:00Z",
	        "end": "2015-01-01T11:00:00Z"
	    }
	}


O exemplo mostra a agenda de atividades e seções de disponibilidade do conjunto de dados definidas com frequência de hora em hora. O exemplo mostra como você pode usar **WindowStart** e **WindowEnd** para selecionar dados relevantes para uma atividade de execução e copiá-los para um blob com **folderPath** apropriado. folderPath é parametrizado para ter uma pasta separada para cada hora.

Quando três das fatias entre 8 – 11: AM forem executadas e os dados no SQL Azure forem:

![Entrada de exemplo](./media/data-factory-scheduling-and-execution/sample-input-data.png)

Ao ser implantado o pipeline, o blob do Azure é populado da seguinte maneira:

1.	Arquivo mypath/2015/1/1/8/Data.&lt;Guid&gt;.txt com dados

			10002345,334,2,2015-01-01 08:24:00.3130000
			10002345,347,15,2015-01-01 08:24:00.6570000
			10991568,2,7,2015-01-01 08:56:34.5300000

	> [AZURE.NOTE] &lt;Guid&gt; é substituído por um guid real. Exemplo de nome de arquivo: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
2.	Arquivo mypath/2015/1/1/9/Data.&lt;Guid&gt;.txt com dados:

			10002345,334,1,2015-01-01 09:13:00.3900000
			24379245,569,23,2015-01-01 09:25:00.3130000
			16777799,21,115,2015-01-01 09:47:34.3130000
3.	Arquivo mypath/2015/1/1/10/Data.&lt;Guid&gt;.txt. txt sem dados.


## Fatias de dados, período ativo do Pipeline e a execução simultânea de fatia

O artigo [Criando pipelines](data-factory-create-pipelines.md) introduziu o conceito de período ativo para um pipeline especificado definindo as propriedades **start** e **end**.
 
Você pode definir a data de início para o período ativo do pipeline no passado. Em seguida, o Data Factory calcula automaticamente (faz preenchimento de fundo) todas as fatias de dados no passado e começa a processá-las.

Com fatias de dados com fundo preenchido, é possível configurá-las para que sejam executadas em paralelo. Você pode fazer isso definindo a propriedade **concurrency** na seção **política** da atividade JSON, conforme mostrado no artigo [Criando pipelines](data-factory-create-pipelines.md).

## Executar novamente fatias de dados com falha e rastreamento automático de dependências de dados

Você pode monitorar a execução de fatias em um formato visualmente sofisticado. Confira **Monitorando e gerenciando pipelines usando** [folhas do Portal do Azure](data-factory-monitor-manage-pipelines.md) (ou) [Monitorar e gerenciar aplicativos](data-factory-monitor-manage-app.md) para obter detalhes.

Considere o exemplo a seguir, que mostra duas atividades. A Atividade1 produz um conjunto de dados de série temporal com fatias como saída, que é consumida como entrada pela Atividade2 para produzir o conjunto de dados de série temporal final de saída.

![Fatia com falha](./media/data-factory-scheduling-and-execution/failed-slice.png)

<br/>

O diagrama mostra que, de três fatias recentes, houve uma falha ao produzir a fatia de 9-10h para **Dataset2**. O data factory controla automaticamente a dependência para conjunto de dados de série temporal e, como resultado, atrasa o início da execução da atividade para a fatia downstream de 9-10h.


Ferramentas de gerenciamento e monitoramento do data factory permitem analisar os logs de diagnóstico para a fatia com falha localizar facilmente a causa raiz do problema e corrigi-lo. Depois de corrigir o problema, você pode iniciar facilmente a execução da atividade para produzir a fatia com falha. Para obter mais detalhes sobre como executar novas execuções e compreender as transições de estado para fatias de dados, confira **Monitorando e gerenciando pipelines usando** [folhas do Portal do Azure](data-factory-monitor-manage-pipelines.md) (ou) [Monitorar e gerenciar aplicativos](data-factory-monitor-manage-app.md) para obter detalhes.

Quando você executa novamente a fatia de 9-10h para dataset2 e ela está pronta, o Data Factory inicia a execução da fatia dependente de 9-10h no conjunto de dados final, conforme mostrado no diagrama a seguir.

![Executar novamente uma fatia com falha](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

Para se aprofundar na especificação e nas dependências de rastreamento para um grupo de atividades, confira a seções a seguir.

## Encadeando atividades
É possível encadear duas atividades fazendo com que o conjunto de dados de saída de uma atividade seja o conjunto de dados de entrada da outra atividade. As atividades podem estar no mesmo pipeline ou em pipelines diferentes. A segunda atividade é executada apenas quando a primeira é concluída com êxito.

Por exemplo, considere o seguinte caso:
 
1.	O pipeline P1 contém a Atividade A1, que exige o conjunto de dados de entrada externa D1, e produz o conjunto de dados de **saída** **D2**.
2.	O pipeline P2 contém a Atividade A2, que exige a **entrada** do conjunto de dados **D2**, e produz o conjunto de dados de saída D3.
 
Nesse cenário, a atividade A1 é executada quando os dados externos estão disponíveis e a frequência de disponibilidade agendada é alcançada. A atividade A2 é executada quando as fatias agendadas de D2 ficam disponíveis e a frequência de disponibilidade agendada é alcançada. Se houver um erro em uma das fatias no conjunto de dados D2, A2 não será executada para essa fatia até que fique disponível.

O Modo de Exibição de Diagrama seria semelhante ao seguinte diagrama:

![Encadeando atividades em dois pipelines](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

O Modo de Exibição de Diagrama com ambas as atividades no mesmo pipeline seria semelhante ao seguinte diagrama:

![Encadeando atividades no mesmo pipeline](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### Cópia solicitada
É possível executar várias operações de cópia, uma após a outra de maneira sequencial/ordenada. Digamos que você tenha duas atividades de cópia em um pipeline: CopyActivity1 e CopyActivity com os conjuntos de dados de saída dos dados de entrada a seguir.

CopyActivity1: Entrada: Dataset1 Saída Dataset2

CopyActivity2: Entradas: Dataset2 Saída Dataset4

CopyActivity2 seria executado somente se CopyActivity1 tivesse sido executado com êxito e Dataset2 estivesse disponível.

No exemplo, CopyActivity2 pode ter uma entrada diferente, digamos Dataset3, mas você especifica Dataset2 também como uma entrada para CopyActivity2 para que a atividade não seja executada até que CopyActivity1 seja concluído. Por exemplo:

CopyActivity1: Entrada: Dataset1 Saída Dataset2

CopyActivity2: Entradas: Dataset3, Dataset2 Saída: Dataset4

Quando várias entradas forem especificadas, somente o primeiro conjunto de dados de entrada será usado para copiar dados, mas outros conjuntos de dados serão usados como dependências. CopyActivity2 começaria executando apenas quando as seguintes condições fossem atendidas:

- CopyActivity1 foi concluído com êxito e Dataset2 está disponível. Esse conjunto de dados não é usado ao copiar dados para Dataset4. Ele atua apenas como uma dependência de agendamento de CopyActivity2.
- Dataset3 está disponível. Esse conjunto de dados representa os dados que são copiados para o destino.



## Conjuntos de dados de modelagem com frequências diferentes

Nos exemplos, as frequências de conjuntos de dados de entrada e saída e a janela de agendamento de atividade foram iguais. Alguns cenários exigem a capacidade de produzir saída em uma frequência diferente de frequências de uma ou mais entradas. O data factory dá suporte à modelagem desses cenários.

### Exemplo 1: gerar relatório diário de saída para dados de entrada que estão disponíveis de hora em hora

Considere um cenário em que temos dados de medição de entrada de sensores disponíveis a cada hora no Blob do Azure. Você deseja produzir um relatório diário de agregação com estatísticas, como média, máx., mín. etc. para o dia com a [Atividade de Hive](data-factory-hive-activity.md) do Data Factory.

Veja como você pode modelar esse cenário com o Data Factory:

**Conjunto de dados de entrada do Blob do Azure:**

Os arquivos de entrada por hora são instalados na pasta para o dia determinado. A disponibilidade de entrada é definida por hora (frequência: Hora, intervalo: 1).

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Conjunto de dados de saída do Blob do Azure**

Um arquivo de saída é criado diariamente na pasta para o dia. A disponibilidade de saída é definida diariamente (frequência: Dia e intervalo: 1).


	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Atividade: atividade de Hive em um pipeline**

O script do hive recebe as informações de data e hora apropriadas como parâmetros que usam a variável **WindowStart** conforme mostrado no trecho a seguir. O script do hive usa essa variável para carregar os dados da pasta correta para o dia e executar a agregação para gerar a saída.

		{  
		    "name":"SamplePipeline",
		    "properties":{  
		    "start":"2015-01-01T08:00:00",
		    "end":"2015-01-01T11:00:00",
		    "description":"hive activity",
		    "activities": [
		        {
		            "name": "SampleHiveActivity",
		            "inputs": [
		                {
		                    "name": "AzureBlobInput"
		                }
		            ],
		            "outputs": [
		                {
		                    "name": "AzureBlobOutput"
		                }
		            ],
		            "linkedServiceName": "HDInsightLinkedService",
		            "type": "HDInsightHive",
		            "typeProperties": {
		                "scriptPath": "adftutorial\\hivequery.hql",
		                "scriptLinkedService": "StorageLinkedService",
		                "defines": {
		                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
		                    "Month": "$$Text.Format('{0:%M}',WindowStart)",
		                    "Day": "$$Text.Format('{0:%d}',WindowStart)"
		                }
		            },
		            "scheduler": {
		                "frequency": "Day",
		                "interval": 1
		            },			
		            "policy": {
		                "concurrency": 1,
		                "executionPriorityOrder": "OldestFirst",
		                "retry": 2,
		                "timeout": "01:00:00"
		            }
	             }
		     ]
		   }
		}

O diagrama a seguir mostra o cenário do ponto de vista de dependência de dados.

![Dependência de dados](./media/data-factory-scheduling-and-execution/data-dependency.png)

A fatia de saída para cada dia depende 24 fatias horárias do conjunto de dados de entrada. O data factory calcula essas dependências automaticamente descobrindo as fatias de dados de entrada que equivalem ao mesmo período de tempo utilizado para a produção da fatia de saída. Se qualquer uma das 24 fatias de entrada não estiver disponível, o Data Factory aguardará até que a fatia de entrada esteja pronta antes de iniciar a execução da atividade diária.


### Exemplo 2: especificar dependência com expressões e funções de data factory

Vamos considerar outro cenário. Suponha que você tem uma atividade de Hive que processa dois conjuntos de dados de entrada: um deles recebe novos dados diariamente, mas o outro recebe novos dados a cada semana. Suponha que você queira fazer uma associação entre duas entradas e produzir uma saída diariamente.
 
A abordagem simples é quando o Data Factory detecta automaticamente as fatias de entrada certas a serem processadas alinhando-se ao período de tempo da fatia de dados de saída que não funciona.

Você precisa de um modo para especificar, para cada execução de atividade, que o data factory deve usar a fatia de dados da semana passada para o conjunto de dados de entrada semanal. Você pode fazer isso com a ajuda de funções do Azure Data Factory, conforme mostrado no trecho a seguir.

**Entrada1: Blob do Azure**

A primeira entrada é um blob do Azure atualizado **diariamente**.
	
	{
	  "name": "AzureBlobInputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Entrada2: Blob do Azure**

A Entrada2 é um blob do Azure atualizado **semanamente**.

	{
	  "name": "AzureBlobInputWeekly",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 7
	    }
	  }
	}

**Saída: Blob do Azure**

Um arquivo de saída é criado diariamente na pasta para o dia. A disponibilidade de saída é definida como Diariamente (frequência: Dia, intervalo: 1).
	
	{
	  "name": "AzureBlobOutputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Atividade: atividade de Hive em um pipeline**

A atividade de hive usa as duas entradas e produz uma fatia de saída todos os dias. É possível especificar que a fatia de saída de cada dia dependa da fatia de entrada da semana passada para a entrada semanal, como demonstrado a seguir.
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"hive activity",
	    "activities": [
	      {
	        "name": "SampleHiveActivity",
	        "inputs": [
	          {
	            "name": "AzureBlobInputDaily"
	          },
	          {
	            "name": "AzureBlobInputWeekly",
	            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
	            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutputDaily"
	          }
	        ],
	        "linkedServiceName": "HDInsightLinkedService",
	        "type": "HDInsightHive",
	        "typeProperties": {
	          "scriptPath": "adftutorial\\hivequery.hql",
	          "scriptLinkedService": "StorageLinkedService",
	          "defines": {
	            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
	            "Month": "$$Text.Format('{0:%M}',WindowStart)",
	            "Day": "$$Text.Format('{0:%d}',WindowStart)"
	          }
	        },
	        "scheduler": {
	          "frequency": "Day",
	          "interval": 1
	        },			
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 2,  
	          "timeout": "01:00:00"
	        }
		   } 
	     ]
	   }
	}


## Funções do Data Factory e variáveis do sistema   

Veja o artigo [Funções e variáveis do sistema do Data Factory](data-factory-functions-variables.md) para obter uma lista das funções e variáveis do sistema compatíveis com o Azure Data Factory.

## Grande aprofundamento em dependência de dados

Para gerar uma fatia do conjunto de dados por uma execução de atividade, o Data Factory usa o **modelo de dependência** a seguir para determinar as relações entre conjuntos de dados consumidos e produzidos por uma atividade.

O intervalo de tempo dos conjuntos de dados de entrada necessários para gerar a fatia do conjunto de dados de saída é chamado de **período de dependência**.

Executar uma atividade gera uma fatia do conjunto de dados somente depois que as fatias de dados nos conjuntos de dados de entrada dentro do período de dependência estão disponíveis. Isso significa que todas as fatias de entrada que compõem o período de dependência devem estar com status **Pronto** para que a fatia do conjunto de dados de saída seja produzida por uma execução de atividade.

Para gerar a fatia do conjunto de dados [início, fim], uma função é necessária para mapear a fatia do conjunto de dados para seu período de dependência. Essa função é essencialmente uma fórmula que converte o início e fim da fatia do conjunto de dados até o início e fim do período de dependência. Mais formalmente,
	
	DatasetSlice = [start, end]
	DependecyPeriod = [f(start, end), g(start, end)]

em que f e g estão mapeando as funções que calculam o início e término do período de dependência para cada entrada de atividade.

Como visto nos exemplos, o período de dependência é igual ao período da fatia de dados a ser produzida. Nesses casos, o data factory calcula automaticamente as fatias de entrada que se enquadram no período de dependência.

Por exemplo: no exemplo de agregação, em que a saída é produzida diariamente e dados de entrada estão disponíveis a cada hora, o período da fatia de dados é de 24 horas. O data factory localiza as fatias de entrada de hora em hora relevantes para esse período de tempo e torna a fatia de saída dependente da fatia de entrada.

Você também pode fornecer seu próprio mapeamento para o período de dependência, como mostrado no exemplo em que uma das entradas é semanal e o intervalo de saída é gerado diariamente.
   
## Validação e dependência de dados

Um conjunto de dados pode ter, opcionalmente, uma política de validação definida que especifica como os dados gerados pela execução de uma fatia podem ser validados antes de estarem prontos para consumo. Veja o artigo [Criando conjuntos de dados](data-factory-create-datasets.md) para obter detalhes.

Nesses casos, quando a execução da fatia tiver terminado, o status da fatia de saída será alterado para **Aguardando** com um substatus de **Validação**. Depois que as fatias são validadas, o status de fatia é alterado para **Pronto**.
   
Se uma fatia de dados foi produzida mas não passou na validação, as execuções de atividade para fatias downstream que dependem da fatia na qual houve falha na validação não serão processadas.

Os diversos estados de fatias de dados no Data Factory são abordados no artigo [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md).

## Dados externos

Um conjunto de dados pode ser marcado como externo (como mostrado no trecho de JSON a seguir), indicando que não foi gerado com o Azure Data Factory. Nesse caso, a política de conjunto de dados pode ter um conjunto de parâmetros que descrevem uma política de validação e repetição de tentativas para o conjunto de dados. Veja [Criando pipelines](data-factory-create-pipelines.md) para obter uma descrição de todas as propriedades.

Semelhante a conjuntos de dados que são produzidos pelo Data Factory, as fatias de dados para dados externos precisam estar prontas para que fatias dependentes possam ser processadas.

	{
		"name": "AzureSqlInput",
		"properties": 
		{
			"type": "AzureSqlTable",
			"linkedServiceName": "AzureSqlLinkedService",
			"typeProperties": 
			{
				"tableName": "MyTable"	
			},
			"availability": 
			{
				"frequency": "Hour",
				"interval": 1     
			},
			"external": true,
			"policy": 
			{
				"externalData": 
				{
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}  
		} 
	} 


## Pipeline Onetime
Você pode criar e agendar um pipeline para ser executado periodicamente (por exemplo: horário e diário etc.) dentro dos horários inicial e final que você especificar na definição do pipeline. Veja [Agendando atividades](#scheduling-and-execution) para obter detalhes. Você também pode criar um pipeline que executa apenas uma vez. Para fazer isso, defina a propriedade **pipelineMode** na definição do pipeline para **onetime** conforme mostrado no exemplo de JSON a seguir. O valor padrão dessa propriedade é **scheduled**.

	{
	    "name": "CopyPipeline",
	    "properties": {
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource",
	                        "recursive": false
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "InputDataset"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "OutputDataset"
	                    }
	                ]
	                "name": "CopyActivity-0"
	            }
	        ]
	        "pipelineMode": "OneTime"
	    }
	}

Observe o seguinte:
 
- As horas de **início** e **término** para o pipeline não são especificadas.
- A **disponibilidade** de conjuntos de dados de entrada e saída é especificada (frequência e intervalo), mesmo que os valores não sejam usados pelo Data Factory.
- A exibição de diagrama não mostra pipelines únicos. Este comportamento ocorre por design.
- Pipelines únicos não podem ser atualizados. É possível clonar um pipeline único, renomeá-lo, atualizar suas propriedades e implantá-lo para criar outro.

  




  









 
 












      

  

<!---HONumber=AcomDC_0824_2016-->