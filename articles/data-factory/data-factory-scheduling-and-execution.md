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
	ms.date="10/20/2015" 
	ms.author="spelluru"/>

# Agendamento e execução com o Data Factory
  
Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. Este artigo se baseia nos artigos [Criando pipelines](data-factory-create-pipelines.md) e [Criando conjuntos de dados](data-factory-create-datasets.md) e presume que você compreenda as noções básicas sobre conceitos de modelo de aplicativo do data factory: atividade, pipelines, serviços vinculados e conjuntos de dados.

## Agendando atividades

Na seção **Agendador** na atividade JSON, você pode especificar um agendamento recorrente para a atividade. Por exemplo, você pode agendar execuções de atividade a cada hora, da seguinte maneira:

	"scheduler": {
		"frequency": "Hour",
	    "interval": 1
	},  
    
![Exemplo do Agendador](./media/data-factory-scheduling-and-execution/scheduler-example.png)

Conforme mostrado acima, especificar um agendamento por hora cria execuções de atividade correspondendo a uma série de janelas em cascata. Janelas em cascata são uma série de intervalos de tempo de tamanho fixo, não se sobrepostos e contínuos.
 
Para que a atividade em execução atualmente seja executada, o intervalo de tempo da janela pode ser acessado com as variáveis de sistema **WindowStart** e **WindowEnd** na atividade JSON. Você pode usar essas variáveis para finalidades diferentes no JSON de sua atividade e scripts associados a essa atividade, inclusive selecionar dados conjuntos de dados de entrada e saída que representam dados de série temporal.

Para obter mais informações sobre propriedades diferentes disponíveis para o agendador, inclusive agendamento em uma diferença de tempo específica, definição do modo de alinhamento e processamento no início ou no final do intervalo para a janela, consulte o artigo [Criar pipelines](data-factory-create-pipelines.md).

## Conjuntos de dados de série temporal e fatias de dados

Dados de série temporal são uma sequência contínua de pontos de dados normalmente consistindo em sucessivas medidas feitas durante um intervalo de tempo. Exemplos comuns de dados de série temporal incluem dados de sensor, dados de telemetria de aplicativo, etc.

Com o Azure Data Factory, é possível processar dados de série temporal em lote com execuções da atividade. Normalmente, há cadências recorrentes nas quais os dados de entrada chegam e os dados de saída devem ser produzidos. Essa cadência é modelada especificando a seção **disponibilidade** no conjunto de dados da seguinte maneira:

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

Cada unidade de dados consumidos e produzidos por uma execução de atividade é chamada de uma **fatia** de dados. O diagrama a seguir mostra um exemplo de uma atividade com um conjunto de dados de série temporal de entrada e um conjunto de dados de série temporal de saída, cada um com conjunto de disponibilidade definido com frequência de hora em hora.

![Agendador de disponibilidade](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

As fatias de dados por hora para o conjunto de dados de entrada e saída são mostradas no diagrama acima. O diagrama mostra 3 fatias de entrada que estão prontas para o processamento e a execução de atividade de 10-11h em andamento, produzindo a fatia de saída de 10-11h.

O intervalo de tempo associado à fatia atual que está sendo gerada pode ser acessado no conjunto de dados JSON com as variáveis **SliceStart** e **SliceEnd**.

Para obter mais informações sobre propriedades diferentes disponíveis para a seção disponibilidade, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md).

## Exemplo: Atividade de cópia movendo dados do SQL do Azure para o Blob do Azure

Vamos colocar alguma coisa juntos e na ação ao visitar novamente o exemplo para a atividade de cópia mostrada no [criar Pipelines](data-factory-create-pipelines.md) artigo que copia dados de uma tabela do SQL do Azure para um blob do Azure a cada hora.

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


Observe que a **frequência** é definida como **Hora** e o **intervalo** é definido como **1** na seção **disponibilidade**.

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


Observe que a **frequência** é definida como **Hora** e o **intervalo** é definido como **1** na seção **disponibilidade**.



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


O exemplo acima mostra a agenda de atividades e seções de disponibilidade do conjunto de dados definidas com frequência de hora em hora. O exemplo mostra como você pode aproveitar as variáveis **WindowStart** e **WindowEnd** para selecionar os dados relevantes para a atividade dada e enviá-la para um blob com o **folderPath** dinâmico apropriado, com parâmetros definindo a frequência da pasta como de hora em hora.

