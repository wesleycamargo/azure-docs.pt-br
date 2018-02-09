---
title: Copiar dados do Impala utilizando o Azure Data Factory (beta) | Microsoft Docs
description: "Saiba como copiar dados do Impala para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Data Factory."
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
ms.openlocfilehash: 06b60968931d18e7c7219d83801a5433631ed470
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="copy-data-from-impala-by-using-azure-data-factory-beta"></a>Copiar dados do Impala utilizando o Azure Data Factory (beta)

Este artigo descreve como usar atividade de cópia no Azure Data Factory para copiar dados de e para o Impala. Ele amplia o artigo [Visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você usar a versão 1 do Data Factory, que está disponível de forma geral, consulte [Atividade de Cópia na versão 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Esse conector está atualmente em versão beta. Você pode experimentá-lo e oferecer comentários. Não utilize-o em ambientes de produção.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do Impala para qualquer repositório de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

 Data Factory fornece um driver interno para habilitar a conectividade. Portanto, você não precisa instalar manualmente um driver para usar esse conector.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do Impala.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Impala.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como **Impala**. | sim |
| host | O endereço IP ou nome do host do servidor Impala (que é 192.168.222.160).  | sim |
| porta | A porta TCP usada pelo servidor Impala para ouvir conexões de cliente. O valor padrão é 21050.  | Não  |
| authenticationType | O tipo de autenticação a ser usado. <br/>Valores permitidos são: **Anônimo**, **SASLUsername** e **UsernameAndPassword**. | sim |
| Nome de Usuário | O nome de usuário usado para acessar o servidor Impala. O valor padrão é anônimo quando você usa SASLUsername.  | Não  |
| Senha | A senha que corresponde ao nome de usuário quando você usa UsernameAndPassword. Você pode marcar este campo como SecureString para armazená-la com segurança no Data Factory. Você também pode armazenar a senha no Azure Key Vault e permitir o pull de atividade de cópia a partir daí, quando você executar a cópia de dados. Para saber mais, consulte [Armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Não  |
| enableSsl | Especifica se as conexões com o servidor são criptografadas usando SSL. O valor padrão é **false**.  | Não  |
| trustedCertPath | O caminho completo do arquivo .pem que contém certificados de autoridade de certificação confiáveis usados para verificar o servidor ao se conectar via SSL. Essa propriedade pode ser definida somente quando você usa o SSL em Integration Runtime auto-hospedada. O valor padrão é o arquivo de cacerts.pem instalado com o tempo de execução de integração.  | Não  |
| useSystemTrustStore | Especifica se deve usar um certificado de autoridade de certificação do repositório de confiança de sistema ou de um arquivo PEM especificado. O valor padrão é **false**.  | Não  |
| allowHostNameCNMismatch | Especifica se é necessário o nome do certificado SSL emitido pela autoridade de certificação para corresponder ao nome de host do servidor ao se conectar via SSL. O valor padrão é **false**.  | Não  |
| allowSelfSignedServerCert | Especifica se deve permitir os certificados autoassinados do servidor. O valor padrão é **false**.  | Não  |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime auto-hospedado ou o Integration Runtime do Azure (se seu armazenamento de dados estiver publicamente acessível). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

**Exemplo:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Impala.

Para copiar dados do Impala, defina a propriedade type do conjunto de dados como **ApacheImpalaObject**. Não há nenhuma propriedade adicional específica do type nesse tipo de conjunto de dados.

**Exemplo**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pelo tipo de fonte do Impala.

### <a name="impala-as-a-source-type"></a>Impala como um tipo de fonte

Para copiar dados do Impala, defina o tipo de fonte na atividade de cópia como **ImpalaSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como **ImpalaSource**. | sim |
| query | Utiliza a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`. | sim |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
