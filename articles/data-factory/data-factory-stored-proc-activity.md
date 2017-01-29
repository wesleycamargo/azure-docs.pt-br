---
title: Atividade de procedimento armazenado do SQL Server
description: "Saiba como é possível usar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado em um banco de dados SQL do Azure ou SQL Data Warehouse do Azure de um pipeline de Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: ec522d843b2827c12ff04afac15d89d525d88676
ms.openlocfilehash: 90bd5b4b6fb58c044b5edaba2c5f3a4bf7346e7d


---
# <a name="sql-server-stored-procedure-activity"></a>Atividade de procedimento armazenado do SQL Server

> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md) 
> * [Pig](data-factory-pig-activity.md)
> * [MapReduce](data-factory-map-reduce.md)
> * [Streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aprendizado de máquina](data-factory-azure-ml-batch-execution-activity.md)
> * [Procedimento armazenado](data-factory-stored-proc-activity.md)
> * [U-SQL da Análise Data Lake](data-factory-usql-activity.md)
> * [.NET personalizado](data-factory-use-custom-activities.md)
>

Use atividades de transformação de dados em um [pipeline](data-factory-create-pipelines.md) do Data Factory para transformar e processar dados brutos em previsões e informações. A Atividade de Procedimento Armazenado é uma das atividades de transformação que o Data Factory dá suporte. Este artigo se baseia no artigo sobre [atividades de transformação de dados](data-factory-data-transformation-activities.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas.

Você pode usar a Atividade de Procedimento Armazenado para invocar um procedimento armazenado em um dos seguintes repositórios de dados: Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, Banco de Dados SQL Server na sua empresa ou em uma VM (máquina virtual) do Azure.  Se estiver usando o SQL Server, será necessário instalar o Gateway de Gerenciamento de Dados no mesmo computador que hospeda o banco de dados ou em um computador separado para evitar a concorrência por recursos com o banco de dados. O Gateway de Gerenciamento de Dados é um software que conecta fontes de dados locais ou em uma VM do Azure a serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para obter detalhes.

A seguir há instruções passo a passo de como chamar um procedimento armazenado em um Banco de Dados SQL do Azure de um pipeline do Data Factory usando a atividade de procedimento armazenado. 

## <a name="walkthrough"></a>Passo a passo
### <a name="sample-table-and-stored-procedure"></a>Tabela de exemplo e procedimento armazenado
1. Crie a seguinte **tabela** em seu Banco de Dados SQL do Azure usando o Estúdio de Gerenciamento do SQL Server ou qualquer outra ferramenta com a qual você estiver confortável. A coluna datetimestamp é a data e a hora em que a ID correspondente é gerada.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    ID é a única identificada e a coluna datetimestamp é a data e a hora em que a ID correspondente é gerada.
    
    ![Dados de amostra](./media/data-factory-stored-proc-activity/sample-data.png)

    Este exemplo usa o Banco de Dados SQL do Azure, mas funciona da mesma maneira para o SQL Data Warehouse do Azure e para o Banco de Dados SQL Server. Para o Banco de Dados SQL Server, é necessário instalar um [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md).
2. Crie o **procedimento armazenado** a seguir que insere dados na **sampletable**.

    ```SQL
    CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > O **nome** e o **uso de maiúsculas** do parâmetro (DateTime, neste exemplo) devem corresponder àqueles do parâmetro especificado no JSON do pipeline ou da atividade. Na definição do procedimento armazenado, certifique-se de que **@** seja usado como um prefixo para o parâmetro.

### <a name="create-a-data-factory"></a>Criar uma data factory
1. Faça logon no [portal do Azure](https://portal.azure.com/).
2. Clique em **NOVO** no menu à esquerda, clique em **Inteligência + Análise** e em **Data Factory**.

    ![Novo data factory](media/data-factory-stored-proc-activity/new-data-factory.png)    
3. Na folha **Novo data factory**, insira **SProcDF** como o Nome. Os nomes do Azure Data Factory são **globalmente exclusivos**. É preciso prefixar o nome do data factory com seu nome, para habilitar a criação bem-sucedida de fábrica.

   ![Novo data factory](media/data-factory-stored-proc-activity/new-data-factory-blade.png)         
4. Selecione sua **assinatura do Azure**.
5. Para **Grupo de Recursos**, realize uma das seguintes etapas:
   1. Clique em **Criar novo** e insira um nome para o grupo de recursos.
   2. Clique em **Usar existente** e selecione um grupo de recursos existente.  
6. Selecione o **local** do data factory.
7. Selecione **Fixar no painel** para ver o data factory no painel no próximo logon.
8. Clique em **Criar** na folha **Novo data factory**.
9. Você vê o data factory sendo criado no **painel** do portal do Azure. Após o data factory ter sido criado com êxito, você verá a página do data factory, que exibe seu conteúdo.

   ![Home page do Data Factory](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Criar um serviço vinculado do SQL do Azure
Depois de criar a data factory, você deve criar um serviço do SQL Azure vinculado que vincula seu banco de dados SQL na data factory. Esse banco de dados contém a tabela tabeladeexemplo e o procedimento armazenado sp_sample.

1. Clique em **Criar e implantar** na folha **Data Factory** de **SProcDF** para iniciar o Editor do Data Factory.
2. Clique em **Novo armazenamento de dados** na barra de comandos e escolha **Banco de Dados SQL do Azure**. Você deve ver o script JSON para criar um serviço vinculado do SQL Azure no editor.

   ![Novo armazenamento de dados](media/data-factory-stored-proc-activity/new-data-store.png)
3. No script JSON, faça as seguintes alterações:

   1. Substitua **&lt;servername&gt;** pelo nome de seu servidor do Banco de Dados SQL do Azure.
   2. Substitua **&lt;databasename&gt;** pelo banco de dados no qual você criou a tabela e o procedimento armazenado.
   3. Substitua **&lt;username@servername&gt;** pela conta de usuário que tem acesso ao banco de dados.
   4. Substitua **&lt;password&gt;** pela senha da conta de usuário.

      ![Novo armazenamento de dados](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado. Confirme se você vê AzureSqlLinkedService no modo de exibição de árvore à esquerda.

    ![modo de exibição de árvore com serviço vinculado](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
1. Clique em **... Mais** na barra de ferramentas, clique em **Novo conjunto de dados** e em **SQL Azure**. **Novo conjunto de dados** na barra de comandos e selecione **SQL Azure**.

    ![modo de exibição de árvore com serviço vinculado](media/data-factory-stored-proc-activity/new-dataset.png)
2. Copie/cole o script JSON a seguir no editor de JSON.

    ```JSON
    {                
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Clique em **Implantar** na barra de comando para implantar o conjunto de dados. Confirme se você vê o conjunto de dados no modo de exibição de árvore.

    ![modo de exibição de árvore com serviços vinculados](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Criar um pipeline com atividade de SqlServerStoredProcedure
AGora, crie um pipeline com atividade de SqlServerStoredProcedure.

1. Clique em **... Mais ** na barra de comandos e clique em **Novo pipeline**.
2. Copiar/colar o seguinte trecho JSON. O **storedProcedureName** definido como **sp_sample**. O nome e o uso de maiúsculas e minúsculas do parâmetro **DateTime** deve corresponder ao nome e o uso de maiúsculas e minúsculas do parâmetro na definição do procedimento armazenado.  

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "sp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
             "start": "2016-08-02T00:00:00Z",
             "end": "2016-08-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```

    Se você precisar passar null para um parâmetro, use a sintaxe: "param1": null (todas as letras minúsculas).
3. Clique em **Implantar** na barra de ferramentas para implantar o pipeline.  

### <a name="monitor-the-pipeline"></a>Monitorar o Pipeline
1. Clique em **X** para fechar as folhas do Editor da Fábrica de Dados e para navegar de volta à folha Fábrica de Dados e clique em **Diagrama**.

    ![bloco do diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. Na **Exibição de Diagrama**, você tem uma visão geral dos pipelines e dos conjuntos de dados usados neste tutorial.

    ![bloco do diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Na Exibição de Diagrama, clique duas vezes no conjunto de dados **sprocsampleout**. Você vê as partes no estado Pronto. Deve haver cinco partes porque uma fatia é produzida para cada hora entre a hora de início e de término no JSON.

    ![bloco do diagrama](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Quando uma fatia estiver no estado **Pronto**, execute uma consulta **select * de sampletable** no banco de dados SQL do Azure para verificar se os dados foram inseridos na tabela pelo procedimento armazenado.

   ![Dados de saída](./media/data-factory-stored-proc-activity/output.png)

   Consulte [Monitorar o pipeline](data-factory-monitor-manage-pipelines.md) para obter informações detalhadas sobre o monitoramento de pipelines da Azure Data Factory.  

> [!NOTE]
> Neste exemplo, SprocActivitySample não tem entradas. Se você desejar encadear essa atividade com uma atividade upstream (ou seja, antes do processamento), as saídas da atividade upstream poderão ser usadas como entradas nessa atividade. Nesse caso, essa atividade não será executada até que a atividade upstream seja concluída e as saídas das atividades upstream estejam disponíveis (com status Pronto). As entradas não podem ser usadas diretamente como parâmetro para a atividade de procedimento armazenado
>
>

## <a name="json-format"></a>Formato JSON
Aqui está o formato JSON para definir uma Atividade de Procedimento Armazenado:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs":  [ { "name": "inputtable"  } ],
    "outputs":  [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":  
        {
            "param1": "param1Value"
            …
        }
    }
}
```

## <a name="json-properties"></a>Propriedades do JSON
| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| name | Nome da atividade |Sim |
| Descrição |Texto que descreve qual a utilidade da atividade |Não |
| type | Deve ser definido como: **SqlServerStoredProcedure** | Sim |
| inputs | Opcional. Se você especificar um conjunto de dados de entrada, ele deverá estar disponível (no status 'Pronto') para a atividade de procedimento armazenado a ser executada. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Ele só é usado para verificar a dependência antes de iniciar a atividade de procedimento armazenado. |Não |
| outputs |Você deve especificar um conjunto de dados de saída para uma atividade de procedimento armazenado. O conjunto de dados de saída especifica a **agenda** da atividade de procedimento armazenado (por hora, semana, mês, etc.). <br/><br/>O conjunto de dados de saída deve usar um **serviço vinculado** que se refere a um Banco de Dados SQL do Azure, ou SQL Data Warehouse do Azure, ou um Banco de Dados SQL Server no qual você quer que o procedimento armazenado seja executado. <br/><br/>O conjunto de dados de saída pode servir como uma maneira de passar o resultado do procedimento armazenado para processamento posterior de outra atividade ([atividades de encadeamento](data-factory-scheduling-and-execution.md#run-activities-in-a-sequence)) no pipeline. No entanto, o Data Factory não trava automaticamente a saída de um procedimento armazenado para esse conjunto de dados. É o procedimento armazenado que grava em uma tabela SQL para a qual o conjunto de dados de saída aponta. <br/><br/>Em alguns casos, o conjunto de dados de saída pode ser um **conjunto de dados fictício**, que é usado apenas para especificar o agendamento para execução da atividade de procedimento armazenado. |Sim |
| storedProcedureName |Especifique o nome do procedimento armazenado no banco de dados SQL do Azure ou SQL Data Warehouse do Azure que é representado pelo serviço vinculado utilizado pela tabela de saída. |Sim |
| storedProcedureParameters |Especifique valores para parâmetros de procedimento armazenado. Se você precisar passar null para um parâmetro, use a sintaxe: "param1": null (todas as letras minúsculas). Veja o exemplo a seguir para saber mais sobre como usar essa propriedade. |Não |

## <a name="passing-a-static-value"></a>Passando um valor estático
Agora, vamos considerar adicionar outra coluna denominada 'Cenário' na tabela que contém um valor estático chamado 'Exemplo de documento'.

![Dados de exemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabela:**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Procedimento armazenado:**

```SQL
CREATE PROCEDURE sp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Agora, passe o parâmetro **Cenário** e o valor da atividade de procedimento armazenado. A seção **typeProperties** no exemplo acima se parece com o seguinte trecho de código:

```JSON
"typeProperties":
{
    "storedProcedureName": "sp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Conjunto de dados do Data Factory:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline do Data Factory**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```


<!--HONumber=Dec16_HO3-->


