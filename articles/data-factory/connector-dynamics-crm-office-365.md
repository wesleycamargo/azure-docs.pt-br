---
title: Copiar dados de/para Dynamics CRM e 365 usando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados do Dynamics CRM e 365 para armazenamentos de dados de coletor com suporte (OR) de armazenamentos de dados de fonte com suporte para Dynamics CRm e 365 usando uma atividade de cópia em um pipeline do Azure Data Factory."
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
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: 91de03f3472244341f4cf086bc8a2f56f7d2e487
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-fromto-dynamics-365dynamics-crm-using-azure-data-factory"></a>Copiar dados de/para Dynamics 365/Dynamics CRM usando o Azure Data Factory

Este artigo estrutura como usar a atividade de cópia no Azure Data Factory para copiar dados de e para Dynamics 365/Dynamics CRM. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Atividade de cópia na V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do Dynamics 365/Dynamics CRM para qualquer armazenamento de dados de coletor com suporte ou copiar dados de qualquer armazenamento de dados de origem com suporte para o Dynamics 365/Dynamics CRM. Para obter uma lista de armazenamentos de dados com suporte, que funcionam como fontes/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Este conector do Dynamics é compatível com as versões e os tipos de autenticação do Dynamics abaixo (*IFD é abreviação de Implantação para a Internet*):