Quando três das fatias entre 8-11h são executadas, a aparência para uma tabela e Blob de exemplo do Azure será como demonstrado a seguir.

Suponha que os dados no SQL do Azure são como demonstrado a seguir:

![Entrada de exemplo](./media/data-factory-scheduling-and-execution/sample-input-data.png)

Ao implantar o pipeline acima, o Blob do Azure será populado da seguinte maneira:

1.	Arquivo mypath/2015/1/1/8/Data.<Guid>.txt com dados 

		10002345,334,2,2015-01-01 08:24:00.3130000
		10002345,347,15,2015-01-01 08:24:00.6570000
		10991568,2,7,2015-01-01 08:56:34.5300000

	**Observação:** <Guid> será substituído por um GUID real. Exemplo de nome de arquivo: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
2.	Arquivo mypath/2015/1/1/9/Data.<Guid>.txt com dados:

		10002345,334,1,2015-01-01 09:13:00.3900000
		24379245,569,23,2015-01-01 09:25:00.3130000
		16777799,21,115,2015-01-01 09:47:34.3130000
3.	Arquivo mypath/2015/1/1/10/Data.<Guid>.txt sem dados.


## Fatias de dados, período ativo do Pipeline e a execução simultânea de fatia

O artigo [Criando pipelines](data-factory-create-pipelines.md) introduziu o conceito de período ativo para um pipeline especificado definindo as propriedades **start** e **end** do pipeline.
 
Você pode definir a data de início para o período ativo do pipeline no passado e o data factory calculará automaticamente (preenchimento de fundo) todas as fatias de dados no passado e começará a processá-las.

Com fatias de dados com fundo preenchido, é possível configurá-las para que sejam executadas em paralelo. Você pode fazer isso definindo a propriedade de simultaneidade na seção **política** da atividade JSON, conforme mostrado no artigo [Criando pipelines](data-factory-create-pipelines.md).

## Executar novamente fatias de dados com falha e rastreamento automático de dependências de dados

Você pode monitorar a execução de fatias em um formato visualmente sofisticado. Consulte o artigo [Monitorando e gerenciando pipelines](data-factory-monitor-manage-pipelines.md) para obter detalhes.

Considere o exemplo a seguir, que mostra duas atividades. A Atividade1 produz um conjunto de dados de série temporal com fatias como saída, que é consumida como entrada pela Atividade2 para produzir o conjunto de dados de série temporal final de saída.

![Fatia com falha](./media/data-factory-scheduling-and-execution/failed-slice.png)

<br/>

O diagrama acima mostra que, de 3 fatias recentes, houve uma falha ao produzir a fatia de 9-10h para **Dataset2**. O data factory controla automaticamente a dependência para conjunto de dados de série temporal e, como resultado, atrasa o início da execução da atividade para a fatia downstream de 9-10h.


Ferramentas de gerenciamento e monitoramento do data factory permitem analisar os logs de diagnóstico para a fatia com falha localizar facilmente a causa raiz do problema e corrigi-lo. Depois de corrigir o problema, você pode iniciar facilmente a execução da atividade para produzir a fatia com falha. Para obter mais detalhes sobre como iniciar reexecuções e para compreender as transições de estado para fatias de dados, consulte o artigo [monitoramento e gerenciamento](data-factory-monitor-manage-pipelines.md).

Depois que você iniciar a reexecução e a fatia de 9-10h para o conjuntodedados2 estiver pronta, o data factory inicia a execução da fatia dependente de 9-10h no conjunto de dados final, conforme mostrado no diagrama abaixo.

