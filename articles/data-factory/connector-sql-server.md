---
title: Copiar dados de/para um SQL Server usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados de/para o banco de dados do SQL Server local ou em uma VM do Azure usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: cb1b8171dc45c286d3f87a3c33e366d818cfaad9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283402"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Copiar dados de e para um SQL Server usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-sqlserver-connector.md)
> * [Versão atual](connector-sql-server.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para um banco de dados SQL Server. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de/para o banco de dados do SQL Server para qualquer armazenamento de dados do coletor com suporte ou copiar dados de qualquer armazenamento de dados de origem com suporte para o banco de dados do SQL Server. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SQL Server dá suporte a:

- SQL Server verão 2016, 2014, 2012, 2008 R2, 2008 e 2005
- Cópia de dados usando a autenticação do **SQL** ou do **Windows**.
- Como fonte, dá suporte à recuperação de dados, usando a consulta SQL ou o procedimento armazenado.
- Como o coletor, ao acréscimo de dados na tabela de destino ou à invocação de um procedimento armazenado com lógica personalizada durante a cópia.

Não há suporte para o [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) do SQL Server neste momento.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a cópia de dados de um banco de dados do SQL Server que não está acessível publicamente, você precisa configurar um Integration Runtime auto-hospedado. Consulte o artigo [Self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Integration Runtime auto-hospedado) para obter detalhes. O Integration Runtime fornece um driver de banco de dados do SQL Server interno, portanto, não é necessário instalar manualmente qualquer driver ao copiar dados de/para no banco de dados do SQL Server.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do banco de dados SQL Server.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do SQL Server:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **SqlServer** | Sim |
| connectionString |Especifique as informações de connectionString necessárias para conexão com o banco de dados do SQL Server usando a autenticação SQL ou a autenticação do Windows. Consulte os exemplos a seguir.<br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. Você também pode colocar uma senha no Azure Key Vault, e se a autenticação for SQL, extraia a `password`configuração da cadeia de conexão. Veja o exemplo de JSON abaixo da tabela e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| userName |Especifique o nome de usuário se você estiver usando a Autenticação do Windows. Exemplo: **domainname\\username**. |Não  |
| Senha |Especifique a senha da conta de usuário que você especificou para userName. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Não  |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime auto-hospedado ou o Integration Runtime do Azure (se seu armazenamento de dados estiver publicamente acessível). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

>[!TIP]
>Se ocorrer erro com código de erro como "UserErrorFailedToConnectToSqlServer" e mensagem como "O limite da sessão para o banco de dados é XXX e foi atingido.", adicione `Pooling=false` à cadeia de conexão e tente novamente.

**Exemplo 1: usando a autenticação do SQL**

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

**Exemplo 2: usando a autenticação do SQL com senha no Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 3: usando a autenticação do Windows**

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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do SQL Server.

Para copiar dados de/para o banco de dados do SQL Server, defina a propriedade type do conjunto de dados como **SqlServerTable**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do conjunto de dados deve ser definida como: **SqlServerTable** | Sim |
| tableName |Nome da tabela ou exibição na instância do banco de dados SQL Server à qual o serviço vinculado se refere. | Não para fonte, Sim para o coletor |

**Exemplo:**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem e o coletor do SQL Server.

### <a name="sql-server-as-source"></a>SQL Server como origem

Para copiar dados do SQL Server, defina o tipo de origem na atividade de cópia como **SqlSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como: **SqlSource** | Sim |
| SqlReaderQuery |Utiliza a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. |Não  |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. |Não  |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são: pares nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não  |

**Pontos a serem observados:**

- Se **sqlReaderQuery** for especificado para SqlSource, a atividade de cópia executará essa consulta na origem do SQL Server para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).
- Se você não especificar "sqlReaderQuery" ou "sqlReaderStoredProcedureName", as colunas definidas na seção “structure” do JSON do conjunto de dados serão usadas para criar uma consulta (`select column1, column2 from mytable`) para executar no SQL Server. Se a definição de conjunto de dados não tiver a seção "structure", todas as colunas serão selecionadas da tabela.

**Exemplo: usando a consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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

**Exemplo: usando o procedimento armazenado**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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

**Definição do procedimento armazenado:**

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

### <a name="sql-server-as-sink"></a>SQL Server como coletor

Para copiar dados para o SQL Server, defina o tipo de coletor na atividade de cópia como **SqlSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do coletor da atividade de cópia deve ser definida como: **SqlSink** | Sim |
| writeBatchSize |Número de linhas para inserções na tabela SQL **por lote**.<br/>Os valores permitidos são: inteiro (número de linhas). |Não (padrão: 10000) |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são: período. Exemplo: “00:30:00” (30 minutos). |Não  |
| preCopyScript |Especifica uma consulta SQL para a atividade de cópia executar antes da gravação dos dados no SQL Server. Isso será invocado somente uma vez por execução de cópia. Você pode usar essa propriedade para limpar os dados previamente carregados. |Não  |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que define como aplicar os dados de origem à tabela de destino, por exemplo, para fazer upserts ou transformações usando sua própria lógica de negócios. <br/><br/>Observe que esse procedimento armazenado será **invocado por lote**. Se você deseja executar uma operação que é executada apenas uma vez e que não tem nenhuma relação com os dados de origem, por exemplo, excluir/truncar, use a propriedade `preCopyScript`. |Não  |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são: pares nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não  |
| sqlWriterTableType |Especifique um nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados sendo copiados com dados existentes. |Não  |

