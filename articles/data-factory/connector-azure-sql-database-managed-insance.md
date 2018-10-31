---
title: Copiar dados para/da Instância Gerenciada do Banco de Dados SQL do Azure usando o Azure Data Factory| Microsoft Docs
description: Saiba como mover dados para/da Instância Gerenciada do Banco de Dados SQL do Azure usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: jingwang
ms.openlocfilehash: 87ca7c10095f12c82137b6287fbb895c97676062
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458978"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-using-azure-data-factory"></a>Copiar dados para e da Instância Gerenciada do Banco de Dados SQL do Azure usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para uma Instância Gerenciada do Banco de Dados SQL do Azure. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados da Instância Gerenciado do Banco de Dados SQL do Azure para qualquer armazenamento de dados de coletor com suporte ou copiar dados de qualquer armazenamento de dados de origem com suporte para a Instância Gerenciada. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector da Instância Gerenciada do Banco de Dados SQL do Azure dá suporte a:

- Cópia de dados usando a autenticação do **SQL** ou do **Windows**.
- Como fonte, dá suporte à recuperação de dados, usando a consulta SQL ou o procedimento armazenado.
- Como o coletor, ao acréscimo de dados na tabela de destino ou à invocação de um procedimento armazenado com lógica personalizada durante a cópia.

## <a name="prerequisites"></a>Pré-requisitos

Para usar os dados copiados de uma Instância Gerenciada do Banco de Dados SQL do Azure que está localizado na rede virtual, você precisa configurar um Microsoft Integration Runtime auto-hospedado na mesma rede virtual que pode acessar o banco de dados. Consulte o artigo [Self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Integration Runtime auto-hospedado) para obter detalhes.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector da Instância Gerenciada do Banco de Dados SQL do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado da Instância Gerenciada do Banco de Dados SQL do Azure:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como **SqlServer** | SIM |
| connectionString |Especifique as informações de connectionString necessárias para conexão com a Instância Gerenciada usando a autenticação SQL ou a autenticação do Windows. Consulte o exemplo a seguir. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |SIM |
| userName |Especifique o nome de usuário se você estiver usando a Autenticação do Windows. Exemplo: **domainname\\username**. |Não  |
| Senha |Especifique a senha da conta de usuário que você especificou para userName. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Não  |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Provisione o Microsoft Integration Runtime auto-hospedado na mesma rede virtual que sua Instância Gerenciada. |SIM |

>[!TIP]
>Se ocorrer erro com código de erro como "UserErrorFailedToConnectToSqlServer" e mensagem como "O limite da sessão para o banco de dados é XXX e foi atingido.", adicione `Pooling=false` à cadeia de conexão e tente novamente.

**Exemplo 1: usando a autenticação SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: usando a autenticação do Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
             "userName": "<domain\\username>",
             "password": {
                "type": "SecureString",
                "value": "<password>"
             }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados da Instância Gerenciada do Banco de Dados SQL do Azure.

Para copiar dados de/para a Instância Gerenciada do Banco de Dados SQL do Azure, defina o tipo de propriedade do conjunto de dados como **SqlServerTable**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do conjunto de dados deve ser definida como: **SqlServerTable** | SIM |
| tableName |Nome da tabela ou exibição na instância do banco de dados ao qual o serviço vinculado se refere. | SIM |

**Exemplo**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte da fonte de dados e do coletor da Instância Gerenciada do Banco de Dados SQL do Azure.

### <a name="azure-sql-database-managed-instance-as-source"></a>Instância Gerenciada do Banco de Dados SQL do Azure como fonte de dados

Para copiar dados da Instância Gerenciada do Banco de Dados SQL do Azure, defina o tipo de fonte na atividade de cópia como **SqlSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como: **SqlSource** | SIM |
| SqlReaderQuery |Utiliza a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. |Não  |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. |Não  |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são: pares nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não  |

**Pontos a serem observados**

- Se **sqlReaderQuery** for especificado para SqlSource, a atividade de cópia executará essa consulta na fonte de dados da Instância Gerenciada para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).
- Se você não especificar a propriedade “sqlReaderQuery” nem a “sqlReaderStoredProcedureName”, as colunas definidas na seção “estrutura” do JSON do conjunto de dados serão usadas para criar uma consulta (`select column1, column2 from mytable`) para ser executada na Instância Gerenciada. Se a definição de conjunto de dados não tiver a seção "structure", todas as colunas serão selecionadas da tabela.
- Quando você usa **sqlReaderStoredProcedureName**, ainda é necessário especificar uma propriedade **tableName** fictícia no JSON do conjunto de dados.

**Exemplo: usando uma consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemplo: usando um procedimento armazenado**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**A definição do procedimento armazenado**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-managed-instance-as-sink"></a>Instância Gerenciada do Banco de Dados SQL do Azure como coletor