![Executar novamente uma fatia com falha](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

Para se aprofundar na especificação de dependência e acompanhar as dependências para uma cadeia complexa de atividades e conjuntos de dados, consulte a seções abaixo.

## Conjuntos de dados de modelagem com frequências diferentes

Nos exemplos acima, as frequências de conjuntos de dados de entrada e saída e a janela de agendamento de atividade foram iguais. Alguns cenários exigem a capacidade de produzir saída em uma frequência diferente de frequências de uma ou mais entradas. O data factory dá suporte à modelagem desses cenários.

### Exemplo 1: gerar relatório diário de saída para dados de entrada que estão disponíveis de hora em hora

Considere um cenário onde podemos ter dados de medição de entrada provenientes de sensores disponíveis de hora em hora no Blob do Azure e queremos produzir um relatório diário de agregação com estatísticas, como média, máx, mín, etc. para os dados do dia com a [atividade de Hive](data-factory-hive-activity.md) do data factory.

Eis como você pode modelar isso com o data factory:

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

Um arquivo de saída será ignorado todos os dias na pasta para o dia. A disponibilidade de saída é definida diariamente (frequência: Dia e intervalo: 1).


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

O script do hive recebe as informações de data e hora apropriadas como parâmetros aproveitando a variável **WindowStart**, conforme mostrado abaixo. O script do hive usa essa variável para carregar os dados da pasta correta para o dia e executar a agregação para gerar a saída.

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
		                    "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
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

Eis aqui a aparência disso do ponto de vista da dependência de dados.

![Dependência de dados](./media/data-factory-scheduling-and-execution/data-dependency.png)

A fatia de saída para cada dia depende 24 fatias horárias do conjunto de dados de entrada. O data factory calcula essas dependências automaticamente descobrindo as fatias de dados de entrada que equivalem ao mesmo período de tempo utilizado para a produção da fatia de saída. Se qualquer uma das 24 fatias de entrada não está disponível (devido ao processamento ocorrendo em uma atividade upstream que produz essa fatia, por exemplo), o data factory aguardará que a fatia de entrada esteja pronta antes de iniciar a execução da atividade diária.


### Exemplo 2: especificar dependência com expressões e funções de data factory

Vamos considerar outro cenário. Suponha que você tem uma atividade de Hive que processa dois conjuntos de dados de entrada: um deles recebe novos dados diariamente, mas o outro recebe novos dados a cada semana. Suponha que você queira fazer uma associação entre duas entradas e produzir uma saída diariamente.
 
A abordagem simples até o momento em que o data factory detecta automaticamente as fatias de entrada corretas a processar, incluindo fatias de dados de entrada alinhadas ao período de tempo da fatia de dados de saída, não funciona mais.

Você precisa de um modo para especificar, para cada execução de atividade, que o data factory deve usar a fatia de dados da semana passada para o conjunto de dados de entrada semanal. Você pode fazer isso com a ajuda das funções do Azure Data Factory, conforme mostrado abaixo.

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

Um arquivo de saída será ignorado todos os dias na pasta para o dia. A disponibilidade de saída é definida como Diariamente (frequência: Dia, intervalo: 1).
	
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

A atividade de hive usa as 2 entradas e produz uma fatia de saída todos os dias. É possível especificar que a fatia de saída de cada dia dependa da fatia de entrada da semana passada para a entrada semanal, como demonstrado a seguir.
	
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
	            "startTime": "Date.AddDays(SliceStart,  -7 - Date.DayOfWeek(SliceStart))",
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
	            "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
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



## Variáveis de sistema do Data Factory

Nome de variável | Descrição | Escopo do objeto | Escopo JSON e casos de uso
------------- | ----------- | ------------ | ------------------------
WindowStart | Início do intervalo de tempo para a janela da execução de atividade atual | atividade | <ol><li>Especifica consultas de seleção de dados. Consulte os artigos de conector referenciados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md).</li><li>Passe parâmetros para o script do Hive (exemplo mostrado acima).</li>
WindowEnd | Fim do intervalo de tempo para a janela da execução de atividade atual | atividade | o mesmo que o descrito acima
SliceStart | Início do intervalo de tempo para a fatia de dados sendo gerada | atividade<br/>conjunto de dados | <ol><li>Especifique caminhos de pasta dinâmicos e nomes de arquivos trabalhando simultaneamente com o [Blob do Azure](data-factory-azure-blob-connector.md) e [Conjuntos de dados do sistema de arquivos](data-factory-onprem-file-system-connector.md).</li><li>Especifique dependências de entrada com funções do data factory na coleção de entradas de atividade.</li></ol>
SliceEnd | Fim do intervalo de tempo para a fatia de dados atual que está sendo gerada | atividade<br/>conjunto de dados | O mesmo que o descrito acima. 

> [AZURE.NOTE]Atualmente, o data factory exige que a agenda especificada na atividade corresponda exatamente à agenda especificada na disponibilidade do conjunto de dados de saída. Isso significa que WindowStart, WindowEnd e SliceStart e SliceEnd sempre são mapeados para o mesmo período de tempo e uma única fatia de saída.
 
## Referência de funções do Data Factory

