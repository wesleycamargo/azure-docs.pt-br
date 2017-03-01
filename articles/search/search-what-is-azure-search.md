---
title: "O que é o Azure Search | Microsoft Docs"
description: "A Pesquisa do Azure é um serviço de pesquisa em nuvem hospedado totalmente gerenciado. Saiba mais nesta visão geral do recurso."
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
ms.date: 01/12/2017
ms.author: ashmaka
experimental: true
experiment_id: heidist-20170221
translationtype: Human Translation
ms.sourcegitcommit: 292c9150822363aba3336b1efce579dc5362cb14
ms.openlocfilehash: 99b3babee9b252b2d741515391295d8b1dc5c747
ms.lasthandoff: 01/14/2017

---
# <a name="what-is-azure-search"></a>O que é a Pesquisa do Azure?
O Azure Search é uma solução de pesquisa como um serviço de nuvem que delega o gerenciamento de infraestrutura e servidor à Microsoft, fornecendo um serviço pronto para uso que você pode preencher com seus dados e então utilizar para adicionar pesquisa a seu aplicativo Web ou móvel. O Azure Search permite que você adicione facilmente uma experiência de pesquisa robusta a seus aplicativos usando uma [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) simples ou o [SDK do .NET](search-howto-dotnet-sdk.md) sem gerenciar a infraestrutura de pesquisa nem se tornar um especialista em pesquisa.

