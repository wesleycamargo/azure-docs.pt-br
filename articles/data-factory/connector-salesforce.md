---
title: Copiar dados de e para Salesforce usando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados do Salesforce para armazenamentos de dados de coletor com suporte ou de armazenamentos de dados de fonte com suporte para Salesforce usando uma atividade de cópia em um pipeline do Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 4b2561aa338707567b44237e668e9d6d1a01bfea
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Copiar dados de e para Salesforce usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – Disponível de forma geral](v1/data-factory-salesforce-connector.md)
> * [Versão 2 – Versão prévia](connector-salesforce.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Salesforce. Ele amplia o artigo [Visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você usar a versão 1 do Data Factory, que está disponível de forma geral, consulte [Conector Salesforce na versão 1](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do Salesforce para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer repositório de dados de fonte com suporte para um banco de dados Salesforce. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da Atividade de Cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector Salesforce dá suporte à:

- Edições de Desenvolvedor, Professional, Enterprise ou Ilimitada do Salesforce.
- Copiar dados de e para a produção, da área restrita e do domínio personalizado do Salesforce.

## <a name="prerequisites"></a>pré-requisitos

A permissão de API deve estar habilitada no Salesforce. Para mais informações, consulte [Habilitar o acesso à API no Salesforce por conjunto de permissões](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limites da solicitação Salesforce

O Salesforce tem limites para o total de solicitações de API e as solicitações simultâneas de API. Observe os seguintes pontos:

- Se o número de solicitações simultâneas exceder o limite, a limitação será atingida e você verá falhas aleatórias.
- Se o número total de solicitações exceder o limite, a conta do Salesforce será bloqueada por 24 horas.

Você também pode receber a mensagem de erro "REQUEST_LIMIT_EXCEEDED" em ambos os cenários. Para obter mais informações, veja a seção "Limites de Solicitações da API" na seção [Salesforce Developer Limits](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Limites do Desenvolvedor Salesforce).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Salesforce.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Salesforce.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo |A propriedade type deve ser definida para **Salesforce**. |sim |
| environmentUrl | Especifique a URL da instância do Salesforce. <br> – O padrão é `"https://login.salesforce.com"`. <br> – Para copiar dados da área restrita, especifique `"https://test.salesforce.com"`. <br> – Para copiar dados do domínio personalizado, especifique, por exemplo, `"https://[domain].my.salesforce.com"`. |Não  |
| Nome de Usuário |Especifique um nome de usuário para a conta de usuário. |sim |
| Senha |Especifique um senha para a conta de usuário.<br/><br/>Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |sim |
| securityToken |Especifique um token de segurança para a conta de usuário. Para obter instruções sobre como redefinir e obter o token de segurança, consulte [Obter token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Para saber mais sobre os tokens de segurança em geral, veja [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) (Segurança e a API).<br/><br/>Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não para a fonte, Sim para o coletor se o serviço vinculado à fonte não possuir integration runtime |

>[!IMPORTANT]
>Ao copiar dados para o Salesforce, o Azure Integration Runtime padrão não poderá ser usado para executar a cópia. Em outras palavras, se seu serviço vinculado à fonte não tem um integration runtime especificado, explicitamente [crie um Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) com um local perto de sua instância do Salesforce. Associe o serviço vinculado ao Salesforce como no exemplo a seguir.

**Exemplo: Credenciais de armazenamento no Data Factory**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: Credenciais de armazenamento no Key Vault**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Salesforce.

Para copiar dados do e para o Salesforce, defina a propriedade tipo do conjunto de dados como **SalesforceObject**. Há suporte para as seguintes propriedades.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade tipo deve ser definida para **SalesforceObject**.  | sim |
| objectApiName | O nome do objeto de Salesforce para recuperar dados. | Não para fonte, Sim para o coletor |

> [!IMPORTANT]
> A parte "__c" do **Nome da API** é necessária para qualquer objeto personalizado.

![Nome da API de conexão do Salesforce ao Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exemplo:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>Para retrocompatibilidade: ao copiar dados de Salesforce, se você usar o conjunto de dados de tipo "RelationalTable" anterior continuará funcionando, enquanto você vê uma sugestão para alternar para o novo tipo de "SalesforceObject".

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do conjunto de dados deve ser definida como **RelationalTable**. | sim |
| tableName | Nome da tabela no Salesforce. | Não (se "query" na fonte da atividade for especificada) |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e coletor do Salesforce.

### <a name="salesforce-as-a-source-type"></a>Salesforce como um tipo de fonte

Para copiar dados do Salesforce, defina o tipo de origem na atividade de cópia como **SalesforceSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade tipo da fonte da atividade de cópia deve ser definida como: **SalesforceSource**. | sim |
| query |Utiliza a consulta personalizada para ler os dados. Você pode usar uma consulta SQL-92 ou uma consulta [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Um exemplo é `select * from MyTable__c`. | Não (se "tableName" no conjunto de dados for especificado) |

> [!IMPORTANT]
> A parte "__c" do **Nome da API** é necessária para qualquer objeto personalizado.

![Lista Nome da API de conexão do Salesforce ao Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Para retrocompatibilidade: ao copiar dados de Salesforce, se você usar a cópia de tipo "RelationalSource" anterior, a fonte continuará funcionando, enquanto você vê uma sugestão para alternar para o novo tipo de "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>Salesforce como um tipo de coletor

Para copiar dados do Salesforce, defina o tipo de coletor na atividade de cópia como **SalesforceSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do coletor da atividade de cópia deve ser definida como **SalesforceSink**. | sim |
| writeBehavior | O comportamento da operação de gravação.<br/>Valores permitidos são **Insert** e **Upsert**. | Não (o padrão é Insert) |
| externalIdFieldName | O nome do campo de ID externo para a operação upsert. O campo especificado deve ser definido como "Campo de ID externo" no objeto de Salesforce. Ele não pode ter valores nulos nos dados de entrada correspondentes. | Sim para "Upsert" |
| writeBatchSize | A contagem de linhas de dados gravados no Salesforce em cada lote. | Não (o padrão é 5.000) |
| ignoreNullValues | Indica se deve ignorar valores NULL de dados de entrada durante a operação de gravação.<br/>Os valores permitidos são **True** e **False**.<br>- **True**: Deixa os dados no objeto de destino inalterados quando você faz uma operação upsert ou atualização. Insira um valor padrão definido quando você faz uma operação insert.<br/>- **False**: atualiza os dados no objeto de destino como NULL quando você faz uma operação upsert ou atualização. Insira um valor NULL quando você faz uma operação insert. | Não (padrão é falso) |

**Exemplo: coletor Salesforce em uma atividade de cópia**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Dicas de consulta

### <a name="retrieve-data-from-a-salesforce-report"></a>Recupere dados do relatório do Salesforce

Você pode recuperar dados de relatórios do Salesforce especificando uma consulta como `{call "<report name>"}`. Um exemplo é `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Recupere registros excluídos da lixeira do Salesforce

Para consultar os registros excluídos pelo software da lixeira do Salesforce, você pode especificar **"IsDeleted = 1"** em sua consulta. Por exemplo: 

* Para consultar apenas os registros excluídos, especifique "select * from MyTable__c **where IsDeleted= 1**."
* Para consultar todos os registros, incluindo existentes e excluídos, especifique "select * from MyTable__c **onde IsDeleted = 0 ou IsDeleted = 1**."

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Recuperar dados usando um onde cláusula na coluna DateTime

Ao especificar a consulta SQL ou SOQL, preste atenção à diferença de formato DateTime. Por exemplo: 

* **Exemplo de SOQL**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Exemplo de SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}"`

## <a name="data-type-mapping-for-salesforce"></a>Mapeamento de tipo de dados para Salesforce

Ao copiar dados do Salesforce, os seguintes mapeamentos são usados de tipos de dados do Salesforce para tipos de dados provisórios do Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do Salesforce | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| Numeração automática |Cadeia de caracteres |
| Caixa de seleção |BOOLEAN |
| Moeda |Duplo |
| Data |Datetime |
| Data/hora |Datetime |
| Email |Cadeia de caracteres |
| ID |Cadeia de caracteres |
| Relação de pesquisa |Cadeia de caracteres |
| Lista de seleção múltipla |Cadeia de caracteres |
| Número |Duplo |
| Porcentagem |Duplo |
| Telefone |Cadeia de caracteres |
| Lista de seleção |Cadeia de caracteres |
| Texto |Cadeia de caracteres |
| Área de texto |Cadeia de caracteres |
| Área de texto (longo) |Cadeia de caracteres |
| Área de texto (Rich) |Cadeia de caracteres |
| Texto (criptografado) |Cadeia de caracteres |
| URL |Cadeia de caracteres |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).