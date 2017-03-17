---
title: "O que é o Azure Search | Microsoft Docs"
description: "Mecanismo de pesquisa com recursos de pesquisa avançados para sua barra de pesquisa em sites personalizados, aplicativos e armazenamentos de arquivos e dados corporativos."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: heidist
experiment_id: heidist-20170221
translationtype: Human Translation
ms.sourcegitcommit: 71694028366f48682881523c0220a4158078a76c
ms.openlocfilehash: b851f906b57225dce516d9704fdace80c44c4897
ms.lasthandoff: 02/23/2017

---
# <a name="what-is-azure-search"></a>O que é a Pesquisa do Azure?

O Azure Search oferece um mecanismo de pesquisa dedicado e programável com comportamentos de pesquisa inteligentes para ativar a barra de pesquisa para o conteúdo em seu site, seus aplicativos e nos armazenamentos de arquivos ou dados corporativos. 

![Barra de pesquisa ativada pelo Azure Search](./media/search-what-is-azure-search/search-powered-by-azsearch3.png)

Ter uma experiência de pesquisa sólida é uma necessidade para a maioria dos aplicativos web e móveis. Pesquisa de texto completo com os termos da consulta com preenchimento automático, correções ortográficas e correspondências com base em entradas semanticamente idênticas, mas superficialmente diferentes ("carro" e "auto"), é quase a barra mínima, apesar da complexidade técnica reforçando a experiência. Igualmente importantes são os requisitos operacionais para o dimensionamento, confiabilidade e sincronização entre os armazenamentos de dados de back-end e de pesquisa. 

O Azure Search fornece ampla funcionalidade, permitindo que você atenda aos requisitos operacionais e de pesquisa.

![Barra de pesquisa e página de pesquisa personalizada com os recursos de pesquisa comuns](./media/search-what-is-azure-search/search-page-callouts3.png)

## <a name="how-it-works"></a>Como ele funciona

Para usar o Azure Search, provisione um serviço gratuito ou pago em sua assinatura do Azure, crie e carregue um índice que contém o conteúdo pesquisável, em seguida, chame as APIs para enviar solicitações de pesquisa e lidar com os resultados. Um serviço pago será necessário se você quiser recursos dedicados em grande escala.

|Camada |Descrição |
|-----|------------|
|[Gratuito](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) | Um serviço compartilhado que pode ser rapidamente provisionado e usado para as pequenas cargas de trabalho, típico em cenários de aprendizado e avaliação. |
|[Faturáveis](search-sku-tier.md) | Um serviço dedicado em diversos níveis de capacidade, do Básico ao Standard de Alta Densidade; aceitando uma ampla variedade de configurações de implantação em diferentes camadas de preço.|

O Azure Search é executado na nuvem como um serviço gerenciado da Microsoft. Ela pode ser integrada com um código personalizado em qualquer plataforma do aplicativo. O serviço completamente gerenciado e o conteúdo privado estão globalmente disponíveis, são programáveis, escalonáveis e recuperáveis. 