## <a name="give-your-users-a-powerful-search-experience"></a>Dê aos seus usuários uma experiência de pesquisa avançada
**Consultas avançadas** podem ser formuladas usando a [sintaxe de consulta simples](https://msdn.microsoft.com/library/azure/dn798920.aspx), que oferece operadores lógicos, operadores de pesquisa de frase, operadores de sufixo e operadores de precedência. Além disso, a [sintaxe de consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) pode permitir a pesquisa difusa, pesquisa por proximidade, aprimoramento de termo e expressões regulares. A Pesquisa do Azure também oferece suporte a analisadores léxicos personalizados para permitir que seu aplicativo lide com consultas de pesquisa complexas usando expressões regulares e correspondência fonética.

**Suporte ao idioma** está [incluído para idiomas diferentes de 56](https://msdn.microsoft.com/library/azure/dn879793.aspx). Usando tanto analisadores Lucene quanto analisadores da Microsoft (refinados por anos de processamento de idioma natural no Office e no Bing), o Azure Search pode analisar o texto na caixa de pesquisa do seu aplicativo para tratar inteligentemente elementos linguísticos específicos a um idioma, incluindo tempos verbais, sexo, substantivos plurais irregulares (por exemplo, 'mouse' x 'mice'), decomposição de palavras, quebra de palavras (para idiomas sem espaços) e muito mais.

**Sugestões de pesquisa** pode ser habilitada para barras de pesquisa com preenchimento automático e barras de previsão de digitação. [Documentos reais no seu índice são sugeridos](https://msdn.microsoft.com/library/azure/dn798936.aspx) conforme os usuários inserem entrada de pesquisa parcial.

**Realce de ocorrências** [permite](https://msdn.microsoft.com/library/azure/dn798927.aspx) que os usuários vejam o trecho de código de texto em cada resultado que contém as correspondências para a consulta. Você pode selecionar e escolher quais campos retornam trechos de código realçados.

**Faceted navigation** é facilmente adicionada à sua página de resultados de pesquisa com a Pesquisa do Azure. Usando [apenas um único parâmetro de consulta](https://msdn.microsoft.com/library/azure/dn798927.aspx), o Azure Search retornará todas as informações necessárias para construir uma experiência de pesquisa facetada na interface do usuário do seu aplicativo para permitir aos usuários fazer o drill-down e filtrar resultados de pesquisa (por exemplo, filtrar itens de catálogo por faixa de preços ou marca).

**geoespacial** permite processamento, filtro e exibição de locais geográficos de maneira inteligente. A Pesquisa do Azure permite que os usuários explorarem dados com base na proximidade de um resultado de pesquisa para um local específico ou em uma região geográfica específica. Este vídeo explica como isso funciona: [Channel 9: dados de Pesquisa do Azure e geoespaciais](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

**Filtros** podem ser usados para facilmente incorporar faceted navigation na interface do usuário de seu aplicativo, aprimorar a formulação de consulta e filtrar com base em critérios especificados pelo usuário ou pelo desenvolvedor. Crie filtros poderosos usando a [sintaxe de OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

## <a name="empower-your-developers-with-an-easy-to-use-service"></a>Capacitar seus desenvolvedores com um serviço fácil de usar
**Alta disponibilidade** garante uma experiência de serviço de pesquisa muito confiável. Quando dimensionada corretamente, a [Pesquisa do Azure oferece um SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

**Totalmente gerenciada** como uma solução de ponta a ponta, a Pesquisa do Azure não requer absolutamente nenhum gerenciamento de infraestrutura. Seu serviço pode ser adaptado facilmente às suas necessidades ajustando a sua escala em duas dimensões para lidar com mais armazenamento de documento, maiores cargas de consulta ou ambos.

A **integração de dados** usando [indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) permite que o Azure Search rastreie automaticamente o Banco de Dados SQL, o Azure DocumentDB ou o [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) para sincronizar o conteúdo do seu índice de pesquisa com seu armazenamento de dados primário.

**Violação de documento** está disponível (atualmente em visualização) [para ler e indexar os principais formatos de arquivos](search-howto-indexing-azure-blob-storage.md) , incluindo Microsoft Office, bem como documentos PDF e HTML.

**Análise de tráfego de pesquisa** é [facilmente coletada e analisada](search-traffic-analytics.md) para revelar novas informações sobre o que os usuários estão digitando na caixa de pesquisa.

**Pontuação simples** é o principal benefício da Pesquisa do Azure. [Perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx) são usados para permitir que as organizações modelem relevância como uma função de valores dos próprios documentos. Por exemplo, talvez você queira que produtos mais recentes ou com desconto apareçam primeiro nos resultados da pesquisa. Você também pode criar perfis de pontuação usando marcas de pontuação personalizadas com base nas preferências de pesquisa do cliente que você controlou e armazenou separadamente.

**Classificação** é oferecida para vários campos por meio do esquema de índice e então alternada no momento da consulta com um parâmetro de pesquisa único.

**Paginação** e limitação dos resultados da pesquisa são [simples com o controle bem ajustado](search-pagination-page-layout.md) que a Pesquisa do Azure oferece sobre seus resultados da pesquisa.  

**Gerenciador de pesquisa** permite que emitir consultas em todos os seus índices diretamente do portal do Azure da sua conta para testar consultas e refinar os perfis de pontuação.

## <a name="how-it-works"></a>Como ele funciona
### <a name="1-provision-service"></a>1. Provisionar serviços
Você pode criar um serviço do Azure Search usando o [Portal do Azure](https://portal.azure.com/) ou a [API de Gerenciamento de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Dependendo de como o serviço de pesquisa for configurado, você usará a camada gratuita de serviço que é compartilhada com outros assinantes da Pesquisa do Azure, ou uma [camada paga](https://azure.microsoft.com/pricing/details/search/) que dedica recursos a serem usados apenas pelo seu serviço. Ao provisionar seu serviço, você também escolhe a região do data center que hospeda o serviço.

Dependendo de qual camada de serviço for escolhida, é possível escalonar seu serviço em duas dimensões: 1) adicionar réplicas para aumentar sua capacidade de lidar com cargas pesadas de consulta e 2) adicionar partições para o armazenamento de mais documentos. Ao manipular o armazenamento de documentos e a produtividade de consultas separadamente, você pode personalizar o serviço de pesquisa para suas necessidades específicas.

### <a name="2-create-index"></a>2. Criar índice
Antes de carregar o conteúdo para o serviço de Pesquisa do Azure, você deve primeiro definir um índice de Pesquisa do Azure. Um índice é como uma tabela de banco de dados que contém os dados e pode aceitar consultas de pesquisa. Você define o esquema de índice a mapear para a estrutura dos documentos que deseja pesquisar, semelhante a campos em um banco de dados.

O esquema desses índices pode ser criado no Portal do Azure ou de modo programático usando o [SDK do .NET](search-howto-dotnet-sdk.md) ou a [API REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Quando o esquema é definido, você pode carregar os dados para o serviço de Pesquisa do Azure, no qual eles serão indexados posteriormente.

### <a name="3-index-data"></a>3. Dados de índice
Depois de definir os campos e os atributos do índice, você está pronto para carregar o conteúdo para o índice. Você pode usar os modelos push ou pull para carregar dados para o índice.

O modelo de pull é fornecido por meio de indexadores podem ser configurados para sob demanda ou atualizações agendadas (consulte [(API de REST de serviço de Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn946891.aspx)) as operações do indexador), que permite uma fácil inclusão dos dados e as alterações de dados de um Banco de Dados de Documentos do Azure, Banco de Dados SQL do Azure, Armazenamento de Blobs do Azure ou SQL Server hospedado em uma VM do Azure.

O modelo de push é fornecido por meio do SDK ou APIs REST usados para enviar documentos atualizados para um índice. Você pode enviar dados de praticamente qualquer conjunto de dados usando o formato JSON. Confira [Adicionar, atualizar ou excluir Documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou [Como usar o SDK do .NET)](search-howto-dotnet-sdk.md) para obter orientação sobre como carregar dados.

### <a name="4-search"></a>4. Pesquisar
Depois de preencher o índice de Pesquisa do Azure, você pode agora [emitir consultas de pesquisa](https://msdn.microsoft.com/library/azure/dn798927.aspx) para seu ponto de extremidade de serviço usando solicitações HTTP simples com API REST ou o SDK do .NET.

## <a name="try-it-now-for-free"></a>Experimente agora (gratuitamente!)
Os assinantes do Azure podem [provisionar um serviço na camada gratuita](search-create-service-portal.md).

Se não for um assinante, você poderá [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F): você recebe créditos que podem ser usados para experimentar serviços pagos do Azure e, mesmo após eles serem utilizados, você pode manter a conta e usar os serviços gratuitos do Azure, como os Sites. Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança.

Se preferir, você pode [ativar benefícios para assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): todos os meses, sua assinatura do MSDN concede créditos que podem ser usados para serviços pagos do Azure. 



