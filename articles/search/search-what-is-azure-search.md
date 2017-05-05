---
title: "O que é o Azure Search | Microsoft Docs"
description: "O Azure Search é um serviço de pesquisa em nuvem hospedado totalmente gerenciado. Saiba mais nesta visão geral do recurso."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/24/2017
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 3720aed95706f6e8dd9a28085a6de0bc1f756a9b
ms.lasthandoff: 04/26/2017

---
# <a name="what-is-azure-search"></a>O que é o Azure Search?
O Azure Search é uma solução de pesquisa como um serviço de nuvem que delega o gerenciamento de infraestrutura e servidor à Microsoft, fornecendo um serviço pronto para uso que você pode preencher com seus dados e então utilizar para adicionar pesquisa a seu aplicativo Web ou móvel. O Azure Search permite que você adicione facilmente uma experiência de pesquisa robusta a seus aplicativos usando uma [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) simples ou o [SDK do .NET](search-howto-dotnet-sdk.md) sem gerenciar a infraestrutura de pesquisa nem se tornar um especialista em pesquisa.

## <a name="embed-a-powerful-search-experience-in-your-app-or-site"></a>Incorporar uma experiência de pesquisa poderosa em seu aplicativo ou site


### <a name="full-text-search-and-text-analysis"></a>Análise de texto e pesquisa de texto completo

As consultas podem ser formuladas usando a [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), que oferece operadores lógicos, operadores de pesquisa de frase, operadores de sufixo e operadores de precedência. Além disso, a [sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) pode permitir a pesquisa difusa, pesquisa por proximidade, aprimoramento de termo e expressões regulares. O Azure Search também oferece suporte aos [analisadores léxicos personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) para permitir que seu aplicativo lide com as consultas de pesquisa complexas usando expressões regulares e correspondência fonética.

### <a name="language-support"></a>Suporte ao idioma