Você pode usar funções no Data Factory junto com as variáveis do sistema mencionadas acima para as seguintes finalidades:

1.	Especificando consultas de seleção de dados (consulte os artigos de conector referenciados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md).

	A sintaxe para invocar uma função do Data Factory é: **$$<function>** para consultas de seleção de dados e outras propriedades na atividade e nos conjuntos de dados.  
2. Especificar dependências de entrada com funções de data factory na coleção de entradas da atividade (consulte o exemplo acima).

	$$ não é necessário para especificar expressões de dependência de entrada.

No exemplo a seguir, a propriedade **sqlReaderQuery** em um arquivo JSON é atribuída a um valor retornado pela função **Text.Format**. Esta amostra também usa uma variável de sistema chamada **WindowStart**, que representa a hora de início da janela de execução de atividade.
	
	{
	    "Type": "SqlSource",
	    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
	}

### Funções

As tabelas a seguir listam todas as funções no Azure Data Factory:

Categoria | Função | Parâmetros | Descrição
-------- | -------- | ---------- | ----------- 
Hora | AddHours(X,Y) | X: DateTime <p>Y: int</p> | Adiciona Y horas até o momento X determinado. <p>Exemplo: 5/9/2013 12h + 2 horas = 5/9/2013 14h</p>
Hora | AddMinutes(X,Y) | X: DateTime <p>Y: int</p> | Adiciona Y minutos a X.<p>Exemplo: 15/9/2013 12h + 15 minutos = 15/9/2013 12h15</p>
Hora | StartOfHour(X) | X: Datetime | Obtém a hora de início para a hora representada pelo componente de hora do X. <p>Exemplo: a StartOfHour de 15/9/2013 17:10:23 é 15/9/2013 17:00:00</p>
Data | AddDays(X,Y) | X: DateTime<p>Y: int</p> | Adiciona Y dias a X.<p>Exemplo: 15/9/2013 12h + 2 dias = 17/9/2013 12h</p>
Data | AddMonths(X,Y) | X: DateTime<p>Y: int</p> | Adiciona Y meses a X.<p>Exemplo: 15/9/2013 12h + 1 mês = 15/10/2013 12h</p> 
Data | AddQuarters(X,Y) | X: DateTime <p>Y: int</p> | Adiciona Y * 3 meses a X.<p>Exemplo: 15/9/2013 12h + 1 trimestre = 15/12/2013 12h</p>
Data | AddWeeks(X,Y) | X: DateTime<p>Y: int</p> | Adiciona Y * 7 dias a X<p>Exemplo: 15/9/2013 12h + 1 semana = 22/9/2013 12h</p>
Data | AddYears(X,Y) | X: DateTime<p>Y: int</p> | Adiciona Y anos a X<p>Exemplo: 15/9/2013 12h + 1 ano = 15/9/2014 12h</p>
Data | Day(X) | X: DateTime | Obtém o componente de dia de X.<p>Exemplo: O componente Day de 15/9/2013 12h é 15.</p>
Data | DayOfWeek(X) | X: DateTime | Obtém o componente de dia da semana de X.<p>Exemplo: o DayOfWeek de 15/9/2013 12h é Domingo.</p>
Data | DayOfYear(X) | X: DateTime | Obtém o dia do ano que representa o componente de ano de X.<p>Exemplos:<br/>1/12/2015: dia 335 de 2015<br/>31/12/2015: dia 365 de 2015<br/>31/12/2016: dia 366 de 2016 (ano bissexto)</p>
Data | DaysInMonth(X) | X: DateTime | Obtém os dias do mês representados pelo componente de mês do parâmetro X.<p>Exemplo: DaysInMonth de 15/9/2013 são 30, já que há 30 dias do mês de setembro.</p>
Data | EndOfDay(X) | X: DateTime | Obtém a data e hora que representam o fim do dia (componente do dia) do X.<p>Exemplo: EndOfDay de 15/9/2013 17:10:23 é 15/9/2013 23:59:59.</p>
Data | EndOfMonth(X) | X: DateTime | Obtém o final do mês representado pelo componente de mês de parâmetro X.<p>Exemplo: EndOfMonth de 15/9/2013 17:09:23 é 30/9/2013 23:59:59 (data e hora que representam o final do mês de setembro)</p>
Data | StartOfDay(X) | X: DateTime | Obtém o início do dia representado pelo componente dia do parâmetro X.<p>Exemplo: StartOfDay de 15/9/2013 17:10:23 é 15/9/2013 12h.</p>
DateTime | From(X) | X: Cadeia de caracteres | Analise a cadeia de caracteres X para um valor de data e hora.
DateTime | Ticks(X) | X: DateTime | Obtém os tiques de propriedade do parâmetro X. Um tique é igual a 100 nanossegundos. O valor dessa propriedade representa o número de tiques que se passaram desde 0h, meia-noite de 1º de janeiro de 0001. 
Texto | Format(X) | X: variável de cadeia de caracteres | Formata o texto.

