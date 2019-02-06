---
title: Copiar dados do SAP Business Warehouse via Open Hub com o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do SAP BW (Business Warehouse) via Open Hub para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: jingwang
ms.openlocfilehash: 74061eb081fcc7c2c84707f2414a2edfbfde3289
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299530"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse via Open Hub com o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados SAP BW (Business Warehouse) via Open Hub. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="sap-bw-open-hub-integration"></a>Integração do SAP BW Open Hub 

O [Serviço do SAP BW Open Hub](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) é uma maneira eficiente de extrair dados do SAP BW. O diagrama a seguir mostra um dos fluxos típicos que os clientes têm em seu sistema SAP, caso em que os dados fluem do SAP ECC -> PSA -> DSO -> Cubo.

O SAP BW OHD (Destino de Open Hub) define o destino ao qual os dados do SAP são retransmitidos. Quaisquer objetos compatíveis com o SAP DTP (Data Transfer Process) podem ser usados como fontes de dados do Open Hub, como DSO, InfoCube, MultiProvider, DataSource etc. Tipo de Destino Open Hub – no qual os dados retransmitidos são armazenados – pode ser composto por tabelas de banco de dados (locais ou remotas) e arquivos simples. Esse conector do SAP BW Open Hub é compatível com copiar dados da tabela local do OHD no BW. Caso você esteja usando outros tipos, poderá conectar-se diretamente ao banco de dados ou sistema de arquivos usando outros conectores.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do SAP Business Warehouse via Open Hub para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SAP Business Warehouse Open Hub dá suporte a:

- SAP Business Warehouse **versão 7.30 ou superior (em uma recente Pilha de Pacote de Suporte SAP lançada após o ano de 2015)**.
- Cópia de dados por meio da tabela de local de destino do Open Hub que pode ser, por baixo, DSO, InfoCube, MultiProvider, DataSource etc.
- À cópia de dados usando a autenticação Básica.
- Conexão ao Servidor de Aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector do SAP Business Warehouse Open Hub, você precisa:

- Configurar um tempo de execução da integração auto-hospedada com versão 3.13 ou superior. Consulte o artigo [Self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Integration Runtime auto-hospedado) para obter detalhes.

- Baixar a versão de **64 bits do [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** do site da SAP e instalá-la no computador de IR auto-hospedado. Ao instalar, na janela de etapas de instalação opcionais, verifique se você selecionou a opção **Instalar Assemblies no GAC** conforme mostra a imagem a seguir. 

    ![Instalar o SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- O usuário do SAP que está sendo usado no conector do Data Factory BW precisa ter as seguintes permissões: 

    - Autorização para RFC e SAP BW. 
    - Permissões para a Atividade de "Executar" do Objeto de Autorização "S_SDSAUTH".

- Criar tipo de Destino do SAP Open Hub como **Tabela de Banco de Dados** com opção "Chave Técnica" marcada.  Também é recomendável deixar a opção Excluir Dados da Tabela desmarcada, embora não seja necessário. Aproveite o DTP (execute ou integre diretamente na cadeia de processo existente) para descarregar dados do objeto de origem (como um cubo) que você escolheu para a tabela de destino do Open Hub.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir apresentam detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas do conector do SAP Business Warehouse Open Hub.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do SAP BW Open Hub (Business Warehouse):

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **SapOpenHub** | SIM |
| Servidor | Nome do servidor no qual reside a instância do SAP BW. | SIM |
| systemNumber | Número de sistema do sistema SAP BW.<br/>Valor permitido: número decimal de dois dígitos representado como uma cadeia de caracteres. | SIM |
| clientId | ID de Cliente do cliente no sistema SAP W.<br/>Valor permitido: número decimal de três dígitos representado como uma cadeia de caracteres. | SIM |
| Linguagem | Idioma que o sistema SAP usa. | Não (o valor padrão é **EN**)|
| userName | Nome do usuário que tem acesso ao servidor SAP. | SIM |
| Senha | Senha do usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | SIM |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |SIM |

**Exemplo:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Para copiar dados de e para o SAP BW Open Hub, defina a propriedade type do conjunto de dados como **SapOpenHubTable**. Há suporte para as seguintes propriedades.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como **SapOpenHubTable**.  | SIM |
| openHubDestinationName | O nome de Destino do Open Hub do qual copiar dados. | SIM |
| excludeLastRequest | Se você deseja excluir os registros da última solicitação. | Não (o padrão é **true**) |
| baseRequestId | A ID da solicitação do carregamento delta. Depois que ele for definido, somente os dados com requestId **maior do que** o valor dessa propriedade serão recuperados.  | Não  |

>[!TIP]
>Se a tabela do Open Hub contém apenas os dados gerados por ID de solicitação única, por exemplo, você sempre realiza carga completa e substitui os dados existentes na tabela ou somente executa o DTP uma vez para teste; lembre-se de desmarcar a opção "excludeLastRequest" para copiar os dados.

**Exemplo:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do SAP BW Open Hub.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub como origem

Para copiar dados do SAP BW Open Hub, defina o tipo de origem na atividade de cópia como **SapOpenHubSource**. Embora não haja nenhuma propriedade de tipo específico adicional necessária na seção de **origem** da atividade de cópia.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapeamento de tipo de dados para SAP BW Open Hub

Ao copiar dados do SAP BW Open Hub, os seguintes mapeamentos são usados de tipos de dados do SAP BW para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo SAP ABAP | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| C (Cadeia de caracteres) | Cadeia de caracteres |
| I (inteiro) | Int32 |
| F (Flutuante) | Double |
| D (Data) | Cadeia de caracteres |
| T (Hora) | Cadeia de caracteres |
| P (BCD Empacotado, Moeda, Decimal, Qtd) | Decimal |
| N (Numc) | Cadeia de caracteres |
| X (Binário e Bruto) | Cadeia de caracteres |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
