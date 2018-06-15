---
title: Pesquisa cognitiva para extração de dados e processamento de idioma natural no Azure Search | Microsoft Docs
description: Extração de dados, NLP (processamento de idioma natural) e processamento de imagem para criar conteúdo pesquisável na indexação do Azure Search usando habilidades cognitivas.
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: ca6c285348208a7ad24faf966073d641810039fc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641104"
---
# <a name="what-is-cognitive-search"></a>O que é pesquisa cognitiva?

Pesquisa cognitiva é um recurso de versão prévia do [Azure Search](search-what-is-azure-search.md), disponível em todos níveis no Centro-Sul dos EUA e na Europa Ocidental, que adiciona IA para cargas de trabalho de indexação. A extração de dados, o processamento de idioma natural e o processamento de imagem durante a indexação localiza as informações latentes em conteúdo não estruturado ou não pesquisável e as torna pesquisáveis no Azure Search.

A integração de IA é por meio de *habilidades cognitivas* que enriquecem os documentos de origem por meio de processos sequenciais, na rota para um índice de pesquisa. 

![Diagrama do pipeline de pesquisa cognitiva](./media/cognitive-search-intro/cogsearch-architecture.png "Visão geral do pipeline de pesquisa cognitiva")

As habilidades usadas durante a indexação podem ser predefinidas ou personalizadas:

+ As [habilidades predefinidas](cognitive-search-predefined-skills.md) são baseadas nos mesmos algoritmos de IA usados nas APIs de API de Serviços Cognitivos: [Reconhecimento de Entidade Nomeada](cognitive-search-skill-named-entity-recognition.md), [Extração de Frases-chave](cognitive-search-skill-keyphrases.md) e [OCR](cognitive-search-skill-ocr.md) são apenas alguns. 

+ As [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) podem ser desenvolvidas por você para qualquer processamento especializado necessário. Exemplos de habilidades personalizadas podem ser um módulo de entidade personalizada ou um classificador de documento direcionado a um domínio específico, como finanças, publicações científicas ou medicina.

> [!NOTE]
> A Pesquisa Cognitiva está na versão prévia pública e a execução do conjunto de habilidades é oferecida no momento gratuitamente. Posteriormente, o preço dessa capacidade será anunciado.

## <a name="components-of-cognitive-search"></a>Componentes da pesquisa cognitiva

O pipeline de pesquisa cognitiva baseia-se em [*indexadores* do Azure Search](search-indexer-overview.md) que rastreiam as fontes de dados e oferecem processamento de índice de ponta a ponta. Agora, as habilidades são anexadas aos indexadores, interceptando e enriquecendo documentos de acordo com o conjunto de habilidades que você define. Após a indexação, você pode acessar o conteúdo por meio de solicitações de pesquisa em todos os [tipos de consulta compatíveis com o Azure Search](search-query-overview.md).  Se você estiver começando a usar indexadores, esta seção o orientará pelas etapas.

### <a name="source-data-and-document-cracking-phase"></a>Fase de desbloqueio de fonte de dados e de documento

No início do pipeline, há texto não estruturado ou conteúdo que não é de texto (como imagens e arquivos JPEG de documentos digitalizados). Os dados precisam existir em um serviço de armazenamento de dados do Azure que possa ser acessado por um indexador. Os indexadores podem "desbloquear" documentos de origem para extrair o texto da fonte de dados.