| Versões do Dynamics | Tipos de autenticação | Exemplos de serviço vinculado |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM online | Office365 | [Dynamics Online + Autenticação do Office365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 local com IFD <br> Dynamics CRM 2016 local com IFD <br> Dynamics CRM 2015 local com IFD | IFD | [Dynamics local com IFD + Autenticação do IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Para o Dynamics 365 especificamente, os seguintes tipos de aplicativos são compatíveis:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

> [!NOTE]
> Para usar o conector do Dynamics, armazene sua senha no Azure Key Vault e permita que a atividade de cópia faça pull desse local ao realizar a cópia de dados. Veja como configurar na seção [propriedades do serviço vinculado](#linked-service-properties).

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Dynamics.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Dynamics:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 e Dynamics CRM Online

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **Dynamics**. | Sim |
| deploymentType | O tipo de implantação da instância do Dynamics. Deve ser **"Online"** para o Dynamics Online. | Sim |
| organizationName | O nome da organização da instância do Dynamics. | Não, deve especificar quando há mais de uma instância do Dynamics associada ao usuário. |
| authenticationType | O tipo de autenticação para se conectar ao servidor do Dynamics. Especifique **"Office365"** para o Dynamics Online. | Sim |
| Nome de Usuário | Especifica o nome de usuário para se conectar ao Dynamics. | Sim |
| Senha | Especifique a senha da conta de usuário que você especificou para o nome de usuário. Você precisa colocar a senha no Azure Key Vault e configurar a senha como um "AzureKeyVaultSecret". Saiba mais sobre [Armazenar credenciais no Cofre de Chaves](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não para a origem, Sim para o coletor se o serviço vinculado à fonte não possuir IR |

>[!IMPORTANT]
>Ao copiar dados **para o** Dynamics, o Azure Integration Runtime padrão não poderá ser usado para executar a cópia. Em outras palavras, se seu serviço vinculado à fonte não tiver um IR especificado, explicitamente [crie um IR do Azure](create-azure-integration-runtime.md#create-azure-ir) com um local perto de seu Dynamics e associe no serviço vinculado do Dynamics como o exemplo a seguir.

**Exemplo: Dynamics online usando a autenticação do Office365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 e Dynamics CRM local com IFD

*Propriedades adicionais, comparando com o Dynamics online, são "hostName" e "port".*

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **Dynamics**. | Sim |
| deploymentType | O tipo de implantação da instância do Dynamics. Deve ser **"OnPremisesWithIfd"** para o Dynamics local com IFD.| Sim |
| **hostName** | O nome do host do servidor do Dynamics local. | Sim |
| **port** | A porta do servidor do Dynamics local. | Não, o padrão é 443 |
| organizationName | O nome da organização da instância do Dynamics. | Sim |
| authenticationType | O tipo de autenticação para se conectar ao servidor do Dynamics. Especifique **"Ifd"** para o Dynamics local com IFD. | Sim |
| Nome de Usuário | Especifica o nome de usuário para se conectar ao Dynamics. | Sim |
| Senha | Especifique a senha da conta de usuário que você especificou para o nome de usuário. Observe que você precisa colocar a senha no Azure Key Vault e configurar a senha como um "AzureKeyVaultSecret". Saiba mais sobre [Armazenar credenciais no Cofre de Chaves](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não para fonte, Sim para o coletor |

>[!IMPORTANT]
>Para copiar dados em Dynamics, explicitamente [Crie um IR Azure](create-azure-integration-runtime.md#create-azure-ir) com um local perto de seu Dynamics e associe no serviço vinculado como o exemplo a seguir.

**Exemplo: Dynamics local com IFD usando a autenticação de IFD**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Dynamics.

Para copiar dados de/para Dynamics, defina o tipo da propriedade do conjunto de dados como **DynamicsEntity**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do conjunto de dados deve ser definida como: **DynamicsEntity** |Sim |
| entityName | O nome lógico da entidade a ser recuperada. | Não para fonte (se "query" na fonte da atividade for especificada), Sim para coletor |

> [!IMPORTANT]
>- **Quando copiar dados do Dynamics, a seção "estrutura" no conjunto de dados é necessária**, a qual define o tipo de dados e o nome de coluna para os dados do Dynamics nos quais você deseja copiar. Saiba mais em [estrutura do conjunto de dados](concepts-datasets-linked-services.md#dataset-structure) e [Mapeamento de tipo de dados para o Dynamics](#data-type-mapping-for-dynamics).
>- **Ao copiar dados para o Dynamics, a seção "estrutura" é opcional** no conjunto de dados do Dynamics. Quais colunas copiar serão determinadas pelo esquema de fonte de dados. Se a fonte for um arquivo CSV sem cabeçalho, do conjunto de dados de entrada, especifique "structure" com a coluna Nome e tipo de dados que mapeia para os campos no arquivo CSV de uma em uma na ordem.

**Exemplo:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typePoperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e coletor do Dynamics.

### <a name="dynamics-as-source"></a>Dynamics como fonte

Para copiar dados do Dynamics, defina o tipo de fonte na atividade de cópia como **DynamicsSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A o tipo da propriedade da fonte da atividade de cópia deve ser definida como: **DynamicsSource**  | Sim |
| query  | FetchXML é uma linguagem de consulta proprietária que é usada no Microsoft Dynamics (online e local). Veja o exemplo abaixo e saiba mais em [Criar consultas com FetchXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx). | Não (se "entityName" no conjunto de dados for especificada)  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Exemplo de consulta FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-sink"></a>Dynamics como coletor

Para copiar dados do Dynamics, defina o tipo de coletor na atividade de cópia como **DynamicsSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A tipo de propriedade do coletor da atividade de cópia deve ser definida como: **DynamicsSink**  | Sim |
| writeBehavior | O comportamento da operação de gravação.<br/>O valor permitido é: **"Upsert"**. | Sim |
| writeBatchSize | A contagem de linhas de dados gravados no Dynamics em cada lote. | Não (o padrão é 10) |
| ignoreNullValues | Indica se deve ignorar valores nulos de dados de entrada (exceto campos de chave) durante a operação de gravação.<br/>Os valores permitidos são: **True** e **False**.<br>- true: deixe os dados no objeto de destino inalterados ao fazer a operação upsert/atualização e insira valor definido pelo padrão ao fazer a operação de inserção.<br/>- false: atualize os dados no objeto de destino inalterados para NULL ao fazer a operação upsert/atualização e insira valor definido pelo padrão ao fazer a operação de inserção.  | Não (padrão é falso) |

>[!NOTE]
>O valor padrão da atividade de coletor writeBatchSize e cópia [parallelCopies](copy-activity-performance.md#parallel-copy) para o coletor do Dynamics são ambos os 10, o que significa 100 registros enviados ao Dynamics simultaneamente.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Mapeamento de tipo de dados para Dynamics

Ao copiar dados do Dynamics, os seguintes mapeamentos são usados de tipos de dados do Dynamics para tipos de dados intermediários do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

Configure o tipo de dados do Data Factory correspondente na estrutura do conjunto de dados com base em sua fonte de tipo de dados do Dynamics, usando a tabela de mapeamento abaixo:

| Tipo de dados do Dynamics | Tipo de dados provisório do Data Factory | Tem suporte como origem | Tem suporte como coletor |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | BOOLEAN | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ |  |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Duplo | ✓ | ✓ |
| AttributeType.EntityName | Cadeia de caracteres | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ |  |
| AttributeType.ManagedProperty | BOOLEAN | ✓ |  |
| AttributeType.Memo | Cadeia de caracteres | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | Cadeia de caracteres | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Não há suporte para os tipo de dados do Dynamics AttributeType.CalendarRules e AttributeType.PartyList.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).