Os serviços dedicados são executados 24x7 em escala com [99,9% dos contratos no nível do serviço ](https://azure.microsoft.com/support/legal/sla/search/v1_0/)feitos pelo Microsoft Azure.

## <a name="how-it-compares"></a>Como ele se compara

Vários provedores de serviço de nuvem oferecem mecanismos de pesquisa personalizados com recursos que ativam uma barra de pesquisa nos aplicativos personalizados. Alguns oferecem recursos comparáveis da linha de base, com a pesquisa de texto completo, pesquisa geográfica e capacidade de lidar com certo nível de ambiguidade nas entradas da pesquisa. Normalmente, é um [recurso especializado](#feature-drilldown) ou a facilidade e simplicidade geral das APIs, ferramentas e gerenciamento que determinam o melhor ajuste.

Como pontos de comparação com outras soluções de pesquisa, o Azure Search é mais forte para as cargas de trabalho de pesquisa de texto completo nos armazenamentos de conteúdo e bancos de dados no Azure, com uma camada adicional de funcionalidade necessária para os aplicativos que dependem principalmente da pesquisa para recuperação de informações e navegação do conteúdo. 

+ Integração dos dados do Azure (rastreadores) na camada de indexação
+ Portal do Azure para o gerenciamento central
+ Escala do Azure, confiabilidade e disponibilidade superior
+ Análise linguística e personalizada, com analisadores para a pesquisa sólida de texto completo em 56 idiomas
+ Principais recursos comuns para os aplicativos centrados em pesquisa, incluindo a relevância, estrutura mista, sugestões, sinônimos, pesquisa geográfica e muito mais (listados a seguir).

> [!Note]
> As fontes de dados não do Azure são totalmente compatíveis. Você pode direcionar qualquer coleção de documentos JSON para um índice do Azure Search.

Use casos que sejam capazes de aproveitar a mais ampla gama de recursos no Azure Search, incluindo catálogos online, programas de linha de negócios e aplicativos de descoberta de documentos.

### <a name="feature-drilldown"></a>Busca detalhada do recurso

#### <a name="full-text-search-and-text-analysis"></a>Análise de texto e pesquisa de texto completo

As consultas podem ser formuladas usando a [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), que oferece operadores lógicos, operadores de pesquisa de frase, operadores de sufixo e operadores de precedência. Além disso, a [sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) pode permitir a pesquisa difusa, pesquisa por proximidade, aprimoramento de termo e expressões regulares. O Azure Search também oferece suporte aos [analisadores léxicos personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) para permitir que seu aplicativo lide com as consultas de pesquisa complexas usando expressões regulares e correspondência fonética.

#### <a name="language-support"></a>Suporte ao idioma

O Azure Search oferece suporte aos analisadores léxicos para [56 idiomas diferentes](https://docs.microsoft.com/rest/api/searchservice/language-support). Usando tanto analisadores Lucene quanto analisadores da Microsoft (refinados por anos de processamento de idioma natural no Office e no Bing), o Azure Search pode analisar o texto na caixa de pesquisa do seu aplicativo para tratar inteligentemente elementos linguísticos específicos a um idioma, incluindo tempos verbais, sexo, substantivos plurais irregulares (por exemplo, 'mouse' x 'mice'), decomposição de palavras, quebra de palavras (para idiomas sem espaços) e muito mais.

#### <a name="data-integration"></a>Integração de dados

Você pode enviar as estruturas de dados JSON para preencher um índice do Azure Search. E mais, para as fontes de dados com suporte, você pode usar [indexadores](search-indexer-overview.md) para rastrear automaticamente o Banco de Dados SQL do Azure, o DocumentDB do Azure ou o [armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) para sincronizar o conteúdo do índice de pesquisa com o armazenamento de dados primário.

A **decodificação de documentos** permite [indexar formatos de arquivos grandes](search-howto-indexing-azure-blob-storage.md), incluindo documentos do Microsoft Office, PDF e HTML.

#### <a name="search-experience"></a>Experiência de pesquisa

+ As **sugestões de pesquisa** podem ser habilitadas para barras de pesquisa com preenchimento automático e barras de previsão de digitação. [Documentos reais no seu índice são sugeridos](https://docs.microsoft.com/rest/api/searchservice/suggesters) conforme os usuários inserem entrada de pesquisa parcial.

+ A **navegação mista** está habilitada por meio de [apenas um único parâmetro de consulta](https://docs.microsoft.com/azure/search/search-faceted-navigation). O Azure Search retorna uma estrutura de navegação mista, que você pode usar como o código por trás de uma lista de categorias para a filtragem autodirigida (por exemplo, para filtrar os itens do catálogo por faixa de preços ou marca).

+ Os **filtros** podem ser usados para incorporara navegação mista na IU de seu aplicativo, aprimorar a formulação da consulta e filtrar com base em critérios especificados pelo usuário ou pelo desenvolvedor. Crie filtros usando a [sintaxe OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

+ O **realce de ocorrência** [aplica uma formatação visual a uma palavra-chave correspondente nos resultados da pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Você pode escolher quais campos retornam os trechos de código destacados.

+ **Pontuação simples** é o principal benefício da Pesquisa do Azure. Os [perfis de pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) são usados para modelar a relevância como uma função de valores nos próprios documentos. Por exemplo, talvez você queira que produtos mais recentes ou com desconto apareçam primeiro nos resultados da pesquisa. Você também pode criar perfis de pontuação usando marcas de pontuação personalizadas com base nas preferências de pesquisa do cliente que você controlou e armazenou separadamente.

+ **Classificação** é oferecida para vários campos por meio do esquema de índice e então alternada no momento da consulta com um parâmetro de pesquisa único.

+ **Paginação** e limitação dos resultados da pesquisa são [simples com o controle bem ajustado](search-pagination-page-layout.md) que a Pesquisa do Azure oferece sobre seus resultados da pesquisa.  

#### <a name="geosearch"></a>Pesquisa geográfica

O Azure Search com inteligência processa, filtra e exibe as localizações geográficas. Ela permite que os usuários explorem os dados com base na proximidade de um resultado da pesquisa para um local específico ou em uma região geográfica específica. Este vídeo explica como isso funciona: [Channel 9: dados de Pesquisa do Azure e geoespaciais](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

#### <a name="cloud-service-advantages"></a>Vantagens do serviço de nuvem

+ **Alta disponibilidade** garante uma experiência de serviço de pesquisa muito confiável. Quando dimensionada corretamente, a [Pesquisa do Azure oferece um SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

+ **Totalmente gerenciada** como uma solução de ponta a ponta, a Pesquisa do Azure não requer absolutamente nenhum gerenciamento de infraestrutura. Seu serviço pode ser adaptado facilmente às suas necessidades ajustando as duas dimensões para lidar com mais armazenamento de documentos, maiores cargas de consulta ou ambos.

#### <a name="monitoring-and-reporting"></a>Monitoramento e emissão de relatórios

+ A **análise do tráfego de pesquisa** é [ coletada e analisada](search-traffic-analytics.md) para revelar novas informações sobre o que os usuários estão digitando na caixa de pesquisa.

+ As métricas nas consultas por segundo, latência e limitação são capturadas e informadas nas páginas do portal sem nenhuma configuração adicional necessária. Você também pode monitorar facilmente o índice e as contagens de documentos para poder ajustar a capacidade quando necessário. 

#### <a name="tools-for-prototyping-and-inspection"></a>Ferramentas para criação de protótipos e inspeção

No portal, você pode usar o assistente **Importar dados** para configurar os indexadores, o designer de índices para criar um índice e o **Gerenciador de pesquisas** para enviar consultas em relação a todos os seus índices diretamente do portal do Azure de sua conta para testar as consultas e aprimorar os perfis de pontuação. Também é possível abrir qualquer índice para exibir seu esquema.

## <a name="how-to-get-started"></a>Como começar

Comece com um serviço gratuito, em seguida, [crie e consulte um índice usando os dados de exemplo internos](search-get-started-portal.md). Você pode usar o portal para todas as tarefas, que é uma maneira rápida de experimentar o Azure Search antes de precisar escrever algum código.

1. Os assinantes do Azure podem [provisionar um serviço na camada gratuita](search-create-service-portal.md).

  Os não assinantes podem [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e usar os créditos gratuitos para experimentar os serviços pagos do Azure. Depois dos créditos serem usados, mantenha a conta e continue a usar os serviços gratuitos do Azure, como os sites. Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações solicite esse tipo de cobrança.

  Como alternativa, [ative os benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Todos os meses, sua assinatura do MSDN lhe oferece créditos que podem ser usados para serviços pagos do Azure. 

2. Execute o assistente **Importar dados** no [conjunto de dados de exemplo interno de imóveis](search-get-started-portal.md#create-index).

  Esse assistente pode gerar e carregar um índice da maioria das fontes de dados do Azure. O código é necessário para as fontes de dados não especificamente suportadas pelo assistente.

3. Use o [Gerenciador de Pesquisa](search-get-started-portal.md#query-index) para consultar o índice.

## <a name="rest-api--net-sdk"></a>API REST | SDK .Net

Embora várias tarefas possam ser executadas no portal, o Azure Search é para os desenvolvedores que desejam integrar a funcionalidade de pesquisa nos aplicativos existentes. As seguintes interfaces de programação estão disponíveis.

|Plataforma |Descrição |
|-----|------------|
|[REST](https://docs.microsoft.com/rest/api/searchservice/) | Comandos HTTP suportados por qualquer plataforma de programação e linguagem, incluindo Xamarin, Java e JavaScript|
|[SDK .NET](search-howto-dotnet-sdk.md) | O wrapper do .NET para a API REST oferece uma codificação eficiente no C# e outras linguagens de código gerenciado destinadas ao .NET Framework |

## <a name="watch-a-short-video"></a>Assista a um vídeo curto

Os mecanismos de pesquisa são os drivers comuns de recuperação de informações nos aplicativos móveis, na Web e nos armazenamentos de dados corporativos. O Azure Search fornece ferramentas para criar uma experiência de pesquisa semelhante aos grandes sites comerciais. Este vídeo de 9 minutos do gerente de programa Liam Cavanagh explica como integrar um mecanismo de pesquisa pode beneficiar seu aplicativo, os principais recursos do Azure Search e a aparência de um fluxo de trabalho típico. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ O vídeo de&0;-3 minutos aborda os principais recursos e casos de uso.
+ O vídeo de&3;-4 minutos aborda o provisionamento de serviços. 
+ O vídeo de&4;-6 minutos aborda o Assistente para Importar Dados usado para criar um índice com o conjunto de dados interno de imóveis.
+ O vídeo de&6;-9 minutos aborda o Gerenciador de Pesquisa e várias consultas.


