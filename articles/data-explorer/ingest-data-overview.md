---
title: Ingestão de dados do Azure Data Explorer
description: Saiba mais sobre as diferentes maneiras de ingerir (carregar) com os dados no Azure Data Explorer
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6c7d4d8d4a16e0679722f9de007870a7ec7554b0
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51635992"
---
# <a name="azure-data-explorer-data-ingestion"></a>Ingestão de dados do Azure Data Explorer

A ingestão de dados é o processo usado para carregar os registros de dados de uma ou mais fontes para criar ou atualizar uma tabela no Azure Data Explorer. Depois de ingeridos, os dados ficam disponíveis para consulta. O diagrama a seguir mostra o fluxo de ponta a ponta para trabalhar no Azure Data Explorer, incluindo a ingestão de dados.

![Fluxo de dados](media/ingest-data-overview/data-flow.png)

O serviço de gerenciamento de dados do Azure Data Explorer, que é responsável pela ingestão de dados, fornece a seguinte funcionalidade:

1. **Pull de dados**: extrai dados de fontes externas (Hubs de Eventos) ou de solicitações de ingestão de leitura de uma Fila do Azure.

1. **Envio em lote**: dados em lote que fluem para o mesmo banco de dados e tabela para otimizar a produtividade da ingestão do lote.

1. **Validação**: validação preliminar e conversão de formato se necessário.

1. **Manipulação de dados**: correspondência de esquema, organização, indexação, codificação e compactação dos dados.

1. **Ponto de persistência no fluxo de ingestão**: gerenciar a carga de ingestão no mecanismo e lidar com novas tentativas após falhas transitórias.

1. **Confirmar a ingestão de dados**: disponibiliza os dados para consulta.

## <a name="ingestion-methods"></a>Métodos de ingestão

O Azure Data Explorer dá suporte a vários métodos de ingestão, cada um com seus próprios cenários de destino, vantagens e desvantagens. O Azure Data Explorer oferece pipelines e conectores para serviços comuns, ingestão programática usando SDKs e acesso direto ao mecanismo para fins de exploração.

### <a name="ingestion-using-pipelines"></a>Ingestão usando pipelines

O Azure Data Explore atualmente dá suporte para pipeline de Hub de Eventos, que pode ser gerenciado usando o assistente de gerenciamento no portal do Azure. Para obter mais informações, confira [Início Rápido: Ingestão de dados do Hub de Eventos no Azure Data Explorer](ingest-data-event-hub.md).

