---
title: Copiar dados para ou do Banco de Dados SQL do Azure usando o Data Factory | Microsoft Docs
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte para o Banco de Dados SQL do Azure ou do Banco de Dados SQL do Azure para armazenamentos de dados de coletor com suporte, usando o Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: jingwang
ms.openlocfilehash: 24fdfcb53e8f3cbf0e1bf4f7e567d9f768383ac1
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884224"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copiar dados de ou para o Banco de Dados SQL do Azure usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [Versão 1](v1/data-factory-azure-sql-connector.md)
> * [Versão atual](connector-azure-sql-database.md)

Este artigo explica como usar a atividade de cópia na fábrica de dados do Azure para copiar dados de ou para o banco de dados do SQL Azure. Ele amplia a [visão geral da atividade de cópia](copy-activity-overview.md) artigo, que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de ou para o Banco de Dados SQL do Azure para qualquer repositório de dados de coletores com suporte. E você pode copiar dados de qualquer repositório de dados de origem com suporte para o Banco de Dados SQL do Azure. Para obter uma lista de armazenamentos de dados que são suportados como fontes ou coletores por Atividade de Cópia, consulte a tabela [Armazenamentos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector de banco de dados SQL dá suporte a estas funções:

- Copie os dados usando a autenticação de token do Aplicativo Azure AD (Azure Active Directory) e autenticação do SQL com uma entidade de serviço ou identidades gerenciadas para recursos do Azure.
- Como uma fonte, recupere dados usando uma consulta SQL ou procedimento armazenado.
- Como um coletor, anexe os dados a uma tabela de destino ou chame um procedimento armazenado com lógica personalizada durante a cópia.

O [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) do Banco de Dados SQL do Azure não é aceito agora. 

> [!IMPORTANT]
> Se você copiar dados usando o Tempo de Execução de Integração do Azure Data Factory, configure um [firewall do servidor SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para que os Serviços do Azure possam acessar o servidor.
> Se você copiar dados usando um tempo de execução de integração auto-hospedado, configure o firewall do servidor SQL do Azure para permitir o intervalo de IP apropriado. Esse intervalo inclui o IP da máquina usado para se conectar ao Banco de Dados SQL do Azure.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas de um conector do Banco de Dados SQL do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

Essas propriedades são suportadas por um serviço vinculado ao Banco de Dados SQL do Azure:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade  **tipo** deve ser definida como **AzureSqlDatabase**. | SIM |
| connectionString | Especifique as informações necessárias para se conectar à instância do Banco de Dados SQL do Azure para a propriedade **connectionString**. Marque esse campo como **SecureString** para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | SIM |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque esse campo como **SecureString** para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| locatário | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Para recuperá-lo, passe o mouse no canto superior direito do portal do Azure. | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Tempo de Execução de Integração do Azure ou um tempo de execução de integração auto-hospedado se o seu armazenamento de dados estiver localizado em uma rede privada. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não  |

Para diferentes tipos de autenticação, consulte as seções a seguir sobre pré-requisitos e amostras JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- [Autenticação de token do aplicativo Azure AD: Entidade de serviço](#service-principal-authentication)
- [Autenticação de token do aplicativo Azure AD: identidades gerenciadas para recursos do Azure](#managed-identity)

>[!TIP]
>Se ocorrer erro com código de erro como "UserErrorFailedToConnectToSqlServer" e mensagem como "O limite da sessão para o banco de dados é XXX e foi atingido.", adicione `Pooling=false` à cadeia de conexão e tente novamente.

### <a name="sql-authentication"></a>Autenticação do SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exemplo de serviço vinculado que usa autenticação SQL

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

### <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

Para usar uma autenticação de token de aplicativo do Azure AD baseada no serviço principal, siga estas etapas:

1. **[Crie um aplicativo do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** no portal do Azure. Anote o nome do aplicativo e os seguintes valores que definem o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

1. **[Provisione um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** para o servidor SQL do Azure no Portal do Azure, caso ainda não tenha feito isso. O administrador do Azure AD deve ser um usuário do Azure AD ou um grupo do Azure AD, mas não pode ser um diretor de serviços. Essa etapa é feita para que, na próxima etapa, você possa usar uma identidade do Azure AD para criar um usuário de banco de dados contido para a entidade de serviço.

1. **[ Crie usuários de banco de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** para o diretor de serviços. Conecte-se ao banco de dados de ou para o qual você deseja copiar dados usando ferramentas como SSMS, com uma identidade do Azure AD que tenha pelo menos a permissão ALTER ANY USER. Execute o seguinte T-SQL: 
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

1. **Conceda ao principal de serviço as permissões necessárias**, como faria normalmente para usuários do SQL ou outros. Execute o código a seguir:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

1. **Configure um serviço vinculado do Banco de Dados SQL do Azure** no Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemplo de serviço vinculado que usa autenticação principal de serviço

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

### <a name="managed-identity"></a> Identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar essa identidade de serviço para a autenticação do Banco de Dados SQL do Azure. A fábrica designada pode acessar e copiar dados de ou para seu banco de dados usando essa identidade.

Para usar a autenticação de token do aplicativo do Azure AD com base em MSI, siga estas etapas:

1. **Crie um grupo no AD do Azure.** Faça da fábrica MSI um membro do grupo.
    
    1. Encontre a identidade do serviço de fábrica de dados no portal do Azure. Vá para as **Propriedades** da sua data factory. Copie o ID da IDENTIDADE DO SERVIÇO.
    
    1. Instale o módulo do [PowerShell do Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2). Entre usando o comando `Connect-AzureAD`. Execute os seguintes comandos para criar um grupo e inclua o MSI do data factory como membro.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```
    
1. **[Provisione um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** para o servidor SQL do Azure no Portal do Azure, caso ainda não tenha feito isso. O administrador do Azure AD pode ser um usuário do AD do Azure ou um grupo do Azure AD. Se você conceder ao grupo com o MSI uma função administrativa, pule as etapas 3 e 4. O administrador terá acesso total ao banco de dados.

1. **[ Crie usuários de banco de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** para o grupo do AD do Azure. Conecte-se ao banco de dados de ou para o qual você deseja copiar dados usando ferramentas como SSMS, com uma identidade do Azure AD que tenha pelo menos a permissão ALTER ANY USER. Execute o seguinte T-SQL: 
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

1. **Conceda ao grupo do AD do Azure as permissões necessárias**, como faria normalmente para usuários do SQL e outros usuários. For example, run the following code:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

1. **Configure um serviço vinculado do Banco de Dados SQL do Azure** no Azure Data Factory.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>Exemplo de serviço vinculado que usa autenticação MSI

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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). Esta seção fornece uma lista de propriedades com suporte pelo conjunto de dados do banco de dados SQL.

Para copiar dados de ou para o Banco de Dados SQL do Azure, defina a propriedade **tipo** do conjunto de dados para **AzureSqlTable**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade **tipo** do conjunto de dados deve ser definida como **AzureSqlTable**. | SIM |
| tableName | O nome da tabela ou exibição na instância do Banco de Dados SQL do Azure à qual o serviço vinculado se refere. | Não para fonte, Sim para o coletor |

#### <a name="dataset-properties-example"></a>Exemplo de propriedades do conjunto de dados

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

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela origem e pelo coletor do Banco de Dados SQL do Azure.

### <a name="azure-sql-database-as-the-source"></a>Banco de Dados SQL do Azure como a origem

Para copiar dados do Banco de Dados SQL do Azure, defina a propriedade **tipo** na origem da Atividade de Cópia para **SqlSource**. As seguintes propriedades são suportadas na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade **tipo** da origem da Atividade de Cópia deve ser definida como **SqlSource**. | SIM |
| SqlReaderQuery | Utiliza a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. | Não  |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. | Não  |
| storedProcedureParameters | Parâmetros para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. | Não  |

### <a name="points-to-note"></a>Pontos a serem observados

- Se **sqlReaderQuery** for especificado para o **SqlSource**, o Copy Activity executará essa consulta em relação à origem do Banco de Dados SQL do Azure para obter os dados. Ou você pode especificar um procedimento armazenado. Especifique **sqlReaderStoredProcedureName** e **storedProcedureParameters** se o procedimento armazenado receber parâmetros.
- Se você não especificar **sqlReaderQuery** ou **sqlReaderStoredProcedureName**, as colunas definidas na seção **structure** do conjunto de dados JSON serão usadas para construir uma consulta. `select column1, column2 from mytable`é executado no Banco de Dados SQL do Azure. Se a definição do conjunto de dados não tiver a **estrutura**, todas as colunas serão selecionadas da tabela.

#### <a name="sql-query-example"></a>Exemplo de consulta SQL

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

#### <a name="stored-procedure-example"></a>Exemplo de procedimento armazenado

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

### <a name="stored-procedure-definition"></a>Definição do procedimento armazenado

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

### <a name="azure-sql-database-as-the-sink"></a>Banco de Dados SQL do Azure como o coletor

Para copiar dados para o banco de dados do SQL Azure, defina o **tipo** do coletor de propriedade na atividade de cópia para **SqlSink**. As seguintes propriedades são suportadas na seção Copy Activity **sink**:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | O **tipo** do coletor de atividade de cópia deve ser definida como **SqlSink**. | SIM |
| writeBatchSize | Insere dados na tabela SQL quando o tamanho do buffer atinge **writeBatchSize** .<br/> O valor permitido é **inteiro** (número de linhas). |  Não. O padrão é 10000. |
| writeBatchTimeout | O tempo de espera para o lote inserir operação seja concluída antes de expirar.<br/> O valor permitido é **timespan**. Exemplo: “00:30:00” (30 minutos). | Não  |
| preCopyScript | Especifique uma consulta SQL para que a Atividade de Cópia seja executada antes de gravar dados no Banco de Dados SQL do Azure. É invocado apenas uma vez por cópia. Use essa propriedade para limpar os dados pré-carregados. | Não  |
| sqlWriterStoredProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem em uma tabela de destino. Um exemplo é fazer upserts ou transformar usando sua própria lógica de negócios. <br/><br/>Este procedimento armazenado é **chamado por lote**. Para operações que são executadas apenas uma vez e não têm nada a ver com dados de origem, use a `preCopyScript` propriedade. Exemplos de operações são excluir e truncar. | Não  |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Valores permitidos são pares de nome e valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. | Não  |
| sqlWriterTableType | Especifique um nome do tipo de tabela a ser usado no procedimento armazenado. A Atividade de Cópia torna os dados movidos disponíveis em uma tabela temporária com este tipo de tabela. O código de procedimento armazenado pode mesclar os dados sendo copiados com dados existentes. | Não  |

> [!TIP]
> Quando você copia dados para o Banco de Dados SQL do Azure, a Atividade de Cópia anexa dados à tabela do coletor por padrão. Para fazer um upsert ou lógica de negócios adicional, use o procedimento armazenado em **SqlSink**. Saiba mais detalhes em [Invocando o procedimento armazenado do SQL Sink](#invoking-stored-procedure-for-sql-sink).

#### <a name="append-data-example"></a>Anexar exemplo de dados

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

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>Chamar um procedimento armazenado durante a cópia para o exemplo do upsert

Saiba mais detalhes em [Invocando o procedimento armazenado do SQL Sink](#invoking-stored-procedure-for-sql-sink).

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

Esta seção mostra como copiar dados de uma tabela de origem sem uma coluna de identidade para uma tabela de destino com uma coluna de identidade.

#### <a name="source-table"></a>Tabela de origem

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

#### <a name="destination-table"></a>Tabela de destino

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

> [!NOTE]
> A tabela de destino possui uma coluna de identidade.

#### <a name="source-dataset-json-definition"></a>Definição de JSON do conjunto de dados de origem

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

#### <a name="destination-dataset-json-definition"></a>Definição de JSON de conjunto de dados de destino

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

> [!NOTE]
> Sua tabela de origem e destino tem esquema diferente. 

O alvo tem uma coluna adicional com uma identidade. Nesse cenário, você deve especificar a propriedade **estrutura** na definição do conjunto de dados de destino, que não inclui a coluna de identidade.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Invocar o procedimento armazenado do coletor SQL

Quando você copiar dados para o banco de dados do SQL Azure, você também pode configurar e invocar um procedimento armazenado especificado pelo usuário com parâmetros adicionais.

Você pode usar um procedimento armazenado quando os mecanismos internos de cópia não atendem à finalidade. Eles são normalmente usados quando um upsert, insert plus update ou processamento extra deve ser feito antes da inserção final dos dados de origem na tabela de destino. Alguns exemplos de processamento extra são colunas de mesclagem, pesquisa valores adicionais e inserção em mais de uma tabela.

A amostra a seguir mostra como usar um procedimento armazenado para fazer um upsert em uma tabela no Banco de Dados SQL do Azure. Supondo que os dados de entrada e cada tabela **Marketing** do coletor tenham três colunas: **ProfileID**, **State** e **Category**. Faça o upsert com base na coluna **ProfileID** e aplique-o apenas a uma categoria específica.

#### <a name="output-dataset"></a>Conjunto de dados de saída

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

Defina a seção **SqlSink** em Atividade de Cópia:

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
      VALUES (source.ProfileID, source.State, source.Category)
END
```

No banco de dados, definir o tipo de tabela com o mesmo nome que o **sqlWriterTableType**. O esquema do tipo de tabela deve ser o mesmo que o esquema retornado pelos dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL,
)
```

O recurso de procedimento armazenado se beneficia de [parâmetros com valores de tabela](https://msdn.microsoft.com/library/bb675163.aspx).

>[!NOTE]
>Se você gravar no tipo de dados Money / Smallmoney invocando o procedimento armazenado, os valores podem ser arredondados. Especifique o tipo de dados correspondente em TVP como Decimal em vez de Money / Smallmoney para atenuar. 

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapeamento do tipo de dados do Banco de Dados SQL do Azure

Quando você copia dados de ou para o Banco de Dados SQL do Azure, os seguintes mapeamentos são usados dos tipos de dados do Banco de Dados SQL do Azure para os tipos de dados temporários do Azure Data Factory. Consulte [Esquema e mapeamentos de tipo de dados](copy-activity-schema-and-type-mapping.md) para saber como a Atividade de Cópia mapeia o esquema de origem e o tipo de dados para o coletor.

| Tipo de dados do Banco de Dados SQL do Azure | Tipo de dados provisório do Data Factory |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

>[!NOTE]
> Em mapas de tipos de dados para o tipo provisório Decimal, atualmente o ADF dá suporte a uma precisão de até 28. Se você tiver dados com precisão maior do que 28, considere a conversão da cadeia de caracteres em consulta SQL.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de repositórios de dados com suporte como fontes e repositórios por Atividade de Cópia no Azure Data Factory, consulte [repositórios de dados e formatos compatíveis](copy-activity-overview.md##supported-data-stores-and-formats).
