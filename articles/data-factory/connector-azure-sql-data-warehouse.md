---
title: Copiar dados de e para o SQL Data Warehouse do Azure usando o Azure Data Factory | Microsoft Docs
description: Aprenda como copiar dados de armazenamentos de fontes suportados para o SQL Data Warehouse do Azure ou do SQL Data Warehouse para armazenamentos de coletores suportados usando o Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: jingwang
ms.openlocfilehash: e3fc5a3dc5dc40078ca3a4733f6a2ba11da450f1
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681209"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiar dados de e para o SQL Data Warehouse do Azure usando o Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you're using:"]
> * [Versão 1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão atual](connector-azure-sql-data-warehouse.md)

Este artigo explica como usar a atividade de cópia no Azure Data Factory para copiar dados para ou do Azure SQL Data Warehouse. Ele amplia o artigo [visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do SQL Data Warehouse do Azure para qualquer repositório de dados do coletor com suporte. E você pode copiar dados de qualquer repositório de dados de origem com suporte para o SQL Data Warehouse do Azure. Para obter uma lista de armazenamentos de dados que são suportados como fontes ou coletores por Atividade de Cópia, consulte a tabela [Armazenamentos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, esse conector do Azure SQL Data Warehouse suporta estas funções:

- Copie os dados usando a autenticação de token do Aplicativo Azure AD (Azure Active Directory) e autenticação do SQL com uma entidade de serviço ou identidades gerenciadas para recursos do Azure.
- Como uma fonte, recupere dados usando uma consulta SQL ou procedimento armazenado.
- Como um coletor, carregue dados usando o PolyBase ou uma inserção em massa. Recomendamos o PolyBase para um melhor desempenho de cópia.

> [!IMPORTANT]
> Observe que o PolyBase suporta somente a autenticação do SQL, mas não a autenticação do Azure AD.

> [!IMPORTANT]
> Se você copiar dados usando o Tempo de Execução de Integração do Azure Data Factory, configure um [firewall do servidor SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para que os serviços do Azure possam acessar o servidor.
> Se você copiar dados usando um tempo de execução de integração auto-hospedado, configure o firewall do servidor SQL do Azure para permitir o intervalo de IP apropriado. Esse intervalo inclui o IP da máquina usado para se conectar ao Banco de Dados SQL do Azure.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter melhor desempenho, use o PolyBase para carregar dados no SQL Data Warehouse. A seção [Use PolyBase to load data into Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) (Usar o PolyBase para carregar dados para o Azure SQL Data Warehouse) apresenta os detalhes. Para ver um passo a passo com um caso de uso, veja [Load 1 TB into Azure SQL Data Warehouse under 15 minutes with Azure Data Factory](load-azure-sql-data-warehouse.md) (Carregar 1 TB no SQL Data Warehouse do Azure em menos de 15 minutos com o Azure Data Factory).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que definem entidades do Data Factory específicas para um conector do Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para um serviço vinculado do Data Warehouse SQL do Azure:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como  **AzureSqlDW**. | Sim |
| connectionString | Especifique as informações necessárias para conectar-se à instância do Azure SQL Data Warehouse para a propriedade  **connectionString**. <br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. Você também pode colocar uma senha/chave da entidade de serviço no Azure Key Vault e se sua autenticação do SQL efetua pull da configuração da `password` da cadeia de conexão. Veja o exemplo de JSON abaixo da tabela e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) que fornece mais detalhes. | Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| tenant | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-lo focalizando o canto superior direito do portal do Azure. | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Tempo de Execução de Integração do Azure ou um tempo de execução de integração auto-hospedado (se o seu armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não  |

Para diferentes tipos de autenticação, consulte as seções a seguir sobre pré-requisitos e amostras JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- Uso da autenticação de token do aplicativo Azure Active Directory: [Entidade de serviço](#service-principal-authentication)
- Uso da autenticação de token do aplicativo Azure Active Directory: [Identidades gerenciadas para os recursos do Azure](#managed-identity)

>[!TIP]
>Se ocorrer erro com código de erro como "UserErrorFailedToConnectToSqlServer" e mensagem como "O limite da sessão para o banco de dados é XXX e foi atingido.", adicione `Pooling=false` à cadeia de conexão e tente novamente.

### <a name="sql-authentication"></a>Autenticação do SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exemplo de serviço vinculado que usa autenticação SQL

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

**Senha no Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

### <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

Para usar a autenticação de token de aplicativo do Azure AD com base em entidade de serviço, siga estas etapas:

1. **[ Crie um aplicativo do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** no portal do Azure. Anote o nome do aplicativo e os seguintes valores que definem o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. **[Provisione um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** para o servidor SQL do Azure no Portal do Azure, caso ainda não tenha feito isso. O administrador do Azure AD pode ser um usuário do AD do Azure ou um grupo do Azure AD. Se você conceder ao grupo com uma função de administrador de identidade gerenciada, ignore as etapas 3 e 4. O administrador terá acesso total ao banco de dados.

3. **[Crie usuários de banco de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** para o diretor de serviços. Conecte-se ao data warehouse de ou para o qual você deseja copiar dados usando ferramentas como SSMS, com uma identidade do Azure AD que tenha pelo menos a permissão ALTER ANY USER. Execute o seguinte T-SQL:
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Conceda ao principal de serviço as permissões necessárias**, como faria normalmente para usuários do SQL ou outros. Execute o seguinte código, ou consulte para obter mais opções [aqui](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Configure um serviço vinculado do Data Warehouse SQL do Azure** no Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemplo de serviço vinculado que usa autenticação principal de serviço

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa o factory específico. Você pode usar essa identidade gerenciada para a autenticação do Azure SQL Data Warehouse. A fábrica designada pode acessar e copiar dados de ou para seu data warehouse usando essa identidade.

Para usar a autenticação de identidade gerenciada, siga estas etapas:

1. **[Provisione um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** para o servidor SQL do Azure no Portal do Azure, caso ainda não tenha feito isso. O administrador do Azure AD pode ser um usuário do AD do Azure ou um grupo do Azure AD. Se você conceder ao grupo com uma função de administrador de identidade gerenciada, ignore as etapas 3 e 4. O administrador terá acesso total ao banco de dados.

2. **[Criar usuários de banco de dados independente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  para a identidade gerenciada do Data Factory. Conecte-se ao data warehouse de ou para o qual você deseja copiar dados usando ferramentas como SSMS, com uma identidade do Azure AD que tenha pelo menos a permissão ALTER ANY USER. Execute o seguinte T-SQL. 
    
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Conceda as permissões necessárias da identidade gerenciada do Data Factory** como faria normalmente para usuários do SQL e outros. Execute o seguinte código, ou consulte para obter mais opções [aqui](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

5. **Configure um serviço vinculado do Data Warehouse SQL do Azure** no Azure Data Factory.

**Exemplo:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do Azure SQL Data Warehouse.

Para copiar dados de ou para o SQL Data Warehouse do Azure, defina a propriedade **tipo** do conjunto de dados para  **AzureSqlDWTable**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **tipo** do conjunto de dados deve ser definida como  **AzureSqlDWTable**. | Sim |
| tableName | O nome da tabela ou exibição na instância do Azure SQL Data Warehouse à qual o serviço vinculado se refere. | Não para fonte, Sim para o coletor |

#### <a name="dataset-properties-example"></a>Exemplo de propriedades do conjunto de dados

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela origem e pelo coletor do Azure SQL Data Warehouse.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Data warehouse do SQL do Azure como a origem

Para copiar dados do SQL Data Warehouse do Azure, defina a propriedade **tipo** na origem da Atividade de Cópia para  **SqlDWSource**. As seguintes propriedades são suportadas na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **tipo** da origem da Atividade de Cópia deve ser configurada para **SqlDWSource**. | Sim |
| sqlReaderQuery | Utiliza a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. | Não  |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. | Não  |
| storedProcedureParameters | Parâmetros para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. | Não  |

### <a name="points-to-note"></a>Pontos a serem observados

- Se o **sqlReaderQuery** for especificado para o **SqlSource**, a Atividade de Cópia executará essa consulta em relação à origem do Azure SQL Data Warehouse para obter os dados. Ou você pode especificar um procedimento armazenado. Especifique**sqlReaderStoredProcedureName** e **storedProcedureParameters** se o procedimento armazenado receber parâmetros.
- Se você não especificar **sqlReaderQuery** ou  **sqlReaderStoredProcedureName**, as colunas definidas na seção **structure** do conjunto de dados JSON serão usadas para construir uma consulta. `select column1, column2 from mytable` é executado no Azure SQL Data Warehouse. Se a definição do conjunto de dados não tiver a **estrutura**, todas as colunas serão selecionadas da tabela.

#### <a name="sql-query-example"></a>Exemplo de consulta SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

### <a name="azure-sql-data-warehouse-as-sink"></a> SQL Data Warehouse do Azure como coletor

Para copiar dados para o SQL Data Warehouse do Azure, defina o tipo de coletor em Atividade de Cópia para **SqlDWSink**. As seguintes propriedades são suportadas na seção Copy Activity **sink**:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **tipo** do coletor de Atividade de Cópia deve ser definida como **SqlDWSink**. | Sim |
| allowPolyBase | Indica se deve usar o PolyBase, quando aplicável, em vez do mecanismo BULKINSERT. <br/><br/> Recomendamos que você carregue dados no SQL Data Warehouse usando o PolyBase. Consulte o [Use PolyBase para carregar dados na seção do Azure SQL Data Warehouse ](#use-polybase-to-load-data-into-azure-sql-data-warehouse)para restrições e detalhes.<br/><br/>Os valores permitidos são **True** e **False** (padrão).  | Não  |
| polyBaseSettings | Um grupo de propriedades que pode ser especificado quando a propriedade **allowPolybase** está definida como **true**. | Não  |
| rejectValue | Especifica o número ou o percentual de linhas que podem ser rejeitadas antes de a consulta falhar.<br/><br/>Saiba mais sobre as opções de rejeição do PolyBase na seção Argumentos de [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Os valores permitidos são 0 (padrão), 1, 2 etc. |Não  |
| rejectType | Especifica se a opção **rejectValue** é um valor literal ou uma porcentagem.<br/><br/>Os valores permitidos são **Valor** (padrão) e **Porcentagem**. | Não  |
| rejectSampleValue | Determina o número de linhas a serem recuperadas antes que o PolyBase recalcule a porcentagem de linhas rejeitadas.<br/><br/>Os valores permitidos são 1, 2 etc. | Sim, se o **rejectType** for  **porcentagem**. |
| useTypeDefault | Especifica como tratar valores ausentes nos arquivos de texto delimitados quando PolyBase recupera dados do arquivo de texto.<br/><br/>Saiba mais sobre essa propriedade na seção Argumentos em [CRIAR FORMATO DE ARQUIVO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Os valores permitidos são **True** e **False** (padrão). | Não  |
| writeBatchSize | Insere dados na tabela SQL quando o tamanho do buffer atinge **writeBatchSize** . Aplica-se apenas quando o PolyBase não é usado.<br/><br/>O valor permitido é **inteiro** (número de linhas). |  Não. O padrão é 10000. |
| writeBatchTimeout | Tempo de espera para a operação de inserção em lote ser concluída antes de expirar. Aplica-se apenas quando o PolyBase não é usado.<br/><br/>O valor permitido é **timespan**. Exemplo: “00:30:00” (30 minutos). | Não  |
| preCopyScript | Especifique uma consulta SQL para que a Atividade de Cópia seja executada antes de gravar dados no Azure SQL Data Warehouse em cada execução. Use essa propriedade para limpar os dados pré-carregados. | Não  |

#### <a name="sql-data-warehouse-sink-example"></a>Exemplo de coletor do SQL Data Warehouse

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

Saiba mais sobre como usar o PolyBase para carregar com eficiência o SQL Data Warehouse na próxima seção.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Use PolyBase to load data into Azure SQL Data Warehouse

O uso do [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) é uma maneira eficiente de carregar uma grande quantidade de dados no Azure SQL Data Warehouse com alto throughput. Você verá um grande ganho na taxa de transferência usando PolyBase em vez do mecanismo BULKINSERT padrão. Veja [Referência de desempenho](copy-activity-performance.md#performance-reference) para uma comparação detalhada. Para obter um passo a passo com um caso de uso, consulte [Carregar 1 TB no Data Warehouse do SQL do Azure](https://docs.microsoft.com/azure/data-factory/v1/data-factory-load-sql-data-warehouse).

* Se os dados de origem estiverem no armazenamento do Azure Blob ou no Azure Data Lake Store e o formato for compatível com o PolyBase, copie diretamente para o SQL Data Warehouse do Azure usando o PolyBase. Para detalhes, veja **[ Cópia direta usando PolyBase ](#direct-copy-by-using-polybase)**.
* Se o armazenamento e o formato de dados de origem não forem originalmente suportados pelo PolyBase, use a **[cópia Staged usando o recurso PolyBase ](#staged-copy-by-using-polybase)**. O recurso de cópia preparada também oferece melhor rendimento. Ele converte automaticamente os dados em formato compatível com PolyBase. E armazena os dados no armazenamento do Azure Blob. E armazena os dados no armazenamento do Azure Blob.

### <a name="direct-copy-by-using-polybase"></a>Cópia direta usando PolyBase

O SQL Data Warehouse PolyBase oferece suporte diretamente ao Azure Blob e ao Azure Data Lake Store. Ele usa o principal de serviço como uma fonte e possui requisitos específicos de formato de arquivo. Se os dados de origem atenderem aos critérios descritos nesta seção, use o PolyBase para copiar diretamente do data store de origem para o SQL Data Warehouse do Azure. Caso contrário, use [cópia Staged usando PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Para copiar dados de maneira eficiente do Data Lake Store para o SQL Data Warehouse, saiba mais em [O Azure Data Factory torna ainda mais fácil e conveniente descobrir percepções dos dados ao usar o Data Lake Store com o SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se os requisitos não forem atendidos, o Azure Data Factory verificará as configurações e retornará automaticamente ao mecanismo BULKINSERT para a movimentação de dados.

1. O **serviço vinculado de origem** o tipo é o armazenamento de BLOBs do Azure (**AzureBLobStorage**/**AzureStorage**) com **autenticação de chave de conta**  ou o Azure Data Lake armazenamento Gen1 (**AzureDataLakeStore**) com **autenticação de entidade de serviço**.
2. O **tipo de conjunto de dados de entrada** é **AzureBlob** ou  **AzureDataLakeStoreFile**. O tipo de formato em `type`propriedades é **OrcFormat**, **ParquetFormat** ou **TextFormat**, com as seguintes configurações:

   1. `fileName` não contém o filtro curinga.
   2. `rowDelimiter` deve ser **\n**.
   3. `nullValue`é definido como **cadeia de caracteres vazia** ("") ou como padrão, e `treatEmptyAsNull` é deixado como padrão ou definido como true.
   4. `encodingName`está definido como **utf-8**, que é o valor padrão.
   5. `escapeChar`, `quoteChar` e `skipLineCount` não são especificadas. O suporte do PolyBase ignorar a linha de cabeçalho que pode ser configurada como `firstRowAsHeader` no ADF.
   6. `compression` pode ser **sem compactação**, **GZip** ou **Deflate**.

      ```json
      "typeProperties": {
        "folderPath": "<blobpath>",
        "format": {
            "type": "TextFormat",
            "columnDelimiter": "<any delimiter>",
            "rowDelimiter": "\n",
            "nullValue": "",
            "encodingName": "utf-8",
            "firstRowAsHeader": <any>
        },
        "compression": {
            "type": "GZip",
            "level": "Optimal"
        }
      },
      ```

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Cópia organizada usando PolyBase

Quando os dados de origem não atendem aos critérios da seção anterior, ative a cópia de dados por meio de uma instância intermediária de armazenamento do Blur de armazenamento temporário do Azure. Não pode ser o Armazenamento Premium do Azure. Nesse caso, o Azure Data Factory executa automaticamente as transformações nos dados para atender aos requisitos de formato de dados do PolyBase. Em seguida, ele usa o PolyBase para carregar os dados no SQL Data Warehouse. Finalmente, ele limpa seus dados temporários do armazenamento de blobs. Consulte [Cópia preparada](copy-activity-performance.md#staged-copy) para obter detalhes sobre a cópia de dados por meio de uma instância de armazenamento de Blob do Azure de preparação.

Para usar esse recurso, crie um [ serviço vinculado do Armazenamento do Azure ](connector-azure-blob-storage.md#linked-service-properties) que se refira à conta de armazenamento do Azure com o armazenamento de blob provisório. Em seguida, especifique as propriedades `enableStaging` e `stagingSettings` para a atividade de cópia, conforme mostrado no código a seguir:

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-for-using-polybase"></a>Práticas recomendadas para usar o PolyBase

As seções a seguir fornecem práticas recomendadas além daquelas mencionadas em [Práticas recomendadas para o SQL Data Warehouse do Azure](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Permissão de banco de dados obrigatória

Para usar o PolyBase, o usuário que carrega dados no SQL Data Warehouse deve ter a [permissão "CONTROLE"](https://msdn.microsoft.com/library/ms191291.aspx) no banco de dados de destino. Uma maneira de conseguir isso é adicionar o usuário como membro da função **db_owner**. Aprenda como fazer isso na [visão geral do SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Limites de tamanho de linha e tipo de dados

As cargas do PolyBase são limitadas a linhas menores que 1 MB. Eles não podem carregar para VARCHR (MAX), NVARCHAR (MAX) ou VARBINARY (MAX). Para obter mais informações, consulte [Limites de capacidade do serviço SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Quando os dados de origem tiverem linhas maiores que 1 MB, talvez você queira dividir verticalmente as tabelas de origem em várias pequenas. Certifique-se de que o maior tamanho de cada linha não exceda o limite. As tabelas menores podem ser carregadas por meio de PolyBase e mescladas no Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do SQL Data Warehouse

Para obter a melhor taxa de transferência possível, atribua uma classe de recurso maior para o usuário que carrega dados em SQL Data Warehouse por meio do PolyBase.

### <a name="tablename-in-azure-sql-data-warehouse"></a>**tableName** no Azure SQL Data Warehouse

A tabela a seguir fornece exemplos de como especificar o **tableName** propriedade no conjunto de dados JSON. Ele mostra as várias combinações de nomes de esquema e tabela.

| Esquema do BD | Nome da tabela | **tableName** propriedade JSON |
| --- | --- | --- |
| dbo | MyTable | MyTable ou dbo.MyTable ou [dbo].[MyTable] |
| dbo1 | MyTable | dbo1.MyTable ou [dbo1].[MyTable] |
| dbo | My.Table | [My.Table] ou [dbo].[My.Table] |
| dbo1 | My.Table | [dbo1]. [My.Table] |

Se você vir o erro a seguir, o problema pode ser o valor especificado para o **tableName** propriedade. Consulte a tabela anterior para a maneira correta especificar valores para o **tableName** propriedade JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Colunas com valores padrão

No momento, o recurso PolyBase na fábrica de dados aceita apenas o mesmo número de colunas da tabela de destino. Um exemplo é uma tabela com quatro colunas em que uma delas é definida com um valor padrão. Os dados de entrada ainda precisam ter quatro colunas. Um conjunto de dados de entrada de três colunas produz um erro semelhante à seguinte mensagem:

```
All columns of the table must be specified in the INSERT BULK statement.
```

O valor NULL é uma forma especial do valor padrão. Se a coluna permite valor nulo, os dados de entrada no blob para essa coluna podem estar vazios. Mas não pode estar ausente do conjunto de dados de entrada. PolyBase insere NULL para valores ausentes no Azure SQL Data Warehouse.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo de dados do SQL Data Warehouse do Azure

Quando você copia dados de ou para o Azure SQL Data Warehouse, os seguintes mapeamentos são usados de tipos de dados do Azure SQL Data Warehouse para tipos de dados intermediários do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber como a atividade de cópia mapeia o tipo de esquema e os dados de origem para o coletor.

| Tipo de dados do SQL Data Warehouse do Azure | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| bigint | Int64 |
| binary | Byte[] |
| bit | Boolean |
| char | String, Char[] |
| date | Datetime |
| Datetime | Datetime |
| datetime2 | Datetime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| FILESTREAM attribute (varbinary(max)) | Byte[] |
| Float | Double |
| image | Byte[] |
| int | Int32 |
| money | Decimal |
| nchar | String, Char[] |
| ntext | String, Char[] |
| numeric | Decimal |
| nvarchar | String, Char[] |
| real | Single |
| rowversion | Byte[] |
| smalldatetime | Datetime |
| smallint | Int16 |
| smallmoney | Decimal |
| sql_variant | Object |
| text | String, Char[] |
| time | TimeSpan |
|  timestamp | Byte[] |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte[] |
| varchar | String, Char[] |
| Xml | Xml |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de repositórios de dados com suporte como fontes e repositórios por Atividade de Cópia no Azure Data Factory, consulte [ repositórios de dados e formatos suportados ](copy-activity-overview.md##supported-data-stores-and-formats).