### <a name="ingestion-using-connectors-and-plugins"></a>Ingestão usando conectores e plug-ins
O Azure Data Explorer atualmente dá suporte para plug-in do Logstash. Para obter mais informações, consulte [Plug-in de saída do Logstash para Azure Data Explorer](https://github.com/Azure/logstash-output-kusto/blob/master/README.md).

### <a name="programmatic-ingestion"></a>Ingestão programática

O Azure Data Explorer fornece SDKs que podem ser usados para consulta e ingestão de dados. A ingestão programática é otimizada para redução de custos de ingestão (COGs), minimizando as transações de armazenamento durante e após o processo de ingestão.

**Projetos de software livre e SDKs disponíveis**:

O Kusto oferece um SDK cliente que pode ser usado para ingerir e consultar dados com:

* [SDK do Python](/azure/kusto/api/python/kusto-python-client-library)

* [SDK .NET](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [SDK do Node](/azure/kusto/api/node/kusto-node-client-library)

* [API REST](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Técnicas programáticas de ingestão**:

* Ingestão de dados por meio do serviço de gerenciamento de dados do Azure Data Explorer (ingestão confiável e de alta produtividade):

  * [**Ingestão em lote**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (fornecida pelo SDK): o cliente carrega os dados no Armazenamento de Blobs do Azure (designado pelo serviço de gerenciamento de dados do Azure Data Explorer) e envia uma notificação a uma Fila do Azure. Essa é a técnica recomendada para ingestão de dados de alto volume, confiáveis e baratos.

* Ingestão de dados direta no mecanismo do Azure Data Explorer (mais apropriada para exploração e criação de protótipos):

  * **Ingestão embutida**: o comando de controle (.ingest inline) que contém os dados na banda destina-se a testes ad hoc.

  * **Ingestão de consulta**: o comando de controle (.set, .set-or-append, .set-or-replace) que aponta para os resultados da consulta é usado para a geração de relatórios ou tabelas temporárias pequenas.

  * **Ingestão de armazenamento**: o comando de controle (.ingest into) com os dados armazenados externamente (por exemplo, Armazenamento de Blobs do Azure) permite a ingestão eficiente de dados em massa.

**Latência de métodos diferentes**:

| Método | Latency |
| --- | --- |
| **Ingestão embutida** | Imediata |
| **Ingestão de consulta** | Tempo de consulta + tempo de processamento |
| **Ingestão de armazenamento** | Tempo de download + tempo de processamento |
| **Ingestão na fila** | Tempo de envio em lote + tempo de processamento |
| |

O tempo de processamento depende do tamanho dos dados e geralmente é de poucos segundos. O padrão de tempo do envio em lote é de cinco minutos.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Escolhendo o método de ingestão mais apropriado

Antes de iniciar a ingestão de dados, você deve se perguntar o seguinte.

* Onde meus dados residem? 
* Qual é o formato dos dados e ele pode ser alterado? 
* Quais são os campos obrigatórios a serem consultados? 
* Qual é o volume de dados e a velocidade que se espera? 
* Quantos tipos de evento se esperada (refletidos como o número de tabelas)? 
* Qual a frequência esperada para alterações do esquema de eventos? 
* Quantos nós gerarão os dados? 
* Qual é o sistema operacional de origem? 
* Quais são os requisitos de latência? 
* Um dos pipelines de ingestão gerenciados existentes pode ser usado? 

Para organizações com uma infraestrutura existente com base em um serviço de mensagens, como o Hub de Eventos, o uso de um conector provavelmente é a solução mais apropriada. A ingestão na fila é apropriada para grandes volumes de dados.

## <a name="supported-data-formats"></a>Formatos de dados com suporte

Para todos os métodos de ingestão diferentes da ingestão de consulta, os dados precisam ser formatados em um dos formatos de dados com suporte para que o Azure Data Explorer possa analisá-los.

* CSV, TSV, PSV, SCSV, SOH
* JSON (multilinha, separado por linha), Avro
* ZIP e GZIP 

> [!NOTE]
> Quando os dados estão sendo ingeridos, os tipos de dados são inferidos com base nas colunas da tabela de destino. Se um registro estiver incompleto ou um campo não puder ser analisado como o tipo de dados necessário, as colunas da tabela correspondentes serão preenchidas com valores nulos.

## <a name="ingestion-recommendations-and-limitations"></a>Limitações e recomendações de ingestão
* A política de retenção efetiva de dados ingeridos é derivada da política de retenção do banco de dados. Veja [política de retenção](/azure/kusto/concepts/retentionpolicy) para obter detalhes. A ingestão de dados requer as permissões **Gestor de tabela** ou **Gestor de banco de dados**.
* A ingestão tem suporte para um tamanho máximo de arquivo de 5 GB. A recomendação é ingerir arquivos entre 100 MB e 1GB.

## <a name="schema-mapping"></a>Mapeamento de esquema

O mapeamento de esquema ajuda a associar de forma determinística os campos de dados de origem às colunas da tabela de destino.

* O [Mapeamento CSV](/azure/kusto/management/mappings?branch=master#csv-mapping) (opcional) funciona com todos os formatos baseados em ordinais e pode ser passado como o parâmetro do comando de ingestão ou [pré-criado na tabela](/azure/kusto/management/tables?branch=master#create-ingestion-mapping) e referenciado do parâmetro de comando de ingestão.
* O [mapeamento JSON](/azure/kusto/management/mappings?branch=master#json-mapping) (obrigatório) e [mapeamento Avro](/azure/kusto/management/mappings?branch=master#avro-mapping) (obrigatório) podem ser passados como o parâmetro do comando de ingestão ou [pré-criado na tabela](/azure/kusto/management/tables#create-ingestion-mapping) e referenciado pelo parâmetro do comando de ingestão.

## <a name="next-steps"></a>Próximas etapas

[Início rápido: inserir dados do Hub de Eventos no Azure Data Explorer](ingest-data-event-hub.md)

[Início Rápido: inserir dados usando a biblioteca Python do Azure Data Explorer](python-ingest-data.md)

[Início rápido: ingerir dados usando a biblioteca de nós do Azure Data Explorer](node-ingest-data.md)

