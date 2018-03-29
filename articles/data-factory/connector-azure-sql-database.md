---
title: Copiar dados para/do Banco de Dados SQL do Azure usando o Data Factory.| Microsoft Docs
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte para o Banco de Dados SQL do Azure ou do Banco de Dados SQL do Azure para armazenamentos de dados de coletor com suporte, usando o Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: jingwang
ms.openlocfilehash: 12f673a8d3ca9c0bb03b9cd2d8c33ae866039289
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copiar dados de ou para o Banco de Dados SQL do Azure usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-azure-sql-connector.md)
> * [Versão 2 – Versão prévia](connector-azure-sql-database.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para um Banco de Dados SQL do Azure. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está em GA (disponibilidade geral), consulte [Conector do Banco de Dados SQL do Azure na V1](v1/data-factory-azure-sql-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de/para o Banco de Dados SQL do Azure para qualquer armazenamento de dados de coletor com suporte ou copiar dados de qualquer armazenamento de dados de origem com suporte para o Banco de Dados SQL do Azure. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Banco de Dados SQL do Azure dá suporte a:

- Copiar dados usando **autenticação SQL** e **autenticação de token de aplicativo do Azure Active Directory** com Entidade de Serviço ou a Identidade de Serviço Gerenciado (MSI).
- Como fonte, dá suporte à recuperação de dados, usando a consulta SQL ou o procedimento armazenado.
- Como o coletor, ao acréscimo de dados na tabela de destino ou à invocação de um procedimento armazenado com lógica personalizada durante a cópia.

> [!IMPORTANT]
> Se você copiar dados usando o Integration Runtime do Azure, configure o [Firewall de SQL Server do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para [permitir que os serviços do Azure acessem o servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Se você copiar dados usando o Microsoft Integration Runtime auto-hospedado, configure o firewall do Azure SQL Server para permitir o intervalo de IP apropriado, incluindo o IP do computador que é usado para se conectar ao banco de dados do SQL Azure.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Banco de Dados SQL do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Banco de Dados SQL do Azure:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **AzureSqlDatabase** | sim |
| connectionString |Especifique as informações necessárias para se conectar à instância do Banco de Dados SQL Azure para a propriedade connectionString. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim, ao usar a autenticação do AAD com Entidade de Serviço. |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim, ao usar a autenticação do AAD com Entidade de Serviço. |
| locatário | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | Sim, ao usar a autenticação do AAD com Entidade de Serviço. |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

Para diferentes tipos de autenticação, consulte as seções a seguir sobre pré-requisitos e amostras JSON, respectivamente:

- [Usando a autenticação SQL](#using-sql-authentication)
- [Usando a autenticação de token do aplicativo AAD – entidade de serviço](#using-service-principal-authentication)
- [Usando a autenticação de token do aplicativo AAD – identidade do serviço gerenciado](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>Usando a autenticação SQL

**Exemplo de serviço vinculado usando a autenticação SQL:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-principal-authentication"></a>Usar a autenticação de entidade de serviço

Para usar a autenticação de token do aplicativo AAD com base em entidade de serviço, siga estas etapas:

1. **[Criar um aplicativo do Azure Active Directory no portal do Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).**  Anote o nome do aplicativo e os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. **[Provisione um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)** para seu Azure SQL Server usando o portal do Azure se ainda não fez isso. O administrador AAD deve ser um usuário do AAD ou grupo do AAD, mas não pode ser uma entidade de serviço. Esta etapa é feita para que, na próxima etapa, você possa usar uma identidade do AAD para criar um usuário de banco de dados independente para a entidade de serviço.

3. **Crie um usuário de banco de dados independente para a entidade de serviço**, conectando-se ao banco de dados de/para o qual você deseja copiar dados usando ferramentas como o SSMS, com uma identidade de AAD com pelo a permissão ALTER ANY USER, e execute o T-SQL a seguir. Saiba mais sobre o usuário de banco de dados independente [aqui](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Conceda as permissões necessárias para a entidade de serviço** como faria normalmente para usuários do SQL, por exemplo, executando abaixo:

    ```sql
    EXEC sp_addrolemember '[your application name]', 'readonlyuser';
    ```

5. Em ADF, configure um serviço vinculado do Banco de Dados SQL do Azure.


**Exemplo de serviço vinculado usando a autenticação da entidade de serviço:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-managed-service-identity-authentication"></a>Usar autenticação de identidade do serviço gerenciado

Uma data factory pode ser associada a uma [identidade de serviço gerenciada (MSI)](data-factory-service-identity.md), que representa a data factory específica. Você pode usar essa identidade de serviço para autenticação de banco de dados SQL, que permite que esta factory designada acesse e copie os dados de/para seu banco de dados.

Para usar a autenticação de token do aplicativo AAD com base em MSI, siga estas etapas:

1. **Crie um grupo no Azure AD e torne a MSI da factory um membro do grupo**.

    a. Localize a identidade de serviço da data factory no portal do Azure. Acesse sua data factory -> Propriedades -> copiar a **ID DE IDENTIDADE DE SERVIÇO**.

    b. Instale o módulo [PowerShell do Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2), entre usando o comando `Connect-AzureAD` e execute os comandos a seguir para criar um grupo e adicionar a MSI da data factory como um membro.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Provisione um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)** para seu Azure SQL Server usando o Portal do Azure se ainda não fez isso. O administrador do AAD pode ser um usuário ou grupo do AAD. Se você conceder ao grupo com MSI uma função administrativa, ignore as etapas 3 e 4 abaixo, pois o administrador deve ter acesso completo ao banco de dados.

3. **Crie um usuário de banco de dados independente para o grupo do AAD**, conectando-se ao banco de dados de/para o qual você deseja copiar dados usando ferramentas como o SSMS, com uma identidade de AAD com pelo a permissão ALTER ANY USER, e execute o T-SQL a seguir. Saiba mais sobre o usuário de banco de dados independente [aqui](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Conceda as permissões necessárias para o grupo do AAD**  como faria normalmente para usuários do SQL, por exemplo, executando abaixo:

    ```sql
    EXEC sp_addrolemember '[your AAD group name]', 'readonlyuser';
    ```

5. Em ADF, configure um serviço vinculado do Banco de Dados SQL do Azure.

**Exemplo de serviço vinculado usando a autenticação do MSI:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Banco de Dados SQL do Azure.

Para copiar dados de/para o Banco de Dados SQL do Azure, defina a propriedade type do conjunto de dados como **AzureSqlTable**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do conjunto de dados deve ser definida como: **AzureSqlTable** | sim |
| tableName |Nome da tabela ou exibição na instância do Banco de Dados SQL Azure à qual o serviço vinculado se refere. | sim |

**Exemplo:**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pelo coletor e pela fonte do Banco de Dados SQL do Azure.

### <a name="azure-sql-database-as-source"></a>Banco de Dados SQL do Azure como fonte

Para copiar dados do Banco de Dados SQL do Azure, defina o tipo de fonte na atividade de cópia como **SqlSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como: **SqlSource** | sim |
| SqlReaderQuery |Utiliza a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. |Não  |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. |Não  |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são: pares nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não  |

**Pontos a serem observados:**

- Se **sqlReaderQuery** for especificado para SqlSource, a Atividade de Cópia executa essa consulta em relação à fonte do Banco de Dados SQL do Azure para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).
- Se você não especificar "sqlReaderQuery" nem "sqlReaderStoredProcedureName", as colunas definidas na seção "estrutura" do JSON do conjunto de dados serão usadas para criar uma consulta (`select column1, column2 from mytable`) para ser executada no Banco de Dados SQL do Azure. Se a definição de conjunto de dados não tiver a seção "structure", todas as colunas serão selecionadas da tabela.
- Quando você usa **sqlReaderStoredProcedureName**, ainda é necessário especificar uma propriedade **tableName** fictícia no JSON do conjunto de dados.

**Exemplo: usando a consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

**A definição do procedimento armazenado:**

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

### <a name="azure-sql-database-as-sink"></a>Banco de Dados SQL do Azure como coletor

Para copiar dados para o Banco de Dados SQL do Azure, defina o tipo de coletor na atividade de cópia como **SqlSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | O tipo de propriedade do coletor de atividade de cópia deve ser definido como: **SqlSink** | sim |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize.<br/>Os valores permitidos são: inteiro (número de linhas). |Não (o padrão é 10000) |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são: período. Exemplo: "00:30:00" (30 minutos). |Não  |
| preCopyScript |Especifica uma consulta SQL para a atividade de cópia executar antes da gravação dos dados no SQL Server. Isso será invocado somente uma vez por execução de cópia. Você pode usar essa propriedade para limpar os dados previamente carregados. |Não  |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que define como aplicar os dados de origem à tabela de destino, por exemplo, para fazer upserts ou transformações usando sua própria lógica de negócios. <br/><br/>Observe que esse procedimento armazenado será **invocado por lote**. Se você deseja executar uma operação que é executada apenas uma vez e que não tem nenhuma relação com os dados de origem, por exemplo, excluir/truncar, use a propriedade `preCopyScript`. |Não  |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são: pares nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não  |
| sqlWriterTableType |Especifique um nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados sendo copiados com dados existentes. |Não  |

> [!TIP]
> Ao copiar dados para o Banco de Dados SQL do Azure, a atividade de cópia acrescenta dados à tabela de coletor por padrão. Para executar um UPSERT ou lógica de negócios adicional, use o procedimento armazenado no SqlSink. Obtenha mais detalhes de [Invocando o procedimento armazenado para o coletor SQL](#invoking-stored-procedure-for-sql-sink).

**Exemplo 1: anexar dados**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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

Esta seção fornece um exemplo para copiar dados de uma tabela de origem sem uma coluna de identidade para uma tabela de destino com uma coluna de identidade.

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
        "type": " AzureSqlTable",
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
        "type": "AzureSqlTable",
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

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocar o procedimento armazenado do coletor SQL

Ao copiar dados no Banco de Dados SQL do Azure, um procedimento armazenado especificado pelo usuário poderá ser configurado e invocado com parâmetros adicionais.

Um procedimento armazenado pode ser usado quando os mecanismos de cópia internos não têm essa finalidade. Isso normalmente é usado ao realizar um upsert (inserção + atualização) ou um processamento adicional (mesclar colunas, pesquisar valores adicionais, inserção em várias tabelas, etc.) antes da inserção final dos dados de origem na tabela de destino.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert em uma tabela no Banco de Dados SQL do Azure. Supondo que os dados de entrada e a tabela "Marketing" do coletor tem três colunas: ProfileID, State e Category. Execute o upsert com base na coluna "ProfileID" e aplique somente a uma categoria específica.

**Conjunto de dados de saída**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Defina a seção "SqlSink" na atividade de cópia conforme demonstrado a seguir.

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

No banco de dados, defina o procedimento armazenado com o mesmo nome que "SqlWriterStoredProcedureName". Ele lida com os dados de entrada da fonte especificada por você e os mescla na tabela de saída. Observe que o nome de parâmetro do procedimento armazenado deve ser igual ao "tableName" definido no conjunto de dados.

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

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapeamento do tipo de dados do Banco de Dados SQL do Azure

Ao copiar dados do/para o Banco de Dados SQL do Azure, os seguintes mapeamentos são usados de tipos de dados do Banco de Dados SQL do Azure para tipos de dados intermediários do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados do Banco de Dados SQL do Azure | Tipo de dados provisório do Data Factory |
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
| imagem |Byte[] |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).