O Azure Search oferece suporte aos analisadores léxicos para [56 idiomas diferentes](https://docs.microsoft.com/rest/api/searchservice/language-support). Usando tanto analisadores Lucene quanto analisadores da Microsoft (refinados por anos de processamento de idioma natural no Office e no Bing), o Azure Search pode analisar o texto na caixa de pesquisa do seu aplicativo para tratar inteligentemente elementos linguísticos específicos a um idioma, incluindo tempos verbais, sexo, substantivos plurais irregulares (por exemplo, 'mouse' x 'mice'), decomposição de palavras, quebra de palavras (para idiomas sem espaços) e muito mais.

### <a name="data-integration"></a>Integração de dados

Você pode enviar as estruturas de dados JSON para preencher um índice do Azure Search. E mais, para as fontes de dados com suporte, você pode usar [indexadores](search-indexer-overview.md) para rastrear automaticamente o Banco de Dados SQL do Azure, o DocumentDB do Azure ou o [armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) para sincronizar o conteúdo do índice de pesquisa com o armazenamento de dados primário.

A **decodificação de documentos** permite [indexar formatos de arquivos grandes](search-howto-indexing-azure-blob-storage.md), incluindo documentos do Microsoft Office, PDF e HTML.

### <a name="search-experience"></a>Experiência de pesquisa

+ As **sugestões de pesquisa** podem ser habilitadas para barras de pesquisa com preenchimento automático e barras de previsão de digitação. [Documentos reais no seu índice são sugeridos](https://docs.microsoft.com/rest/api/searchservice/suggesters) conforme os usuários inserem entrada de pesquisa parcial.

+ A **navegação mista** está habilitada por meio de [apenas um único parâmetro de consulta](https://docs.microsoft.com/azure/search/search-faceted-navigation). O Azure Search retorna uma estrutura de navegação mista, que você pode usar como o código por trás de uma lista de categorias para a filtragem autodirigida (por exemplo, para filtrar os itens do catálogo por faixa de preços ou marca).

+ Os **filtros** podem ser usados para incorporara navegação mista na IU de seu aplicativo, aprimorar a formulação da consulta e filtrar com base em critérios especificados pelo usuário ou pelo desenvolvedor. Crie filtros usando a [sintaxe OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

+ O **realce de ocorrência** [aplica uma formatação visual a uma palavra-chave correspondente nos resultados da pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Você pode escolher quais campos retornam os trechos de código destacados.

+ **Pontuação simples** é o principal benefício do Azure Search. Os [perfis de pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) são usados para modelar a relevância como uma função de valores nos próprios documentos. Por exemplo, talvez você queira que produtos mais recentes ou com desconto apareçam primeiro nos resultados da pesquisa. Você também pode criar perfis de pontuação usando marcas de pontuação personalizadas com base nas preferências de pesquisa do cliente que você controlou e armazenou separadamente.

+ **Classificação** é oferecida para vários campos por meio do esquema de índice e então alternada no momento da consulta com um parâmetro de pesquisa único.

+ **Paginação** e limitação dos resultados da pesquisa são [simples com o controle bem ajustado](search-pagination-page-layout.md) que o Azure Search oferece sobre seus resultados da pesquisa.  

### <a name="geosearch"></a>Pesquisa geográfica

O Azure Search com inteligência processa, filtra e exibe as localizações geográficas. Ela permite que os usuários explorem os dados com base na proximidade de um resultado da pesquisa para um local específico ou em uma região geográfica específica. Este vídeo explica como isso funciona: [Channel 9: dados do Azure Search e geoespaciais](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

### <a name="cloud-service-advantages"></a>Vantagens do serviço de nuvem

+ **Alta disponibilidade** garante uma experiência de serviço de pesquisa muito confiável. Quando dimensionada corretamente, o [Azure Search oferece um SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

+ **Totalmente gerenciada** como uma solução de ponta a ponta, o Azure Search não requer absolutamente nenhum gerenciamento de infraestrutura. Seu serviço pode ser adaptado facilmente às suas necessidades ajustando as duas dimensões para lidar com mais armazenamento de documentos, maiores cargas de consulta ou ambos.

### <a name="monitoring-and-reporting"></a>Monitoramento e emissão de relatórios

+ A **análise do tráfego de pesquisa** é [ coletada e analisada](search-traffic-analytics.md) para revelar novas informações sobre o que os usuários estão digitando na caixa de pesquisa.

+ As métricas nas consultas por segundo, latência e limitação são capturadas e informadas nas páginas do portal sem nenhuma configuração adicional necessária. Você também pode monitorar facilmente o índice e as contagens de documentos para poder ajustar a capacidade quando necessário. 

### <a name="tools-for-prototyping-and-inspection"></a>Ferramentas para criação de protótipos e inspeção

No portal, você pode usar o assistente **Importar dados** para configurar os indexadores, o designer de índices para criar um índice e o **Gerenciador de pesquisas** para enviar consultas em relação a todos os seus índices diretamente do portal do Azure de sua conta para testar as consultas e aprimorar os perfis de pontuação. Também é possível abrir qualquer índice para exibir seu esquema.

## <a name="how-it-works"></a>Como ele funciona
### <a name="step-1-provision-service"></a>Etapa 1: Provisionar serviço
Você pode criar um serviço do Azure Search usando o [Portal do Azure](https://portal.azure.com/) ou a [API de Gerenciamento de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Dependendo de como o serviço de pesquisa for configurado, você usará a camada gratuita de serviço que é compartilhada com outros assinantes do Azure Search, ou uma [camada paga](https://azure.microsoft.com/pricing/details/search/) que dedica recursos a serem usados apenas pelo seu serviço. Ao provisionar seu serviço, você também escolhe a região do data center que hospeda o serviço.

Dependendo de qual camada de serviço for escolhida, é possível escalonar seu serviço em duas dimensões: 1) adicionar réplicas para aumentar sua capacidade de lidar com cargas pesadas de consulta e 2) adicionar partições para o armazenamento de mais documentos. Ao manipular o armazenamento de documentos e a produtividade de consultas separadamente, você pode personalizar o serviço de pesquisa para suas necessidades específicas.

### <a name="step-2-create-index"></a>Etapa 2: Criar índice
Antes de carregar o conteúdo para o serviço do Azure Search, você deve primeiro definir um índice do Azure Search. Um índice é como uma tabela de banco de dados que contém os dados e pode aceitar consultas de pesquisa. Você define o esquema de índice a mapear para a estrutura dos documentos que deseja pesquisar, semelhante a campos em um banco de dados.

O esquema desses índices pode ser criado no Portal do Azure ou de modo programático usando o [SDK do .NET](search-howto-dotnet-sdk.md) ou a [API REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Quando o esquema é definido, você pode carregar os dados para o serviço do Azure Search, no qual eles serão indexados posteriormente.

### <a name="step-3-index-data"></a>Etapa 3: Indexar dados
Depois de definir os campos e os atributos do índice, você está pronto para carregar o conteúdo para o índice. Você pode usar os modelos push ou pull para carregar dados para o índice.

O modelo de pull é fornecido por meio de indexadores podem ser configurados para sob demanda ou atualizações agendadas (consulte [(API de REST de serviço do Azure Search](https://msdn.microsoft.com/library/azure/dn946891.aspx)) as operações do indexador), que permite uma fácil inclusão dos dados e as alterações de dados de um Azure DocumentDB, Banco de Dados SQL do Azure, Armazenamento de Blobs do Azure ou SQL Server hospedado em uma VM do Azure.

O modelo de push é fornecido por meio do SDK ou APIs REST usados para enviar documentos atualizados para um índice. Você pode enviar dados de praticamente qualquer conjunto de dados usando o formato JSON. Confira [Adicionar, atualizar ou excluir Documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou [Como usar o SDK do .NET)](search-howto-dotnet-sdk.md) para obter orientação sobre como carregar dados.

### <a name="step-4-search"></a>Etapa 4: Pesquisar
Depois de preencher o índice do Azure Search, você pode agora [emitir consultas de pesquisa](https://msdn.microsoft.com/library/azure/dn798927.aspx) para seu ponto de extremidade de serviço usando solicitações HTTP simples com API REST ou o SDK do .NET.

## <a name="rest-api--net-sdk"></a>API REST | SDK .Net

Embora várias tarefas possam ser executadas no portal, o Azure Search é para os desenvolvedores que desejam integrar a funcionalidade de pesquisa nos aplicativos existentes. As seguintes interfaces de programação estão disponíveis.

|Plataforma |Descrição |
|-----|------------|
|[REST](https://docs.microsoft.com/rest/api/searchservice/) | Comandos HTTP suportados por qualquer plataforma de programação e linguagem, incluindo Xamarin, Java e JavaScript|
|[SDK .NET](search-howto-dotnet-sdk.md) | O wrapper do .NET para a API REST oferece uma codificação eficiente no C# e outras linguagens de código gerenciado destinadas ao .NET Framework |

## <a name="free-trial"></a>Avaliação gratuita
Os assinantes do Azure podem [provisionar um serviço na camada gratuita](search-create-service-portal.md).

Se não for um assinante, você poderá [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F): você recebe créditos que podem ser usados para experimentar serviços pagos do Azure e, mesmo após eles serem utilizados, você pode manter a conta e usar os serviços gratuitos do Azure, como os Sites. Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança.

Se preferir, você pode [ativar benefícios para assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): todos os meses, sua assinatura do MSDN concede créditos que podem ser usados para serviços pagos do Azure. 

## <a name="watch-a-short-video"></a>Assista a um vídeo curto

Os mecanismos de pesquisa são os drivers comuns de recuperação de informações nos aplicativos móveis, na Web e nos armazenamentos de dados corporativos. O Azure Search fornece ferramentas para criar uma experiência de pesquisa semelhante aos grandes sites comerciais. Este vídeo de 9 minutos do gerente de programa Liam Cavanagh explica como integrar um mecanismo de pesquisa pode beneficiar seu aplicativo, os principais recursos do Azure Search e a aparência de um fluxo de trabalho típico. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ O vídeo de 0-3 minutos aborda os principais recursos e casos de uso.
+ O vídeo de 3-4 minutos aborda o provisionamento de serviços. 
+ O vídeo de 4-6 minutos aborda o Assistente para Importar Dados usado para criar um índice com o conjunto de dados interno de imóveis.
+ O vídeo de 6-9 minutos aborda o Gerenciador de Pesquisa e várias consultas.



