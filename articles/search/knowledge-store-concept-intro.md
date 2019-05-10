---
title: Introdução e visão geral do Repositório de Dados de Conhecimento – Azure Search
description: Envie documentos enriquecidos ao armazenamento do Azure onde você pode exibir, reformatar e consumir documentos enriquecidos no Azure Search e em outros aplicativos.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: overview
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 3000016de934aaa3faab96821f9747ea4b571ef7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65030084"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>O que é o Repositório de Dados de Conhecimento no Azure Search?

O Repositório de Dados de Conhecimento é um recurso opcional do Azure Search, atualmente em versão prévia pública, que salva documentos enriquecidos e metadados criados por um pipeline de indexação baseada em IA [(pesquisa cognitiva)](cognitive-search-concept-intro.md). O Repositório de Dados de Conhecimento tem suporte da conta de armazenamento do Azure que você configura como parte do pipeline. Quando habilitado, o serviço de pesquisa usa essa conta de armazenamento para armazenar em cache uma representação de cada documento enriquecido. 

Se você já usou a pesquisa cognitiva, sabe que as habilidades poderão ser usadas para mover um documento por uma sequência de enriquecimentos. O resultado pode ser um índice do Azure Search ou projeções (uma novidade nesta versão prévia) em um repositório de dados de conhecimento.

As projeções são o mecanismo para estruturar dados para consumo em um aplicativo downstream. Você pode usar o [Gerenciador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) criado para o armazenamento do Azure ou qualquer aplicativo que se conecte ao armazenamento do Azure, o que abre novas possibilidades para consumir documentos enriquecidos. Alguns exemplos são pipelines de ciência de dados e análises personalizadas.