> [!TIP]
> Ao copiar dados para o SQL Server, a atividade de cópia acrescenta dados à tabela de coletor por padrão. Para executar um UPSERT ou lógica de negócios adicional, use o procedimento armazenado no SqlSink. Obtenha mais detalhes de [Invocando o procedimento armazenado para o coletor SQL](#invoking-stored-procedure-for-sql-sink).

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
                "referenceName": "<SQL Server output dataset name>",
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
                "referenceName": "<SQL Server output dataset name>",
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

**Tabela de origem:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```

**Tabela de destino:**

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

**Definição de JSON de conjunto de dados de destino**

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

Ao copiar dados no banco de dados SQL Server, um procedimento armazenado especificado pelo usuário poderá ser configurado e invocado com parâmetros adicionais.

Um procedimento armazenado pode ser usado quando os mecanismos de cópia internos não têm essa finalidade. Isso normalmente é usado ao realizar um upsert (inserção + atualização) ou um processamento adicional (mesclar colunas, pesquisar valores adicionais, inserção em várias tabelas, etc.) antes da inserção final dos dados de origem na tabela de destino.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert em uma tabela no banco de dados do SQL Server. Supondo que os dados de entrada e cada tabela **Marketing** do coletor tenham três colunas: **ProfileID**, **State** e **Category**. Faça o upsert com base na coluna **ProfileID** e aplique-o apenas a uma categoria específica.

**Conjunto de dados de saída:** "tableName" deve ser o mesmo nome de parâmetro de tipo de tabela em seu procedimento armazenado (veja abaixo o script de procedimento armazenado).

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Definir as **coletor SQL** seção na atividade de cópia da seguinte maneira.

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

Em seu banco de dados, defina o procedimento armazenado com o mesmo nome que o **SqlWriterStoredProcedureName**. Ele manipula os dados de entrada da sua origem especificada e mescla na tabela de saída. O nome do parâmetro do tipo de tabela no procedimento armazenado deve ser o mesmo que o **tableName** definido no conjunto de dados.

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
      VALUES (source.ProfileID, source.State, source.Category);
END
```

No banco de dados, defina o tipo de tabela com o mesmo nome que sqlWriterTableType. Observe que o esquema do tipo de tabela deve ser a mesmo que o esquema retornado por seus dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

O recurso de procedimento armazenado se beneficia de [parâmetros com valores de tabela](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Mapeamento de tipo de dados para o SQL Server

Ao copiar dados do/para o SQL Server, os seguintes mapeamentos são usados de tipos de dados do SQL Server para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados do SQL Server | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binário |Byte[] |
| bit |BOOLEAN |
| char |String, Char[] |
| data |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numérico |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objeto |
| text |String, Char[] |
| tempo real |TimeSpan |
|  timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

>[!NOTE]
> Em mapas de tipos de dados para o tipo provisório Decimal, atualmente o ADF dá suporte a uma precisão de até 28. Se você tiver dados com precisão maior do que 28, considere a conversão da cadeia de caracteres em consulta SQL.

## <a name="troubleshooting-connection-issues"></a>Solucionar problemas de conexão

1. Configure seu SQL Server para aceitar conexões remotas. Inicie o **SQL Server Management Studio**, clique com o botão direito do mouse em **servidor** e clique em **Propriedades**. Selecione **Conexões** na lista e marque a opção **Permitir conexões remotas com o servidor**.

    ![Habilitar conexões remotas](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Veja [Configurar a Opção de Configuração do Servidor de acesso remoto](https://msdn.microsoft.com/library/ms191464.aspx) para obter as etapas detalhadas.

2. Inicie o **SQL Server Configuration Manager**(Gerenciador de Configuração do SQL Server). Expanda **Configuração de Rede do SQL Server** para a instância desejada e selecione **Protocolos para MSSQLSERVER**. Você deve ver os protocolos no painel à direita. Habilite o TCP/IP clicando com o botão direito do mouse em **TCP/IP** e clicando em **Habilitar**.

    ![Habilitar TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Veja [Habilitar ou Desabilitar um Protocolo de Rede do Servidor](https://msdn.microsoft.com/library/ms191294.aspx) para ver detalhes e formas alternativas de habilitar um protocolo TCP/IP.

3. Na mesma janela, clique duas vezes em **TCP/IP** para iniciar a janela **Propriedades de TCP/IP**.
4. Alterne para a guia **Endereços IP** . Role para baixo para ver a seção **IPAll** . Anote a **Porta TCP** (o padrão é **1433**).
5. Crie uma **regra para o Firewall do Windows** no computador para permitir a entrada de tráfego por essa porta.  
6. **Verificar a conexão**: para se conectar ao SQL Server usando um nome totalmente qualificado, use o SQL Server Management Studio de um computador diferente. Por exemplo: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Próximos passos
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
