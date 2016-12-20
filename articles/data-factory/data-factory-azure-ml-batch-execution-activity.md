---
title: Usar atividades de Machine Learning | Microsoft Docs
description: "Descreve como criar pipelines de previsão usando o Azure Data Factory e o Aprendizado de Máquina do Azure"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 5b8144b0a65c18fb98cb410cfd83ad9812c6ed2a
ms.openlocfilehash: 44d1ddbc761851e9bb118444e85e95ae8e9be9fa


---
# <a name="create-predictive-pipelines-using-azure-machine-learning-activities"></a>Criar pipelines preditivos usando as atividades do Aprendizado de Máquina do Azure
> [!div class="op_single_selector"]
> [Hive](data-factory-hive-activity.md)  
> [Pig](data-factory-pig-activity.md)  
> [MapReduce](data-factory-map-reduce.md)  
> [Streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
> [Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> [Procedimento Armazenado](data-factory-stored-proc-activity.md)
> [U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> [.NET personalizado](data-factory-use-custom-activities.md)
>
>

## <a name="introduction"></a>Introdução
[Aprendizado de Máquina do Azure](https://azure.microsoft.com/documentation/services/machine-learning/) permite compilar, testar e implantar soluções de análise preditiva. De um ponto de vista de alto nível, isso é feito em três etapas:

1. **Crie um teste de treinamento**. Conclua esta etapa usando o Estúdio AM do Azure. O Estúdio AM do Azure é um ambiente de desenvolvimento visual colaborativo usado para treinar e testar um modelo de análise preditiva usando dados de treinamento.
2. **Convertê-lo em um teste preditivo**. Quando o modelo foi treinado com dados existentes e você estiver pronto para usá-lo para pontuar novos dados, você preparará e simplificará seu teste para a pontuação.
3. **Implantá-lo como um serviço da Web**. Você pode publicar seu experimento de pontuação como um serviço Web do Azure. Você pode enviar dados ao seu modelo por desse ponto de extremidade de serviço Web e receber previsões de resultado do modelo.  

O Azure Data Factory permite que você crie facilmente pipelines que usam o serviço Web do [Azure Machine Learning][azure-machine-learning] publicado para a análise preditiva. Consulte os artigos [Introdução ao Azure Data Factory](data-factory-introduction.md) e [Criar seu primeiro pipeline](data-factory-build-your-first-pipeline.md) para começar a introdução ao serviço do Azure Data Factory.

Usando a **Atividade de Execução em Lote** em um pipeline do Azure Data Factory, você pode invocar um serviço Web de AM do Azure para fazer previsões sobre dados em lote. Consulte a seção [Invocando um serviço web de AM do Azure usando a Atividade de Execução em Lote](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) para obter detalhes.

Ao longo do tempo, os modelos de previsão nos experimentos de pontuação do AM do Azure precisam ser treinados novamente usando novos conjuntos de dados de entrada. Você pode treinar novamente um modelo do AM do Azure de um pipeline do Data Factory executando as seguintes etapas:

1. Publique o experimento de treinamento (e não um experimento preditivo) como um serviço Web. Essa etapa é feita no Estúdio AM do Azure como você fez para expor o experimento preditivo como um serviço Web no cenário anterior.
2. Use a Atividade de Execução de Lote do AM do Azure para chamar o serviço Web para o experimento de treinamento. Basicamente, você pode usar a atividade de Execução de Lote do AM do Azure para invocar o serviço Web de treinamento e o serviço Web de pontuação.

Depois de concluir o treinamento, você deseja atualizar o serviço Web de pontuação (experimento preditivo exposto como um serviço Web) com o modelo recém-treinado. Siga estas etapas:

1. Adicione um ponto de extremidade não padrão ao serviço Web de pontuação. O ponto de extremidade padrão do serviço Web não pode ser atualizado, portanto você precisa criar um ponto de extremidade não padrão usando o Portal do Azure. Consulte o artigo [Criar pontos de extremidade](../machine-learning/machine-learning-create-endpoint.md) para obter informações conceituais e de procedimentos.
2. Atualize os serviços vinculados do AM do Azure existentes para pontuação para usar o ponto de extremidade não padrão. Comece a usar o novo ponto de extremidade para usar o serviço Web atualizado.
3. Use a **Atividade de Recurso de Atualização de AM do Azure** para atualizar o serviço Web com o modelo recém-treinado.  

Veja a seção [Atualizando modelos do AM do Azure usando a Atividade do Recurso de Atualização](#updating-azure-ml-models-using-the-update-resource-activity) para obter detalhes.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Invocar um serviço Web usando a Atividade de Execução em Lote
Você usa o Azure Data Factory para orquestrar o processamento e movimentação de dados e realizar a execução de lote usando o Aprendizado de Máquina do Azure. Estas são as etapas de nível superior:

1. Criar um serviço vinculado de Aprendizado de Máquina do Azure. Você precisará do seguinte:

   1. **URI de Solicitação** para a API de Execução do Lote. Encontre o URI da Solicitação clicando no link **EXECUÇÃO EM LOTE** na página de serviços Web.
   2. **Chave de API** para o serviço Web publicado do Aprendizado de Máquina do Azure. Encontre a chave de API clicando no serviço Web que você publicou.

      1. Use a atividade **AzureMLBatchExecution** .

      ![Painel de Aprendizado de Máquina](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![URI do lote](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário: Experimentos usando entradas/saídas de serviço Web que se referem aos dados no armazenamento de Blob do Azure
Nesse cenário, o serviço Web de aprendizado de máquina do Azure faz previsões usando dados de um arquivo em um armazenamento de blob do Azure e armazena os resultados de previsão no armazenamento de blob. O JSON a seguir define um pipeline do Data Factory com uma atividade AzureMLBatchExecution. A atividade contém o conjunto de dados **DecisionTreeInputBlob** como entrada e **DecisionTreeResultBlob** como a saída. O **DecisionTreeInputBlob** é passado como uma entrada para o serviço Web usando a propriedade JSON **webServiceInput**. O **DecisionTreeResultBlob** é passado como uma saída para o serviço Web usando a propriedade JSON **webServiceOutputs**.  

> [!IMPORTANT]
> Se o serviço Web receber várias entradas, use a propriedade **webServiceInputs** em vez de usar **webServiceInput**. Veja a seção [Serviço Web exige várias entradas](#web-service-requires-multiple-inputs) para obter um exemplo de como usar a propriedade webServiceInputs.
>
> Conjuntos de dados que são referenciados pelas propriedades **webServiceInput**/**webServiceInputs** e **webServiceOutputs** (em **typeProperties**) também devem ser incluídas nas **entradas** e **saídas** da atividade.
>
> Em seu experimento de Azure ML, as portas de entrada e saída do serviço Web e os parâmetros globais têm nomes padrão ("input1", "input2") os quais você pode personalizar. Os nomes que você usa para as configurações webServiceInputs, webserviceoutputs e globalParameters devem corresponder exatamente aos nomes nos testes. Você pode exibir a carga de solicitação de exemplo na página de Ajuda de Execução do Lote no ponto de extremidade de Azure ML para verificar o mapeamento esperado.
>
>

    {
      "name": "PredictivePipeline",
      "properties": {
        "description": "use AzureML model",
        "activities": [
          {
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [
              {
                "name": "DecisionTreeInputBlob"
              }
            ],
            "outputs": [
              {
                "name": "DecisionTreeResultBlob"
              }
            ],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob"
                }                
            },
            "policy": {
              "concurrency": 3,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            }
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }

> [!NOTE]
> Apenas as entradas e saídas da atividade AzureMLBatchExecution podem ser passadas como parâmetros para o serviço Web. Por exemplo, no trecho JSON acima, DecisionTreeInputBlob é uma entrada para a atividade de AzureMLBatchExecution, que é passada como entrada para o serviço Web a través do parâmetro webServiceInput.   
>
>

### <a name="example"></a>Exemplo
Este exemplo usa o armazenamento do Azure para conter tanto os dados de entrada quanto os de saída.

Recomendamos que você percorra o tutorial [Compilar seu primeiro pipeline com o Data Factory][adf-build-1st-pipeline] antes de usar este exemplo. Use o Editor do Data Factory para criar artefatos do Data Factory (serviços vinculados, conjuntos de dados, pipeline) neste exemplo.   

1. Criar um **serviço vinculado** para o **Armazenamento do Azure**. Se os arquivos de entrada e saída estiverem em contas de armazenamento diferentes, você precisará de dois serviços vinculados. Aqui está um exemplo JSON:

        {
          "name": "StorageLinkedService",
          "properties": {
            "type": "AzureStorage",
            "typeProperties": {
              "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
            }
          }
        }
2. Criar a **entrada** do **conjunto de dados** do Azure Data Factory. Ao contrário de alguns outros conjuntos de dados do Data Factory, esses conjuntos de dados devem conter os valores **folderPath** e **fileName**. Você pode usar o particionamento para fazer com que cada execução em lote (cada fatia de dados) processe ou produza arquivos de entrada e saída exclusiva. Você precisará incluir algumas atividades upstream para transformar a entrada no formato de arquivo CSV e colocá-lo na conta de armazenamento para cada fatia. Nesse caso, não inclua as configurações **external** e **externalData** mostradas no exemplo a seguir e seu DecisionTreeInputBlob seria o conjunto de dados de saída de uma Atividade diferente.

        {
          "name": "DecisionTreeInputBlob",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "azuremltesting/input",
              "fileName": "in.csv",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "external": true,
            "availability": {
              "frequency": "Day",
              "interval": 1
            },
            "policy": {
              "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
              }
            }
          }
        }

    O arquivo csv de entrada deve ter a linha de cabeçalho de coluna. Se você estiver usando a **Atividade de cópia** para criar/mover o csv para dentro do armazenamento de blobs, você deve definir a propriedade de coleta **blobWriterAddHeade**r como **true**. Por exemplo:

         sink:
         {
             "type": "BlobSink",     
             "blobWriterAddHeader": true
         }

    Se o arquivo csv não tem a linha de cabeçalho, você poderá ver o seguinte erro: **Erro na atividade: erro ao ler a cadeia de caracteres. Token inesperado: StartObject. Caminho '', linha 1, posição 1**.
3. Criar a **saída** do **conjunto de dados** da Azure Data Factory. Este exemplo usa o particionamento para criar um caminho de saída exclusivo para cada execução de divisão. Sem o particionamento, a atividade substituiria o arquivo.

        {
          "name": "DecisionTreeResultBlob",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "azuremltesting/scored/{folderpart}/",
              "fileName": "{filepart}result.csv",
              "partitionedBy": [
                {
                  "name": "folderpart",
                  "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "yyyyMMdd"
                  }
                },
                {
                  "name": "filepart",
                  "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HHmmss"
                  }
                }
              ],
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Day",
              "interval": 15
            }
          }
        }
4. Crie um **serviço vinculado** do tipo **AzureMLLinkedService**, fornecendo a chave de API e a URL de execução de lote do modelo.

        {
          "name": "MyAzureMLLinkedService",
          "properties": {
            "type": "AzureML",
            "typeProperties": {
              "mlEndpoint": "https://[batch execution endpoint]/jobs",
              "apiKey": "[apikey]"
            }
          }
        }
5. Por fim, crie um pipeline que contenha uma atividade **AzureMLBatchScoring** . Em tempo de execução, o pipeline executa as seguintes etapas:

   1. Obtém o local do arquivo de entrada de seus conjuntos de dados de entrada.
   2. Invocar a API de execução em lote do Azure Machine Learning
   3. Copia a saída da execução em lote no blob especificado no conjunto de dados de saída.

      > [!NOTE]
      > A atividade AzureMLBatchExecution pode ter zero ou mais entradas e uma ou mais saídas.
      >
      >

       {    "name": "PredictivePipeline",    "properties": {

           "description": "use AzureML model",
           "activities": [
             {
               "name": "MLActivity",
               "type": "AzureMLBatchExecution",
               "description": "prediction analysis on batch input",
               "inputs": [
                 {
                   "name": "DecisionTreeInputBlob"
                 }
               ],
               "outputs": [
                 {
                   "name": "DecisionTreeResultBlob"
                 }
               ],
               "linkedServiceName": "MyAzureMLLinkedService",
               "typeProperties":
               {
                   "webServiceInput": "DecisionTreeInputBlob",
                   "webServiceOutputs": {
                       "output1": "DecisionTreeResultBlob"
                   }                
               },
               "policy": {
                 "concurrency": 3,
                 "executionPriorityOrder": "NewestFirst",
                 "retry": 1,
                 "timeout": "02:00:00"
               }
             }
           ],
           "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
         }  }

      Ambos os valores de data/hora de **início** e de **término** devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O tempo **final** é opcional. Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**" Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**. Consulte a [Referência de script JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter detalhes sobre as propriedades JSON.

      > [!NOTE]
      > A especificação de entrada para a atividade AzureMLBatchExecution é opcional.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário: Experimentos usando módulos Leitor/Gravador para fazer referência a dados em vários armazenamentos
Outro cenário comum durante a criação de experimentos AM do Azure é usar módulos Leitor e Gravador. O módulo leitor é usado para carregar dados em um experimento e o módulo gravador é usado para salvar os dados dos experimentos. Para obter detalhes sobre os módulos de leitor e gravador, consulte os tópicos [Leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Gravador](https://msdn.microsoft.com/library/azure/dn905984.aspx) na biblioteca MSDN.     

Ao usar os módulos leitor e gravador, é recomendável usar um parâmetro de serviço Web para cada propriedade desses módulos leitor/gravador. Esses parâmetros da Web permitem que você configure os valores durante o tempo de execução. Por exemplo, você poderia criar um experimento com um módulo leitor que usa um banco de dados SQL do Azure: XXX.database.windows.net. Depois que o serviço web tiver sido implantado, você precisa habilitar os consumidores do serviço Web para especificar outro servidor SQL do Azure chamado YYY.database.windows.net. Você pode usar um parâmetro de serviço Web para permitir que esse valor seja configurado.

> [!NOTE]
> A saída e entrada de serviço Web são diferentes dos parâmetros de serviço Web. No primeiro cenário, você viu como uma entrada e saída podem ser especificados para um serviço Web do AM do Azure. Nesse cenário, você pode passar parâmetros para um serviço Web que correspondem às propriedades dos módulos de leitor/gravador.
>
>

Vejamos um cenário para o uso de parâmetros de serviço Web. Você tem um serviço Web implantado de Azure Machine Learning que usa um módulo de leitor para ler dados de uma das fontes de dados compatíveis com o Azure Machine Learning (por exemplo: Banco de Dados SQL do Azure). Após a execução do lote, os resultados são gravados usando um módulo Gravador (banco de dados SQL do Azure).  Não há entradas e saídas de serviço Web definidas nos experimentos. Nesse caso, recomendamos que você configure os parâmetros de serviço Web relevantes para os módulos de leitor e gravador. Essa configuração permite que os módulos de leitor/gravador sejam configurados ao usar a atividade AzureMLBatchExecution. Você especifica parâmetros de serviço Web na seção **globalParameters** no JSON da atividade da seguinte maneira:

    "typeProperties": {
        "globalParameters": {
            "Param 1": "Value 1",
            "Param 2": "Value 2"
        }
    }

Você também pode usar [Funções do Data Factory](data-factory-functions-variables.md) para passar valores para os parâmetros do serviço Web conforme mostrado no exemplo a seguir:

    "typeProperties": {
        "globalParameters": {
           "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
        }
      }

> [!NOTE]
> Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, portanto, garanta que os nomes que você especificar na atividade de JSON correspondam aos expostos pelo serviço Web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Uso de um módulo Leitor para ler dados de vários arquivos no Blob do Azure
Pipelines de Big Data com atividades como Pig e Hive podem produzir um ou mais arquivos de saída sem extensão. Por exemplo, quando você especifica uma tabela externa do Hive, os dados para a tabela externa do Hive podem ser armazenados no armazenamento de blob do Azure com o seguinte nome 000000_0. É possível usar o módulo leitor em um experimento para ler vários arquivos e usá-los para previsões.

Ao usar o módulo de leitor em uma experiência de Aprendizado de Máquina do Azure, é possível especificar o Blob do Azure como uma entrada. Os arquivos no Armazenamento de Blobs do Azure podem ser os arquivos de saída (Exemplo: 000000_0) que são produzidos por um script de Pig e Hive em execução no HDInsight. O módulo de leitor permite que você leia arquivos (sem extensões), configurando o **Caminho para o contêiner, blob/diretório**. O **Caminho para o contêiner** aponta para o contêiner e o **diretório/blob** aponta para a pasta que contém os arquivos, conforme mostra a imagem a seguir. O asterisco, \*), **especifica que todos os arquivos no contêiner/pasta (ou seja, data/aggregateddata/year=2014/month-6/\*)** são lidos como parte do teste.

![Propriedades de Blob do Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Exemplo
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline com a atividade AzureMLBatchExecution com parâmetros de serviço Web
    {
      "name": "MLWithSqlReaderSqlWriter",
      "properties": {
        "description": "Azure ML model with sql azure reader/writer",
        "activities": [
          {
            "name": "MLSqlReaderSqlWriterActivity",
            "type": "AzureMLBatchExecution",
            "description": "test",
            "inputs": [
              {
                "name": "MLSqlInput"
              }
            ],
            "outputs": [
              {
                "name": "MLSqlOutput"
              }
            ],
            "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
                  "globalParameters": {
                    "Database server name": "<myserver>.database.windows.net",
                    "Database name": "<database>",
                    "Server user account name": "<user name>",
                    "Server user account password": "<password>"
                  }              
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            },
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }

No exemplo JSON acima:

* O serviço Web implantado de Aprendizado de Máquina do Azure usa um modulo leitor e gravador para ler/gravar dados de/para um banco de dados SQL do Azure. Este serviço Web expõe os seguintes quatro parâmetros: Nome do servidor de banco de dados, Nome do banco de dados, Nome de conta de usuário do servidor e Senha de conta de usuário do servidor.  
* Ambos os valores de data/hora de **início** e de **término** devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O tempo **final** é opcional. Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**" Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**. Consulte a [Referência de script JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter detalhes sobre as propriedades JSON.

### <a name="other-scenarios"></a>Outros cenários
#### <a name="web-service-requires-multiple-inputs"></a>Serviço Web exige várias entradas
Se o serviço Web receber várias entradas, use a propriedade **webServiceInputs** em vez de usar **webServiceInput**. Os conjuntos de dados referenciados por **webServiceInputs** também devem ser incluídos nas **entradas** da Atividade.

Em seu experimento de Azure ML, as portas de entrada e saída do serviço Web e os parâmetros globais têm nomes padrão ("input1", "input2") os quais você pode personalizar. Os nomes que você usa para as configurações webServiceInputs, webserviceoutputs e globalParameters devem corresponder exatamente aos nomes nos testes. Você pode exibir a carga de solicitação de exemplo na página de Ajuda de Execução do Lote no ponto de extremidade de Azure ML para verificar o mapeamento esperado.

    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [{
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [{
                    "name": "inputDataset1"
                }, {
                    "name": "inputDataset2"
                }],
                "outputs": [{
                    "name": "outputDataset"
                }],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": "inputDataset1",
                        "input2": "inputDataset2"
                    },
                    "webServiceOutputs": {
                        "output1": "outputDataset"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }

#### <a name="web-service-does-not-require-an-input"></a>O serviço Web não requer uma entrada
Os serviços Web de execução em lote do ML do Azure podem ser usados para executar qualquer fluxo de trabalho, por exemplo, scripts R ou Python, e esses podem não exigir entradas. Ou o teste pode ser configurado com um módulo Leitor que não expõe GlobalParameters. Nesse caso, a atividade AzureMLBatchExecution seria configurada da seguinte maneira:

    {
        "name": "scoring service",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "myBlob"
            }
        ],
        "typeProperties": {
            "webServiceOutputs": {
                "output1": "myBlob"
            }              
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },


#### <a name="web-service-does-not-require-an-inputoutput"></a>O serviço Web não requer uma entrada/saída
O serviço Web de execução de lote do ML do Azure pode não ter nenhuma saída de serviço Web configurada. Neste exemplo, não há nenhum serviço Web de entrada ou saída, nem GlobalParameters configurados. Ainda há uma saída configurada na própria atividade, mas ela não é fornecida como um webServiceOutput.

    {
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "placeholderOutputDataset"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>O serviço Web usa leitores e gravadores, e a atividade será executada somente quando outras atividades tiverem êxito.
Os módulos leitor e gravador do serviço Web do ML do Azure podem ser configurados para executar com ou sem GlobalParameters. No entanto, convém inserir chamadas de serviço em um pipeline que usa as dependências do conjunto de dados para chamar o serviço apenas quando algum processamento upstream for concluído. Você também pode disparar alguma outra ação após a execução em lote usando esta abordagem. Nesse caso, você pode expressar as dependências usando as entradas e saídas de atividade sem nomeá-las como entradas ou saídas do serviço Web.

    {
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "inputs": [
            {
                "name": "upstreamData1"
            },
            {
                "name": "upstreamData2"
            }
        ],
        "outputs": [
            {
                "name": "downstreamData"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

Os **aprendizados** são:

* Se o ponto de extremidade do teste usar um webServiceInput:, ele será representado por um conjunto de dados de blob e será incluído nas entradas da atividade e na propriedade webServiceInput. Caso contrário, a propriedade webServiceInput é omitida.
* Se o ponto de extremidade do teste usar um webServiceOutput(s):, ele será representado por um conjunto de dados de blob e será incluído nas saídas da atividade e na propriedade webServiceOutputs. A saída da atividade e webServiceOutputs são mapeados de acordo com o nome de cada saída no teste. Caso contrário, a propriedade webServiceOutputs é omitida.
* Se o ponto de extremidade do teste expor globalParameter(s), eles serão fornecidos na propriedade globalParameters da atividade como pares de valor e chave. Caso contrário, a propriedade globalParameters é omitida. As chaves diferenciam maiúsculas de minúsculas. [funções do Azure Data Factory](data-factory-functions-variables.md) podem ser usadas nos valores.
* Podem ser incluídos nas propriedades de entrada e saída da atividade conjuntos de dados adicionais, sem serem referenciados no typeProperties da atividade. Esses conjuntos de dados determinam a execução com dependências de fatia, mas são ignorados pela atividade AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Atualização dos modelos usando a Atividade de Recurso de Atualização
Ao longo do tempo, os modelos de previsão nos experimentos de pontuação do AM do Azure precisam ser treinados novamente usando novos conjuntos de dados de entrada. Depois de concluir o novo treinamento, você deseja atualizar o serviço Web de pontuação com o modelo do AM treinado novamente. As etapas típicas para habilitar novos treinamentos e a atualização de modelos do AM do Azure por meio de serviços Web são:

1. Crie um teste no [Estúdio AM do Azure](https://studio.azureml.net).
2. Quando estiver satisfeito com o modelo, use o Estúdio AM do Azure para publicar serviços Web para o **teste de treinamento** e o teste de pontuação/**previsão**.

A tabela a seguir descreve os serviços Web usados neste exemplo.  Confira [Readaptar os modelos do Aprendizado de Máquina de forma programática](../machine-learning/machine-learning-retrain-models-programmatically.md) para obter detalhes.

| Tipo de serviço Web | description |
|:--- |:--- |
| **Serviço Web de treinamento** |Recebe dados de treinamento e produz modelos treinados. A saída do novo treinamento é um arquivo .ilearner em um Armazenamento de Blobs do Azure.  O **ponto de extremidade padrão** é criado automaticamente para você quando o experimento de treinamento é publicado como um serviço Web. Você pode criar mais pontos de extremidade, mas o exemplo usa apenas o ponto de extremidade padrão |
| **Serviço Web de pontuação** |Recebe exemplos de dados sem rótulo de e faz previsões. A saída de previsão pode ter diversas formas, como um arquivo .csv ou linhas em um banco de dados SQL do Azure, dependendo da configuração do experimento. O ponto de extremidade padrão é criado automaticamente para você quando o teste preditivo é publicado como um serviço Web. Crie o segundo **ponto de extremidade não padrão e atualizável** usando o [Portal do Azure](https://manage.windowsazure.com). Você pode criar mais pontos de extremidade, mas este exemplo usa apenas o ponto de extremidade atualizável não padrão. Confira o artigo [Criar pontos de extremidade](../machine-learning/machine-learning-create-endpoint.md) para conhecer as etapas. |

A figura a seguir descreve o relacionamento entre os pontos de extremidade de treinamento e de pontuação no AM do Azure.

![SERVIÇOS WEB](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Você pode invocar o **training web service** usando o **Atividade de Execução de Lote do AM do Azure**. A invocação de um serviço Web de treinamento é igual à invocação de um serviço Web do AM do Azure ML (serviço Web de pontuação) para pontuação de dados. As seções anteriores abordam em detalhes como invocar um serviço Web de AM do Azure de um pipeline do Azure Data Factory.

Você pode invocar o **scoring web service** usando o **Atividade de Recurso de Atualização de AM do Azure** para atualizar o serviço Web com o modelo recém-treinado. Como mencionado na tabela acima, você deve criar e usar o ponto de extremidade não padrão atualizável. Além disso, atualize quaisquer serviços vinculados existentes em seu data factory a fim de usar o ponto de extremidade não padrão para que eles sempre usem o modelo mais recente recuperado.

O cenário a seguir fornece mais detalhes. Ele tem um exemplo de readaptação e atualização de modelos de AM do Azure de um pipeline do Azure Data Factory.

### <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Cenário: novo treinamento e atualização de um modelo do AM do Azure
Esta seção fornece um pipeline de exemplo que usa a **atividade de Execução em lote do AM do Azure** para treinar novamente um modelo. O pipeline também usa a **atividade do Recurso de atualização do AM do Azure** para atualizar o modelo no serviço Web de pontuação. A seção também fornece trechos de JSON para todos os serviços vinculados, conjuntos de dados e pipeline no exemplo.

Este é o modo de exibição de diagrama do pipeline de exemplo. Como você pode ver, a atividade de Execução em lote do AM do Azure assume a entrada de treinamento e produz uma saída de treinamento (arquivo iLearner). A Atividade de Recurso de Atualização do AM do Azure obtém a saída deste treinamento e atualiza o modelo no ponto de extremidade de serviço Web de pontuação. A Atividade do Recurso de Atualização não produz nenhuma saída. O placeholderBlob é apenas um conjunto de dados de saída fictício necessário ao serviço Azure Data Factory para executar o pipeline.

![diagrama de pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

#### <a name="azure-blob-storage-linked-service"></a>Serviço vinculado do armazenamento de Blob do Azure:
O Armazenamento do Azure contém os seguintes dados:

* dados de treinamento. Os dados de entrada para o serviço Web de treinamento de AM do Azure.  
* Arquivo iLearner. Os dados de saída do serviço Web de treinamento de AM do Azure. Este arquivo também é a entrada para a atividade do Recurso de atualização.  

Veja a definição JSON de exemplo do serviço vinculado:

    {
        "name": "StorageLinkedService",
          "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
            }
        }
    }


#### <a name="training-input-dataset"></a>Conjunto de dados de entrada do treinamento:
O conjunto de dados a seguir representa os dados de treinamento de entrada do serviço Web de treinamento do AM do Azure. A atividade de Execução de Lote do AM do Azure obtém esse conjunto de dados como uma entrada.

    {
        "name": "trainingData",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "labeledexamples",
                "fileName": "labeledexamples.arff",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Week",
                "interval": 1
            },
            "policy": {          
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

#### <a name="training-output-dataset"></a>Conjunto de dados de saída de treinamento:
O conjunto de dados a seguir representa o arquivo do iLearner de saída do serviço Web de treinamento do AM do Azure. A Atividade de Execução de Lote do AM do Azure produz este conjunto de dados. O conjunto de dados também é a entrada para a atividade do Recurso de Atualização do AM do Azure.

    {
        "name": "trainedModelBlob",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "trainingoutput",
                "fileName": "model.ilearner",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Week",
                "interval": 1
            }
        }
    }

#### <a name="linked-service-for-azure-ml-training-endpoint"></a>Serviço vinculado para o ponto de extremidade de treinamento do AM do Azure ML
O trecho JSON a seguir define um serviço vinculado de Aprendizado de Máquina do Azure que aponta para o ponto de extremidade padrão do serviço Web de treinamento.

    {    
        "name": "trainingEndpoint",
          "properties": {
            "type": "AzureML",
            "typeProperties": {
                "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
                  "apiKey": "myKey"
            }
          }
    }

No **Azure ML Studio**, faça o seguinte para obter os valores de **mlEndpoint** e **apiKey**:

1. Clique em **SERVIÇOS WEB** no menu à esquerda.
2. Clique no **serviço Web de treinamento** na lista de serviços Web.
3. Clique em Copiar ao lado da caixa de texto **Chave de API** . Cole a chave na área de transferência e no editor JSON do Data Factory.
4. No **Azure ML Studio**, clique no link **EXECUÇÃO EM LOTE**.
5. Copie o **URI da Solicitação** da seção **Solicitação** e cole-o no editor de JSON do Data Factory.   

#### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Serviço Vinculado para o ponto de extremidade de pontuação atualizável do AM do Azure:
O trecho de código do JSON a seguir define um serviço vinculado do Aprendizado de Máquina do Azure que aponta para o ponto de extremidade não padrão atualizável do serviço Web de pontuação.  

    {
        "name": "updatableScoringEndpoint2",
        "properties": {
            "type": "AzureML",
            "typeProperties": {
                "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
                "apiKey": "endpoint2Key",
                "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
            }
        }
    }


Antes de criar e implantar um serviço vinculado do AM do Azure, siga as etapas do artigo [Criar pontos de extremidade](../machine-learning/machine-learning-create-endpoint.md) para criar um segundo ponto de extremidade (não padrão e atualizável) para o serviço Web pontuação.

Depois de criar o ponto de extremidade atualizável não padrão, faça o seguinte:

* Clique em **EXECUÇÃO EM LOTE** para obter o valor do URI para a propriedade JSON **mlEndpoint**.
* Clique no link **ATUALIZAR RECURSO** para obter o valor do URI para a propriedade JSON **updateResourceEndpoint**. A chave de API está na própria página do ponto de extremidade (no canto inferior direito).

![ponto de extremidade atualizável](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

#### <a name="placeholder-output-dataset"></a>Conjunto de dados de saída de espaço reservado:
A atividade de Atualização do recurso do AM do Azure não gera qualquer saída. No entanto, o Azure Data Factory exige um conjunto de dados de saída para gerar a agenda de um pipeline. Portanto, usamos um conjunto de dados fictício/espaço reservado neste exemplo.  

    {
        "name": "placeholderBlob",
        "properties": {
            "availability": {
                "frequency": "Week",
                "interval": 1
            },
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "any",
                "format": {
                    "type": "TextFormat"
                }
            }
        }
    }


#### <a name="pipeline"></a>Pipeline
O pipeline tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de Execução em lote do AM do Azure usa os dados de treinamento como entrada e produz um arquivo iLearner como saída. A atividade invoca o serviço Web de treinamento (experimento de treinamento exposto como um serviço Web) com os dados de treinamento de entrada e recebe o arquivo ilearner do serviço Web. O placeholderBlob é apenas um conjunto de dados de saída fictício necessário ao serviço Azure Data Factory para executar o pipeline.

![diagrama de pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

    {
        "name": "pipeline",
        "properties": {
            "activities": [
                {
                    "name": "retraining",
                    "type": "AzureMLBatchExecution",
                    "inputs": [
                        {
                            "name": "trainingData"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "trainedModelBlob"
                        }
                    ],
                    "typeProperties": {
                        "webServiceInput": "trainingData",
                        "webServiceOutputs": {
                            "output1": "trainedModelBlob"
                        }              
                     },
                    "linkedServiceName": "trainingEndpoint",
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "02:00:00"
                    }
                },
                {
                    "type": "AzureMLUpdateResource",
                    "typeProperties": {
                        "trainedModelName": "Training Exp for ADF ML [trained model]",
                        "trainedModelDatasetName" :  "trainedModelBlob"
                    },
                    "inputs": [
                        {
                            "name": "trainedModelBlob"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "placeholderBlob"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "name": "AzureML Update Resource",
                    "linkedServiceName": "updatableScoringEndpoint2"
                }
            ],
            "start": "2016-02-13T00:00:00Z",
               "end": "2016-02-14T00:00:00Z"
        }
    }


### <a name="reader-and-writer-modules"></a>Módulos de leitor e gravador
Um cenário comum de uso de parâmetros de serviço Web é o uso de leitores e gravadores do SQL do Azure. O módulo de leitor é usado para carregar dados em um teste a partir de serviços de gerenciamento de dados fora do Estúdio de Azure Machine Learning. O módulo de gravador é usado para salvar dados de seu teste nos serviços de gerenciamento de dados fora do Estúdio de Azure Machine Learning.  

Para obter detalhes sobre leitor/gravador do SQL do Azure/Blob do Azure, consulte os tópicos [Leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Gravador](https://msdn.microsoft.com/library/azure/dn905984.aspx) na biblioteca MSDN. O exemplo na seção anterior usou o leitor de blobs do Azure e o gravador de blobs do Azure. Esta seção discute usando o leitor do SQL do Azure e o gravador do SQL do Azure.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
**P:** Tenho vários arquivos que são gerados pelos meus pipelines de Big Data. Posso usar a atividade AzureMLBatchExecution para trabalhar em todos os arquivos?

**R:** Sim. Confira a seção **Usando um módulo Leitor para ler dados de vários arquivos no Blob do Azure** para obter detalhes.

## <a name="azure-ml-batch-scoring-activity"></a>Atividade de pontuação do lote do ML do Azure
Se você estiver usando a atividade **AzureMLBatchScoring** para integrar-se ao Azure Machine Learning, recomendamos que você use a atividade **AzureMLBatchExecution** mais recente.

A atividade AzureMLBatchExecution foi introduzida na versão de agosto de 2015 do Azure SDK e Azure PowerShell.

Se você quiser continuar usando a atividade AzureMLBatchScoring, continue lendo esta seção.  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Atividade de pontuação em lote do ML do Azure usando o armazenamento do Azure para entrada/saída
    {
      "name": "PredictivePipeline",
      "properties": {
        "description": "use AzureML model",
        "activities": [
          {
            "name": "MLActivity",
            "type": "AzureMLBatchScoring",
            "description": "prediction analysis on batch input",
            "inputs": [
              {
                "name": "ScoringInputBlob"
              }
            ],
            "outputs": [
              {
                "name": "ScoringResultBlob"
              }
            ],
            "linkedServiceName": "MyAzureMLLinkedService",
            "policy": {
              "concurrency": 3,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            }
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }

### <a name="web-service-parameters"></a>Parâmetros de serviço Web
Para especificar valores para parâmetros de serviço Web, adicione uma seção **typeProperties** à seção **AzureMLBatchScoringActivty** no JSON do pipeline, conforme mostra o exemplo a seguir:

    "typeProperties": {
        "webServiceParameters": {
            "Param 1": "Value 1",
            "Param 2": "Value 2"
        }
    }


Você também pode usar [Funções do Data Factory](data-factory-functions-variables.md) para passar valores para os parâmetros do serviço Web conforme mostrado no exemplo a seguir:

    "typeProperties": {
        "webServiceParameters": {
           "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
        }
      }

> [!NOTE]
> Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, portanto, garanta que os nomes que você especificar na atividade de JSON correspondam aos expostos pelo serviço Web.
>
>

## <a name="see-also"></a>Consulte também
* [Postagem do blog do Azure: Introdução ao Azure Data Factory e Aprendizado de Máquina do Azure](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/



<!--HONumber=Nov16_HO3-->