![Repositório de dados de conhecimento no diagrama do pipeline](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Repositório de dados de conhecimento no diagrama do pipeline")

Para usar o Repositório de Dados de Conhecimento, adicione um elemento `knowledgeStore` a um conjunto de qualificações que defina operações por etapas em um pipeline de indexação. Durante a execução, o Azure Search cria um espaço em sua conta de armazenamento do Azure e preenche-o com definições e conteúdo criados pelo pipeline.

## <a name="benefits-of-knowledge-store"></a>Vantagens do Repositório de Dados de Conhecimento

Um repositório de dados de conhecimento fornece estrutura, contexto e conteúdo real, compilados de arquivos de dados não estruturados e semiestruturados como blobs, arquivos de imagem que passaram por análise ou mesmo dados estruturados que são reformatados. Em um [passo a passo](knowledge-store-howto.md) escrito para esta versão prévia, você pode ver em primeira mão como um documento JSON denso é particionado em subestruturas, reconstituído em novas estruturas e disponibilizado para processos downstream como aprendizado de máquina e cargas de trabalho de ciência de dados.

Embora seja útil ver o que um pipeline de indexação baseada em IA pode produzir, o poder real do Repositório de Dados de Conhecimento é a capacidade de reformatar dados. Você pode começar com um conjunto de qualificações básicas e depois iterar sobre ele para adicionar níveis crescentes de estrutura, que podem ser combinados em novas estruturas e consumidos em outros aplicativos além do Azure Search.

Se enumeradas, as vantagens do Repositório de Dados de Conhecimento incluem o seguinte:

+ Consumir documentos enriquecidos em [ferramentas de relatórios e análise](#tools-and-apps) que não sejam de pesquisa. O Power BI com Power Query é uma opção interessante, mas qualquer ferramenta ou aplicativo que consiga se conectar ao armazenamento do Azure poderá efetuar pull de um repositório de dados de conhecimento criado.

+ Refinar um pipeline de indexação por IA durante as etapas de depuração e definições de conjuntos de qualificações. Um repositório de dados de conhecimento mostra o produto de uma definição de conjuntos de qualificações em um pipeline de indexação por IA. Você pode usar esses resultados para criar um conjunto de qualificações enriquecido já que pode ver exatamente como são os enriquecimentos. Você pode usar o [Gerenciador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) no armazenamento do Azure para exibir o conteúdo de um repositório de dados de conhecimento.

+ Reformatar os dados. A reformatação é codificada em conjuntos de qualificações. No entanto, a questão é que um conjunto de qualificações já pode oferecer essa capacidade. A [habilidade do Shaper](cognitive-search-skill-shaper.md) no Azure Search foi estendida para acomodar essa tarefa. A reformatação permite definir uma projeção que se alinha com o uso pretendido dos dados enquanto preserva as relações.

> [!Note]
> Não está familiarizado com a indexação baseada em IA usando os Serviços Cognitivos? O Azure Search integra-se aos recursos de Visão de Computação e Idioma nos Serviços Cognitivos para extrair e enriquecer os dados de origem usando OCR (Reconhecimento Óptico de Caracteres) em arquivos de imagem, reconhecimento de entidade e extração de frases-chave de arquivos de texto e muito mais. Saiba mais em [O que é a pesquisa cognitiva?](cognitive-search-concept-intro.md).

## <a name="create-a-knowledge-store"></a>Criar um repositório de dados de conhecimento

Um repositório de dados de conhecimento é parte da definição de um conjunto de qualificações. Nesta versão prévia, a criação exige a API REST usando o `api-version=2019-05-06-Preview` ou o assistente **Importar Dados** no portal.

O JSON a seguir especifica um `knowledgeStore` que faz parte de um conjunto de qualificações chamado por um indexador (não mostrado). A especificação de projeções no `knowledgeStore` determina se tabelas ou objetos são criados no armazenamento do Azure.

Se você já estiver familiarizado com a indexação baseada em IA, a definição do conjunto de qualificações determina a criação, a organização e a substância de cada documento enriquecido.

```json
{
  "name": "my-new-skillset",
  "description": 
  "Example showing knowledgeStore placement, supported in api-version=2019-05-06-Preview. You need at least one skill, most likely a Shaper skill if you are modulating data structures.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }    
    ]     
    } 
}
```

## <a name="components-backing-a-knowledge-store"></a>Componentes que fornecem backup a um repositório de dados de conhecimento

Para criar um repositório de dados de conhecimento, você precisa dos seguintes serviços e artefatos.

### <a name="1---source-data"></a>1. Dados de origem

Os dados ou documentos que você deseja enriquecer devem existir em uma fonte de dados do Azure compatível com os indexadores do Azure Search: 

* [SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)

O [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md) pode ser usado para dados de saída em um repositório de dados de conhecimento. No entanto, não pode ser usado como um recurso para dados de entrada em um pipeline de indexação baseada em IA.

### <a name="2---azure-search-service"></a>2. Serviço do Azure Search

Você também precisa de uma API REST e o serviço do Azure Search para criar e configurar objetos usados para o enriquecimento de dados. A API REST para a criação de um repositório de dados de conhecimento é `api-version=2019-05-06-Preview`.

O Azure Search fornece o recurso de indexador, e os indexadores são usados para conduzir todo o processo de ponta a ponta. O resultado são documentos enriquecidos e persistentes no armazenamento do Azure. Os indexadores usam uma fonte de dados, um índice e um conjunto de qualificações, todos necessários para criar e preencher um repositório de dados de conhecimento.

| Objeto | API REST | DESCRIÇÃO |
|--------|----------|-------------|
| fonte de dados | [Criar Fonte de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Um recurso que identifica uma fonte de dados externa do Azure, fornecendo os dados de origem usados para criar documentos enriquecidos.  |
| conjunto de qualificações | [Criar Conjunto de Qualificações (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que coordena o uso de [habilidades internas](cognitive-search-predefined-skills.md) e de [habilidades cognitivas personalizadas](cognitive-search-custom-skill-interface.md) em um pipeline de enriquecimento durante a indexação. |
| índice | [Criar o índice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Um esquema que expressa um índice do Azure Search. Os campos no índice são mapeados para os campos na fonte de dados ou para os campos fabricados durante a fase de enriquecimento (por exemplo, um campo para os nomes de organização criados pelo reconhecimento de entidade). |
| indexador | [Criar Indexador (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que define os componentes usados durante a indexação: incluindo uma fonte de dados, um conjunto de habilidades, associações de campo de origem e estruturas de dados intermediárias para o índice de destino e o próprio índice. Executar o indexador é o gatilho para a ingestão de dados e o enriquecimento. A saída é um índice de pesquisa com base no esquema de índice, preenchido com os dados de origem, enriquecidos por conjuntos de qualificações.  |

### <a name="3---cognitive-services"></a>3. Serviços Cognitivos

Os enriquecimentos especificados em um conjunto de qualificações são baseados nos recursos de Pesquisa Visual Computacional e Idioma nos Serviços Cognitivos. A funcionalidade dos Serviços Cognitivos é utilizada durante a indexação por meio de seu conjunto de qualificações. Um conjunto de qualificações é uma composição de habilidades, e elas estão vinculadas a recursos específicos de Pesquisa Visual Computacional e Idioma. Para integrar os Serviços Cognitivos, [anexe um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) a um conjunto de qualificações.

### <a name="4---storage-account"></a>4. Conta de armazenamento

Em sua conta de Armazenamento do Azure, o Azure Search cria um contêiner ou tabelas de Blob, dependendo da forma como você configura um conjunto de qualificações. O conjunto já estará definido se seus dados tiverem origem no armazenamento de Blobs ou Tabela do Azure. Caso contrário, será preciso criar uma conta de armazenamento do Azure. As tabelas e objetos no armazenamento do Azure contêm os documentos enriquecidos criados pelo pipeline de indexação baseada em IA.

A conta de armazenamento é especificada no conjunto de qualificações. Em `api-version=2019-05-06-Preview`, a definição de um conjunto de qualificações inclui uma definição de repositório de dados de conhecimento para que você possa fornecer informações de conta.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5. Acesso e consumo

Após os enriquecimentos estarem no armazenamento, qualquer ferramenta ou tecnologia que se conecte ao armazenamento de Blobs ou de Tabelas do Azure poderá ser usada para explorar, analisar ou consumir o conteúdo. A lista a seguir é um começo:

+ [Gerenciador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) para exibir o conteúdo e a estrutura do documento enriquecido. Considere isso como sua ferramenta de linha de base para visualizar o conteúdo do repositório de dados de conhecimento.

+ [Power BI com o Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) para consultas em linguagem natural ou use ferramentas de análise e relatórios se tiver dados numéricos.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) para manipular mais.

+ Índice do Azure Search para a pesquisa de texto completo sobre o conteúdo que você indexou usando a [pesquisa cognitiva](cognitive-search-concept-intro.md).

## <a name="document-persistence"></a>Persistência de documento

Na conta de armazenamento, os enriquecimentos podem ser expressos como tabelas no armazenamento de Tabelas do Azure ou como objetos no armazenamento de Blobs do Azure. Os enriquecimentos, uma vez armazenados, podem ser usados como uma fonte para carregar dados em outros bancos de dados e ferramentas.

+ O armazenamento de Tabelas é útil quando você deseja uma representação com reconhecimento de esquema dos dados em formato tabular. Se você quiser reformatar ou recombinar elementos de novas maneiras, o armazenamento de tabela fornecerá a granularidade necessária.

+ O armazenamento de Blobs cria uma representação JSON completa de cada documento. Você pode usar as duas opções de armazenamento em um conjunto de qualificações para obter uma gama completa de expressões.

+ O Azure Search persiste o conteúdo em um índice. Se o cenário não estiver relacionado à pesquisa, por exemplo, se seu objetivo for a análise em outra ferramenta, exclua o índice criado pelo pipeline. No entanto, você também pode manter o índice e usar uma ferramenta interna como o [Gerenciador de Pesquisa](search-explorer.md) como uma terceira mídia (após o Gerenciador de Armazenamento e seu aplicativo de análise) para interagir com seu conteúdo.

Juntamente com o conteúdo do documento, os documentos enriquecidos incluem os metadados da versão do conjunto de qualificações que produziram os enriquecimentos.  

## <a name="inside-a-knowledge-store"></a>Dentro de um repositório de dados de conhecimento

O repositório de dados de conhecimento é formado por projeções e um cache de anotação. O *cache* é usado internamente pelo serviço para armazenar em cache os resultados das habilidades e rastrear as alterações. Um *projeção* define o esquema e a estrutura de enriquecimentos que correspondem ao uso pretendido. Há um cache por repositório de dados de armazenamento de conhecimento, mas várias projeções. 

O cache é sempre um contêiner de blobs, mas as projeções podem ser articuladas como tabelas ou objetos:

+ Como objeto, a projeção mapeia para o armazenamento de Blobs onde a projeção é salva em um contêiner no qual estão os objetos ou representações hierárquicas no JSON para cenários como um pipeline de ciência de dados.

+ Como tabela, a projeção mapeia para o armazenamento da Tabelas. Uma representação tabular preserva os relacionamentos para cenários como análise de dados ou exportação como quadros de dados para aprendizado de máquina. As projeções enriquecidas podem ser facilmente importadas para outros repositórios de dados. 

Você pode criar várias projeções em um repositório de dados de conhecimento para acomodar os vários clientes da organização. Talvez um desenvolvedor precise de acesso à representação JSON completa de um documento enriquecido e cientistas de dados ou analistas talvez queiram estruturas de dados granulares ou modulares formatadas pelo conjunto de qualificações.

Por exemplo, se um dos objetivos do processo de enriquecimento também for criar um conjunto de dados para treinar um modelo, projetar os dados no armazenamento de objetos seria uma maneira de usar os dados em seus pipelines de ciência de dados. Como alternativa, se você quiser criar um painel rápido do Power BI com base nos documentos enriquecidos, a projeção tabular funcionará bem.

<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

## <a name="where-do-i-start"></a>Por onde começo?

Recomendamos o uso do serviço Gratuito para fins de aprendizado, mas lembre-se de que o número de transações gratuitas é limitado a 20 documentos por dia, por assinatura.

Quando usar vários serviços, crie todos os serviços na mesma região para obter o melhor desempenho e minimizar os custos. Você não é cobrado pela largura de banda para dados de entrada ou dados de saída que vão para outro serviço na mesma região.

**Etapa 1: [Criar um recurso do Azure Search](search-create-service-portal.md)** 

**Etapa 2: [Criar uma conta do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Etapa 3: [Criar um recurso dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Etapa 4: [Começar com o portal](cognitive-search-quickstart-blob.md) ou [Introdução aos dados de exemplo usando o REST e o Postman](knowledge-store-howto.md)** 

Você pode usar o `api-version=2019-05-06-Preview` REST para criar um pipeline com base em IA que inclui o Repositório de Dados de Conhecimento. Na API de versão prévia mais recente, o objeto Skillset fornece a definição `knowledgeStore`.

## <a name="takeaways"></a>Observações

O Repositório de Dados de Conhecimento oferece uma variedade de benefícios, incluindo, mas não se limitando a, permitir o uso de documentos enriquecidos em diversos cenários de pesquisa, controles de custos e gerenciamento de desvios no processo de enriquecimento. Esses recursos estão disponíveis. Para usá-los, basta adicionar uma conta de armazenamento ao conjunto de qualificações e usar o idioma da expressão atualizado, conforme descrito em [Introdução ao Repositório de Dados de Conhecimento](knowledge-store-howto.md). 

## <a name="next-steps"></a>Próximas etapas

A abordagem mais simples para criar documentos enriquecidos é através do assistente **Importar dados**.

> [!div class="nextstepaction"]
> [Início Rápido: Experimente a pesquisa cognitiva em um passo a passo no portal](cognitive-search-quickstart-blob.md)