Para copiar dados para a Instância Gerenciada do Banco de Dados SQL do Azure, defina o tipo de coletor na atividade de cópia como **SqlSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | O tipo de propriedade do coletor de atividade de cópia deve ser definido como: **SqlSink** | SIM |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize.<br/>Os valores permitidos são: inteiro (número de linhas). |Não (padrão: 10000) |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são: período. Exemplo: "00:30:00" (30 minutos). |Não  |
| preCopyScript |Especifica uma consulta SQL para a atividade de cópia executar antes da gravação dos dados na Instância Gerenciada. Isso será invocado somente uma vez por execução de cópia. Você pode usar essa propriedade para limpar os dados previamente carregados. |Não  |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que define como aplicar os dados de origem à tabela de destino, por exemplo, para fazer upserts ou transformações usando sua própria lógica de negócios. <br/><br/>Observe que esse procedimento armazenado será **invocado por lote**. Se você deseja executar uma operação que é executada apenas uma vez e que não tem nenhuma relação com os dados de origem, por exemplo, excluir/truncar, use a propriedade `preCopyScript`. |Não  |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são: pares nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não  |
| sqlWriterTableType |Especifique um nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados sendo copiados com dados existentes. |Não  |

> [!TIP]
> Ao copiar dados para a Instância Gerenciada do Banco de Dados SQL do Azure, a atividade de cópia acrescenta dados à tabela de coletor por padrão. Para executar um UPSERT ou lógica de negócios adicional, use o procedimento armazenado no SqlSink. Obtenha mais detalhes de [Invocando o procedimento armazenado para o coletor SQL](#invoking-stored-procedure-for-sql-sink).

**Exemplo 1: anexar dados**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exemplo 2: invocar um procedimento armazenado durante a cópia para upsert**

Obtenha mais detalhes de [Invocando o procedimento armazenado para o coletor SQL](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade no banco de dados de destino

Esta seção fornece um exemplo que copia dados de uma tabela de origem sem uma coluna de identidade para uma tabela de destino com uma coluna de identidade.

**Tabela de origem**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Tabela de destino**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Observe que a tabela de destino tem uma coluna de identidade.

**Definição de JSON do conjunto de dados de origem**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Definição de JSON do conjunto de dados de destino**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Observe que sua tabela de origem e de destino têm um esquema diferente (a de destino tem uma coluna adicional com identidade). Nesse cenário, você precisa especificar a propriedade **structure** na definição de conjunto de dados de destino, que não inclui a coluna de identidade.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Invocar o procedimento armazenado do coletor SQL

Ao copiar dados na Instância Gerenciada do Banco de Dados SQL do Azure, um procedimento armazenado especificado pelo usuário poderá ser configurado e invocado com parâmetros adicionais.

Um procedimento armazenado pode ser usado quando os mecanismos de cópia internos não têm essa finalidade. Isso normalmente é usado ao realizar um upsert (inserção + atualização) ou um processamento adicional (mesclar colunas, pesquisar valores adicionais, inserção em várias tabelas, etc.) antes da inserção final dos dados de origem na tabela de destino.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert em uma tabela na Instância Gerenciada. Supondo que os dados de entrada e a tabela "Marketing" do coletor tem três colunas: ProfileID, State e Category. Execute o upsert com base na coluna "ProfileID" e aplique somente a uma categoria específica.

**Conjunto de dados de saída**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Defina a seção SqlSink na atividade de cópia conforme demonstrado a seguir.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

No banco de dados, defina o procedimento armazenado com o mesmo nome que SqlWriterStoredProcedureName. Ele lida com os dados de entrada da fonte especificada por você e os mescla na tabela de saída. O nome do parâmetro do tipo de tabela no procedimento armazenado deve ser o mesmo que o "tableName" definido no conjunto de dados.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

No banco de dados, defina o tipo de tabela com o mesmo nome que sqlWriterTableType. Observe que o esquema do tipo de tabela deve ser a mesmo que o esquema retornado por seus dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

O recurso de procedimento armazenado se beneficia de [parâmetros com valores de tabela](https://msdn.microsoft.com/library/bb675163.aspx).

>[!NOTE]
>Se você gravar no tipo de dados Money / Smallmoney invocando o procedimento armazenado, os valores podem ser arredondados. Especifique o tipo de dados correspondente em TVP como Decimal em vez de Money / Smallmoney para atenuar. 

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapeamento do tipo de dados da Instância Gerenciada do Banco de Dados SQL do Azure

Ao copiar dados da/para a Instância Gerenciada do Banco de Dados SQL do Azure, os seguintes mapeamentos são usados de tipos de dados da Instância Gerenciada para tipos de dados intermediários do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados da Instância Gerenciada do Banco de Dados SQL do Azure | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binário |Byte[] |
| bit |BOOLEAN |
| char |String, Char[] |
| data |Datetime |
| DateTime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte[] |
| Float |Duplo |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numérico |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objeto * |
| text |String, Char[] |
| tempo real |timespan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