#### Exemplo de Text.Format

	"defines": { 
	    "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
	    "Month" : "$$Text.Format('{0:MM}',WindowStart)",
	    "Day" : "$$Text.Format('{0:dd}',WindowStart)",
	    "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
	}

> [AZURE.NOTE]Ao usar uma função dentro de outra função, você não precisa usar o prefixo **$$** para a função interna. Por exemplo: $$Text.Format('PartitionKey eq \\'my\_pkey\_filter\_value\\' e RowKey ge \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). Neste exemplo, observe que o prefixo **$$** não é usado para a função **Time.AddHours**.
  

## Grande aprofundamento em dependência de dados

Para gerar uma fatia do conjunto de dados por uma execução de atividade, o data factory usa o seguinte **modelo dependência** para determinar as relações entre o conjunto(s) de dados consumido por uma atividade e o conjunto(s) de dados produzido por uma atividade.

O intervalo de tempo do conjunto(s) de dados de entrada necessário para gerar a fatia do conjunto de dados de saída é chamado de **período de dependência**.

Executar uma atividade gera uma fatia do conjunto de dados somente depois que as fatias de dados no conjunto(s) de dados de entrada dentro do período de dependência estão disponíveis. Isso significa que todas as fatias de entrada que compõem o período de dependência devem estar com status **Pronta** para que a fatia do conjunto de dados de saída seja produzida por uma execução de atividade.

Para gerar a fatia do conjunto de dados [início, fim], uma função é necessária para mapear a fatia do conjunto de dados para seu período de dependência. Essa função é essencialmente uma fórmula que converte o início e fim da fatia do conjunto de dados até o início e fim do período de dependência. Mais formalmente,
	
	DatasetSlice = [start, end]
	DependecyPeriod = [f(start, end), g(start, end)]

em que f e g estão mapeando as funções que calculam o início e término do período de dependência para cada entrada de atividade.

Como visto em exemplos mostrados acima, na maioria dos casos o período de dependência é igual ao período da fatia de dados a ser gerada. Nesses casos, o data factory calcula automaticamente as fatias de entrada que se enquadram no período de dependência.

Por exemplo: no exemplo de agregação acima, em que a saída é produzida diariamente e dados de entrada estão disponíveis a cada hora, o período da fatia de dados é de 24 horas. O data factory localiza as fatias de entrada de hora em hora relevantes para esse período de tempo e torna a fatia de saída dependente da fatia de entrada.

Você também pode fornecer seu próprio mapeamento para o período de dependência, como mostrado no exemplo acima em que uma das entradas é semanal e o intervalo de saída é gerado diariamente.
   
## Validação e dependência de dados

Um conjunto de dados pode ter, opcionalmente, uma política de validação definida que especifica como os dados gerados pela execução de uma fatia podem ser validados antes de estarem prontos para consumo. Consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md) para obter detalhes.

Nesses casos, quando execução da fatia tiver terminado, o status da fatia de saída é alterado para **Esperando** com um substatus de **Validação**. Depois que as fatias são validadas, o status de fatia é alterado para **Pronto**.
   
Se uma fatia de dados foi produzida mas não passou na validação, as execuções de atividade para fatias downstream que dependem da fatia na qual houve falha na validação não serão processadas.

Os diversos estados de fatias de dados no data factory são abordados no artigo [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md).

## Dados externos

Um conjunto de dados pode ser marcado como externo (como mostra o JSON abaixo), indicando que ele não foi gerado com o Azure Data Factory. Nesse caso, a política de conjunto de dados pode ter um conjunto de parâmetros que descrevem uma política de validação e repetição de tentativas para o conjunto de dados. Consulte [Criando pipelines](data-factory-create-pipelines.md) para obter uma descrição de todas as propriedades.

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






  









 
 












      

  

<!---HONumber=Oct15_HO4-->