![Fase de desbloqueio de documento](./media/cognitive-search-intro/document-cracking-phase-blowup.png "desbloqueio de documento")

 As fontes com suporte incluem o Armazenamento de Blobs do Azure, o Armazenamento de Tabelas do Azure, o Banco de Dados SQL do Azure e o Azure Cosmos DB. O conteúdo baseado em texto pode ser extraído dos seguintes tipos de arquivo: arquivos PDF, do Word, do PowerPoint e CSV. Para obter a lista completa, confira [Formatos com suporte](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="cognitive-skills-and-enrichment-phase"></a>Fase de enriquecimento e habilidades cognitivas

O enriquecimento ocorre por meio de *habilidades cognitivas* que executam operações atômicas. Por exemplo, quando há conteúdo de texto de um PDF, você pode aplicar a Detecção de Idioma de reconhecimento de entidade ou a extração de frase-chave para produzir novos campos no índice que não estão disponíveis nativamente na fonte. A coleção de habilidades usada em seu pipeline é chamada de *conjunto de habilidades*.  

![Fase de enriquecimento](./media/cognitive-search-intro/enrichment-phase-blowup.png "fase de enriquecimento")

Um conjunto de habilidades baseia-se em [habilidades cognitivas predefinidas](cognitive-search-predefined-skills.md) ou em [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) que você fornece e conecta ao conjunto de habilidades. Um conjunto de habilidades pode ser mínima ou altamente complexo e determina não apenas o tipo de processamento, mas também a ordem das operações. Um conjunto de habilidades mais os mapeamentos de campo definidos como parte de um indexador definem a especificação completa do pipeline de enriquecimento. Para obter mais informações sobre como reunir todas essas partes, confira [Define a skillset](cognitive-search-defining-skillset.md) (Definir um conjunto de habilidades).

Internamente, o pipeline gera uma coleção de documentos enriquecidos. Você pode decidir quais partes dos documentos enriquecidos devem ser mapeadas para campos indexáveis no índice de pesquisa. Por exemplo, se você aplicou a extração de frases-chave e as habilidades de reconhecimento de entidade, os novos campos se tornarão parte do documento enriquecido e poderão ser mapeados para os campos no índice. Consulte [Anotações](cognitive-search-concept-annotations-syntax.md) para saber mais sobre formações de entrada/saída.

### <a name="search-index-and-query-based-access"></a>Índice de pesquisa e acesso baseado em consulta

Quando o processamento é concluído, há um corpus de pesquisa consistindo em documentos enriquecidos, com texto totalmente pesquisável no Azure Search. [Consultando o índice](search-query-overview.md) é como os desenvolvedores e usuários acessam conteúdo enriquecido gerado pelo pipeline. 

![Índice com ícone de pesquisa](./media/cognitive-search-intro/search-phase-blowup.png "Índice com ícone de pesquisa")

O índice é como qualquer outro item criado para o Azure Search: é possível complementar com analisadores personalizados, invocar consultas de pesquisa difusa, adicionar pesquisa filtrada ou fazer experiências com perfis de pontuação para reformatar os resultados da pesquisa.

Os índice são gerados por meio de um esquema de índice que define os campos, os atributos e outras construções anexadas a um índice específico, como perfis de pontuação e mapas de sinônimo. Depois que um índice é definido e populado, é possível indexá-lo de forma incremental para capturar documentos de origem novos e atualizados. Algumas modificações exigem uma recompilação completa. Você deve usar um conjunto de dados pequeno até que o design de esquema fique estável. Para obter mais informações, confira [Como recompilar um índice](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Principais recursos e conceitos

| Conceito | DESCRIÇÃO| Links |
|---------|------------|-------|
| Conjunto de habilidades | Um recurso nomeado de nível superior que contém uma coleção de habilidades. Um conjunto de habilidades é o pipeline de enriquecimento. Ele é invocado durante a indexação por um indexador. | [Definir um conjunto de habilidades](cognitive-search-defining-skillset.md) |
| Habilidades cognitivas | Uma transformação atômica em um pipeline de enriquecimento. Geralmente, é um componente que extrai ou infere a estrutura e, portanto, aumenta o seu entendimento sobre os dados de entrada. Quase sempre, a saída é baseada em texto e o processamento é de idioma natural ou de imagem que extrai ou gera o texto usando entradas de imagem. A saída de uma habilidade pode ser mapeada para um campo em um índice ou usada como entrada para um enriquecimento de downstream. Uma habilidade é predefinida e fornecida pela Microsoft ou personalizada, ou seja, criada e implantada por você. | [Habilidades predefinidas](cognitive-search-predefined-skills.md) |
| Extração de dados | Abrange uma ampla gama de processamento, mas em relação à pesquisa cognitiva, a habilidade de reconhecimento de entidade nomeada geralmente é usada para extrair dados (uma entidade) de uma fonte que não fornece informações de modo nativo. | [Habilidade de reconhecimento de entidade nomeada](cognitive-search-skill-named-entity-recognition.md)| 
| Processamento de imagem | Infere o texto de uma imagem, como a capacidade de reconhecer um marco, ou extrai o texto de uma imagem. Exemplos comuns incluem o OCR para levantar caracteres de um arquivo de documento (JPEG) ou reconhecer o nome da rua em uma fotografia que contém uma placa de rua. | [Habilidade de análise de imagem](cognitive-search-skill-image-analysis.md) ou [Habilidade de OCR](cognitive-search-skill-ocr.md)
| Processamento de idioma natural | Processamento de texto para gerar insight e informações sobre entradas de texto. A Detecção de Idioma, a análise de sentimento e a extração de frases-chave são as habilidades que se enquadram no processamento de idioma natural.  | [Habilidade de Extração de Frases-chave](cognitive-search-skill-keyphrases.md), [Habilidade de Detecção de Idioma](cognitive-search-skill-language-detection.md), [Habilidade de Análise de Sentimento](cognitive-search-skill-sentiment.md) |
| Desbloqueio de documento | O processo de extração ou criação de conteúdo de texto de fontes que não são de texto durante a indexação. O OCR (reconhecimento óptico de caracteres) é um exemplo, mas geralmente se refere à funcionalidade principal do indexador, pois o indexador extrai o conteúdo de arquivos de aplicativo. A fonte de dados que fornece o local do arquivo de origem e a definição do indexador que fornece os mapeamentos de campo são os dois fatores-chave no desbloqueio de documento. | Confira [Indexadores](search-indexer-overview.md) |
| Formatação | Consolidar os fragmentos de texto em uma estrutura maior ou, ao contrário, dividir partes de texto maiores em um tamanho gerenciável para processamento de downstream posterior. | [Habilidade do Formatador](cognitive-search-skill-shaper.md), [Habilidade de Mesclagem de Texto](cognitive-search-skill-textmerger.md), [Habilidade de Divisão de Texto](cognitive-search-skill-textsplit.md) |
| Documentos enriquecidos | Uma estrutura interna transitória, que não pode ser acessada diretamente no código. Os documentos enriquecidos são gerados durante o processamento, mas apenas as saídas finais são mantidas em um índice de pesquisa. Os mapeamentos de campo determinam quais elementos de dados são adicionados ao índice. | Confira [Acessando documentos enriquecidos](cognitive-search-tutorial-blob.md#access-enriched-document). |
| Indexador |  Um rastreador que extrai dados e metadados pesquisáveis de uma fonte de dados externa e popula um índice com base nos mapeamentos de campo a campo entre o índice e a fonte de dados para desbloqueio de documento. Para enriquecimentos de pesquisa cognitiva, o indexador invoca um conjunto de habilidades e contém os mapeamentos de campo que associam a saída do enriquecimento aos campos de destino no índice. A definição do indexador contém todas as instruções e as referências para as operações do pipeline e o pipeline é invocado quando você executa o indexador. | [Indexadores](search-indexer-overview.md) |
| Fonte de dados  | Um objeto usado por um indexador para se conectar a uma fonte de dados externa dos tipos com suporte no Azure. | Confira [Indexadores](search-indexer-overview.md) |
| Índice | Um corpus de pesquisa persistente no Azure Search, criado usando um esquema de índice que define a estrutura e o uso do campo. | [Índices no Azure Search](search-what-is-an-index.md) | 


## <a name="where-do-i-start"></a>Por onde começo?

**Etapa 1: Criar um serviço de pesquisa em uma região fornecendo as APIs** 

+ Centro-Sul dos Estados Unidos
+ Europa Ocidental

**Etapa 2: Experiência prática para dominar o fluxo de trabalho**

+ [Início Rápido (portal)](cognitive-search-quickstart-blob.md)
+ [Tutorial (solicitações HTTP)](cognitive-search-tutorial-blob.md)
+ [Habilidades personalizadas de exemplo (C#)](cognitive-search-create-custom-skill-example.md)

**Etapa 3: Examinar a API (somente REST)**

Atualmente, apenas as APIs REST são fornecidas. Use o `api-version=2017-11-11-Preview` em todas as solicitações. Use as seguintes APIs para criar uma solução de pesquisa cognitiva. Apenas duas APIs são adicionadas ou estendidas para a pesquisa cognitiva. As outras APIs têm a mesma sintaxe que as versões disponíveis ao público.

| API REST | DESCRIÇÃO |
|-----|-------------|
| [Criar Fonte de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Um recurso que identifica uma fonte de dados externa, fornecendo os dados de origem usados para criar documentos enriquecidos.  |
| [Criar Conjunto de Habilidades (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que coordena o uso de [habilidades predefinidas](cognitive-search-predefined-skills.md) e de [habilidades cognitivas personalizadas](cognitive-search-custom-skill-interface.md) em um pipeline de enriquecimento durante a indexação. |
| [Criar o índice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Um esquema que expressa um índice do Azure Search. Os campos no índice são mapeados para os campos na fonte de dados ou para os campos fabricados durante a fase de enriquecimento (por exemplo, um campo para os nomes de organização criados pelo reconhecimento de entidade). |
| [Criar Indexador (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que define os componentes usados durante a indexação: incluindo uma fonte de dados, um conjunto de habilidades, associações de campo de origem e estruturas de dados intermediárias para o índice de destino e o próprio índice. Executar o indexador é o gatilho para a ingestão de dados e o enriquecimento. A saída é um corpus de pesquisa com base no esquema de índice, populado com os dados de origem, enriquecidos por conjuntos de habilidades.  |

**Lista de verificação: um fluxo de trabalho típico**

1. Subconjunto de dados de origem do Azure em uma amostra representativa. A indexação é um processo demorado, portanto, comece com um conjunto de dados representativo pequeno e, em seguida, compile incrementalmente à medida que sua solução amadurecer.

1. Crie um [objeto de fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) no Azure Search para fornecer uma cadeia de conexão para a recuperação de dados.

1. Crie um [conjunto de habilidades](https://docs.microsoft.com/rest/api/searchservice/create-skillset) com etapas de enriquecimento.

1. Defina o [esquema de índice](https://docs.microsoft.com/rest/api/searchservice/create-index). A coleção *Campos* inclui campos da fonte de dados. Você também deve usar stub de campos adicionais para manter os valores gerados para o conteúdo criado durante o enriquecimento.

1. Defina o [indexador](https://docs.microsoft.com/rest/api/searchservice/create-skillset) referenciando a fonte de dados, o conjunto de habilidades e o índice.

1. Dentro do indexador, adicione *outputFieldMappings*. Esta seção mapeia a saída do conjunto de habilidades (na etapa 3) para os campos de entrada no esquema de índice (na etapa 4).

1. Envie a solicitação *Criar Indexador* que você acabou de criar (uma solicitação POST com uma definição de indexador no corpo da solicitação) para expressar o indexador no Azure Search. Esta etapa é como você executa o indexador, invocando o pipeline.

1. Execute consultas para avaliar os resultados e modificar o código para atualizar os conjuntos de habilidades, o esquema ou a configuração do indexador.

1. [Reinicie o indexador](search-howto-reindex.md) antes de recompilar o pipeline.

Para obter mais informações sobre problemas ou dúvidas específicas, confira [Troubleshooting tips](cognitive-search-concept-troubleshooting.md) (Dicas de solução de problemas).

## <a name="next-steps"></a>Próximas etapas

+ [Documentação da pesquisa cognitiva](cognitive-search-resources-documentation.md)
+ [Início Rápido: experimente a pesquisa cognitiva em um passo a passo do portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: conheça as APIs de pesquisa cognitiva](cognitive-search-tutorial-blob.md)