---
title: "Como implementar a navegação facetada no Azure Search | Microsoft Docs"
description: "Navegação facetada adicionada aos aplicativos que são integrados à Pesquisa do Azure, um serviço de pesquisa hospedado na nuvem do Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: mblythe
editor: 
ms.assetid: cdf98fd4-63fd-4b50-b0b0-835cb08ad4d3
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/17/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0a044412d138478cf8ebd8ed8768fd4764b8a551


---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Como implementar a navegação facetada na Pesquisa do Azure
A navegação facetada é um mecanismo de filtragem que fornece navegação de busca detalhada autodirigida em aplicativos de pesquisa. Embora o termo “navegação facetada” possa ser desconhecido, é quase certo que você já a tenha utilizado. Como mostra o exemplo a seguir, a navegação facetada é nada mais do que as categorias usadas para filtrar resultados.

## <a name="what-it-looks-like"></a>Que aparência ela tem
 ![][1]

Facetas podem ajudar a encontrar o que você está procurando, garantindo que você não obterá zero resultados. Como desenvolvedor, facetas permitem que você exponha os critérios de pesquisa mais úteis para navegar pelo seu corpo de busca. Em aplicativos de varejo online, a navegação facetada geralmente é criada sobre marcas, departamentos (sapatos infantis), tamanho, preço, popularidade e classificações. 

A implementação da navegação facetada varia entre diferentes tecnologias de pesquisa e pode ser um processo muito complexo. Na Pesquisa do Azure, a navegação mistas baseia-se no momento da consulta, usando os campos atribuídos especificados anteriormente no seu esquema. Nas consultas compiladas pelo seu aplicativo, uma consulta deve enviar *parâmetros de faceta de consulta* para receber os valores de filtro de faceta disponíveis para esse conjunto de resultados do documento. Para realmente recortar o conjunto de resultados do documento, o aplicativo deve aplicar uma expressão `$filter` .

Em termos de desenvolvimento do aplicativo, escrever um código que construa consultas constitui a maior parte do trabalho. Muitos dos comportamentos de aplicativo que você desejaria da navegação facetada são fornecidos pelo serviço, incluindo suporte integrado para configuração de intervalos e obtenção de contagens de resultados de uma determinada faceta. O serviço também inclui padrões pertinentes, que o ajudarão a evitar estruturas de navegação complicadas. 

Este artigo contém as seguintes seções:

* [Como criá-la](#howtobuildit)
* [Criar a camada de apresentação](#presentationlayer)
* [Compilar o índice](#buildindex)
* [Verificar a qualidade de dados](#checkdata)
* [Compilar a consulta](#buildquery)
* [Dicas sobre como controlar a navegação facetada](#tips)
* [Navegação mista com base em valores de intervalo](#rangefacets)
* [Navegação mista com base em GeoPoints](#geofacets)
* [Experimentar](#tryitout)

## <a name="why-use-it"></a>Por que usá-la
Os aplicativos de pesquisa mais eficazes têm vários modelos de interação, além de uma caixa de pesquisa. A navegação facetada é um ponto de entrada alternativo para pesquisa, oferecendo uma alternativa conveniente a digitar expressões de pesquisa complexas manualmente.

## <a name="know-the-fundamentals"></a>Conheça os conceitos básicos
Se você for novo no desenvolvimento de pesquisa, a melhor maneira de pensar em navegação facetada é que ela mostra as possibilidades de pesquisa autodirigida. É um tipo de experiência de busca detalhada, com base em filtros predefinidos usados para restringir os resultados da pesquisa rapidamente por meio de ações de apontar e clicar. 

**Modelo de interação**

A experiência de pesquisa para navegação facetada é iterativa, então vamos começar entendê-la como uma sequência de consultas que se desenrolam em resposta às ações do usuário.

O ponto de partida é uma página de aplicativo que oferece navegação facetada, normalmente posicionada na periferia. A navegação facetada costuma ser uma estrutura de árvore, com caixas de seleção para cada valor ou texto que pode ser clicado. 

1. Uma consulta enviada para a Pesquisa do Azure especifica a estrutura de navegação facetada por meio de um ou mais parâmetros de consulta de faceta. Por exemplo, a consulta pode incluir `facet=Rating`, talvez com uma opção `:values` ou `:sort` para refinar ainda mais a apresentação.
2. A camada de apresentação renderiza uma página de pesquisa que oferece navegação facetada, usando as facetas especificadas na solicitação.
3. Dada uma estrutura de navegação facetada que inclui classificação, o usuário clica em "4" para indicar que somente os produtos com uma classificação de 4 ou superior devem ser mostrados. 
4. Em resposta, o aplicativo envia uma consulta que inclui `$filter=Rating ge 4` 
5. A camada de apresentação atualiza a página, mostrando um conjunto de resultados reduzido apenas com os itens que atendem aos novos critérios (nesse caso, os produtos classificados como 4 e superior).

Uma faceta é um parâmetro de consulta, mas não a confunda com uma entrada de consulta. Ela nunca é usada como critério de seleção em uma consulta. Em vez disso, pense em facetas usadas como parâmetros de consulta como entradas para a estrutura de navegação que retorna como resposta. Para cada parâmetro de consulta de faceta que você fornecer, a Pesquisa do Azure avaliará quantos documentos estão nos resultados parciais para cada valor de faceta.

Observe o `$filter` na etapa 4. Isso é um aspecto importante da navegação facetada. Embora facetas e filtros sejam independentes na API, você precisará de ambos para fornecer a experiência que pretende. 

**Padrões de design**

No código do aplicativo, o padrão é usar parâmetros de consulta de faceta para retornar a estrutura de navegação facetada juntamente com os resultados da faceta, mais de uma expressão de $filter que manipula o evento de clique. Imagine a expressão `$filter` como o código por trás do verdadeiro recorte dos resultados da pesquisa retornados para a camada de apresentação. Dada uma faceta de cores, clicar na cor Vermelho é implementado através de uma expressão `$filter` que seleciona somente os itens que têm cor vermelha. 

**Noções básicas sobre a consulta na Pesquisa do Azure**

Na pesquisa do Azure, uma solicitação é especificada por meio de um ou mais parâmetros de consulta (consulte [Procurar documentos](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obter uma descrição de cada um deles). Nenhum dos parâmetros de consulta é necessário, mas você deve ter pelo menos um para que uma consulta seja válida.

Precisão, geralmente entendido como a capacidade de filtrar correspondências irrelevantes, é obtida por meio de uma ou de ambas essas expressões:

* **search=**<br/>
   O valor deste parâmetro constitui a expressão de pesquisa. Pode ser uma única parte de texto ou uma expressão de pesquisa complexa que inclua vários termos e operadores. No servidor, uma expressão de pesquisa é usada para pesquisa de texto completo, consultando os campos de pesquisa no índice para correspondência de termos, retornando resultados em ordem de classificação. Se você definir `search` como nulo, a consulta é executada sobre todo o índice (ou seja, `search=*`). Nesse caso, outros elementos da consulta, como um `$filter` ou perfil de pontuação, serão os principais fatores que afetam quais documentos são retornados `($filter`) e em qual ordem (`scoringProfile` ou `$orderb`y).
* **$filter=**<br/>
   Um filtro é um mecanismo poderoso para limitar o tamanho dos resultados da pesquisa com base nos valores de atributos específicos do documento. Um `$filter` é avaliado primeiro, seguido pela lógica de facetagem que gera os valores disponíveis e as contagens correspondentes para cada valor

Expressões de pesquisa complexas diminuirão o desempenho da consulta. Sempre que possível, utilize expressões de filtro bem construídas para aumentar a precisão e melhorar o desempenho da consulta.

Para entender melhor como um filtro adiciona mais precisão, compare uma expressão de pesquisa complexa a uma que inclui uma expressão de filtro:

* `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
* `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Embora ambas as consultas sejam válidas, a segunda é melhor se você está procurando estabelecimentos que não sejam motéis com estacionamento em Seattle. A primeira consulta depende dessas palavras específicas serem mencionadas ou não nos campos de cadeia de caracteres como Nome, Descrição e qualquer outro campo que contenha dados pesquisáveis. A segunda consulta procura correspondências precisas em dados estruturados, e provavelmente será muito mais precisa.

Em aplicativos que incluam navegação facetada, você desejará ter certeza de que cada ação do usuário em uma estrutura de navegação facetada é acompanhada de uma redução dos resultados da pesquisa, obtida por meio de uma expressão de filtro.

<a name="howtobuildit"></a>

## <a name="how-to-build-it"></a>Como criá-la
A navegação facetada na Pesquisa do Azure é implementada no código do aplicativo que compila a solicitação, mas se baseia em elementos predefinidos no esquema.

Predefinido em sua pesquisa de índice está o atributo de índice `Facetable [true|false]` , definido nos campos selecionados para habilitar ou desabilitar seu uso em uma estrutura de navegação facetada. Sem `"Facetable" = true`, um campo não pode ser usado na navegação facetada.

No momento da consulta, o código do aplicativo cria uma solicitação que inclui `facet=[string]`, um parâmetro de solicitação que fornece o campo pelo qual facetar. Uma consulta pode ter múltiplas facetas, como `&facet=color&facet=category&facet=rating`, cada uma separada por um caractere “e” comercial (&).

O código do aplicativo também deve construir uma expressão `$filter` para manipular os eventos de clique na navegação facetada. Um `$filter` reduz os resultados da pesquisa, usando o valor da faceta como critério de filtro.

A Pesquisa do Azure retorna os resultados da pesquisa, segundo os termos inseridos pelo usuário, junto com atualizações para a estrutura de navegação facetada. Na Pesquisa do Azure, a navegação facetada é uma construção de nível único, com valores de facetas e contagens de quantos resultados são encontrados para cada uma delas.

A camada de apresentação em seu código fornece a experiência do usuário. Ele deve listar as partes constituintes da navegação facetada, como o rótulo, valores, caixas de seleção e a contagem. A API REST de Pesquisa do Azure é independente de plataforma, portanto, use qualquer idioma e plataforma que desejar. O importante é incluir elementos de interface do usuário que deem suporte a atualização incremental, com estado da interface do usuário sendo atualizado quando cada faceta adicional é selecionada. 

Nas seções a seguir, vamos examinar mais detalhadamente como criar cada parte, começando com a camada de apresentação.

<a name="presentationlayer"></a>

## <a name="build-the-presentation-layer"></a>Criar a camada de apresentação
Trabalhar na camada de apresentação pode ajudá-lo a descobrir requisitos que de outro modo poderiam ter sido ignorados, além de entender quais recursos são essenciais para a experiência de pesquisa.

Em termos de navegação facetada, sua página ou aplicativo da Web exibe a estrutura de navegação facetada, detecta a entrada do usuário na página e insere os elementos alterados. 

Para aplicativos da Web, o AJAX normalmente é usado na camada de apresentação porque ele permite que você atualize as alterações incrementais. Você também pode usar o MVC ASP.NET ou qualquer outra plataforma de visualização que possa se conectar a um serviço de pesquisa do Azure via HTTP. O aplicativo de exemplo referenciado neste artigo – **Catálogo AdventureWorks** – trata-se de um aplicativo MVC ASP.NET.

O exemplo a seguir, extraído do arquivo **index.cshtml** da página de resultados do aplicativo de exemplo, compila uma estrutura HTML dinâmica para exibir a navegação facetada em sua página de resultados da pesquisa. No exemplo, a navegação facetada baseia-se na página de resultados da pesquisa e é exibida depois de o usuário ter enviado um termo de pesquisa.

Observe que cada faceta tem um rótulo (Cores, Categorias, Preços), uma associação a um campo facetado (color, categoryName, listPrice) e um parâmetro `.count` , usado para retornar o número de itens encontrados para o resultado dessa faceta.

  ![][2]

> [!TIP]
> Ao criar a página de resultados da pesquisa, lembre-se de adicionar um mecanismo para limpar facetas. Se você usar as caixas de seleção, os usuários podem intuir facilmente como limpar os filtros. Para outros layouts, talvez seja necessário um padrão de navegação estrutural ou outra abordagem criativa. Por exemplo, no catálogo AdventureWorks, um aplicativo de exemplo, você pode clicar no título, catálogo AdventureWorks, para redefinir a página de pesquisa.
> 
> 

<a name="buildindex"></a>

## <a name="build-the-index"></a>Compilar o índice
A facetagem está habilitada no índice de campo por campo, por meio desse atributo de índice: `"Facetable": true`.  
Todos os tipos de campo que poderiam ser usados na navegação facetada são `Facetable` por padrão. Esses tipos de campo incluem `Edm.String`, `Edm.DateTimeOffset` e todos os tipos de campo numérico (basicamente, todos os tipos de campo são facetáveis exceto `Edm.GeographyPoint`, que não pode ser usado na navegação facetada). 

Ao criar um índice, uma prática recomendada para navegação facetada é desativar explicitamente a facetagem para os campos que nunca devem ser usados como uma faceta.  Em particular, os campos de cadeia de caracteres para valores únicos, como uma ID ou nome de produto, devem ser definidos como `"Facetable": false` para evitar seu uso acidental (e ineficaz) em uma navegação facetada.

A seguir temos o esquema para o aplicativo de exemplo do Catálogo AdventureWorks (recortado eliminando alguns atributos, para reduzir o tamanho geral):

 ![][3]

Observe que `Facetable` é desativado para os campos de cadeia de caracteres que não devem ser usados como facetas, como um nome ou ID. Desligar a facetagem onde você não precisa dela ajuda a manter o tamanho do índice pequeno e geralmente melhora o desempenho.

> [!TIP]
> Como prática recomendada, inclua o conjunto completo de atributos de índice para cada campo. Embora `Facetable` seja ativado por padrão para quase todos os campos, definir propositadamente cada atributo pode ajudá-lo a considerar as implicações de cada decisão do esquema. 
> 
> 

<a name="checkdata"></a>

## <a name="check-for-data-quality"></a>Verificar a qualidade de dados
Ao desenvolver qualquer aplicativo centrado em dados, preparar os dados geralmente é uma das maiores partes do trabalho. Os aplicativos de pesquisa não são exceção. A qualidade dos seus dados tem um efeito direto quanto à estrutura de navegação facetada se materializar conforme esperado ou não, bem como sua eficácia em lhe ajudar a criar filtros que reduzam o conjunto de resultados.

Na Pesquisa do Azure, o corpo de busca é formado de documentos que preenchem um índice. Os documentos fornecem os valores que são usados para computar facetas. Se você quiser realizar a facetagem por Marca ou Preço, cada documento deverá conter valores para *BrandName* e *ProductPrice* que sejam válidos, consistentes e produtivos como uma opção de filtragem.

Alguns lembretes de pelo que se deve procurar estão listados abaixo:

* Pergunte-se, para cada campo segundo o qual você deseja realizar a facetagem, se ele contém valores que são adequados como filtros na pesquisa autodirigida. Os valores devem ser curtos, descritivos e suficientemente diferentes para oferecer uma escolha clara entre as opções de concorrentes.
* Erros de ortografia ou valores quase correspondentes. Se você realiza a facetagem por Cor e os valores de campo incluem Laranja e Laranja (erro), uma facetagem baseada no campo Cor utilizaria ambos.
* Texto composto
de caracteres maiúsculos e minúsculos também pode causar estragos na navegação facetada, com laranja e Laranja aparecendo como dois valores diferentes. 
* Versões do mesmo valor no singular e no plural podem resultar em uma faceta separada para cada uma.

Como você pode imaginar, a auditoria na preparação dos dados é um aspecto essencial para uma navegação facetada eficiente.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Compilar a consulta
O código que você escreve para a compilação de consultas deve especificar todas as partes de uma consulta válida, incluindo expressões de pesquisa, facetas, filtros, perfis de pontuação – tudo o que for usado para formular uma solicitação. Nesta seção, exploraremos onde as facetas se encaixam em uma consulta e como os filtros são usados com facetas para fornecer um conjunto de resultados reduzido.

Um exemplo é geralmente um bom lugar para começar. O exemplo a seguir, extraído do arquivo **CatalogSearch.cs** , compila uma solicitação que cria navegação facetada com base em Cor, Categoria e Preço. 

Observe que as facetas são integrais nesse aplicativo de exemplo. A experiência de pesquisa no Catálogo AdventureWorks foi criada em torno de filtros e navegação facetada. Isso fica evidente no posicionamento proeminente de navegação facetada na página. O aplicativo de exemplo inclui parâmetros URI para facetas (cor, categoria, preços) como propriedades do método de pesquisa (como construído no aplicativo de exemplo).

  ![][4]

Um parâmetro de faceta para consulta é definido como um campo e, dependendo do tipo de dados, pode ser ainda mais parametrizado por uma lista delimitada por vírgulas que inclua `count:<integer>`, `sort:<>`, `intervals:<integer>` e `values:<list>`. Há suporte para dados numéricos em uma lista de valores ao configurar intervalos. Consulte [Pesquisar documentos (API da Pesquisa do Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obter detalhes de utilização.

Além de facetas, a solicitação formulada pelo seu aplicativo também deve criar filtros para restringir o conjunto de documentos candidatos com base em uma seleção de valor da faceta. Para uma loja de bicicletas, a navegação facetada oferece dicas para perguntas como "quais cores, fabricantes e tipos de bicicletas estão disponíveis", enquanto a filtragem responde perguntas como "Quais bicicletas exatamente são vermelhas, mountain bikes e pertencem a esta faixa de preço".

Quando um usuário clica em "Vermelho" para indicar que somente os produtos vermelhos devem ser mostrados, a próxima consulta que o aplicativo envia incluiria `$filter=Color eq ‘Red’`.

## <a name="best-practices-for-faceted-navigation"></a>Práticas recomendadas para navegação facetada
A lista a seguir resume algumas práticas recomendadas.

* **Precisão**<br/>
   Utilize filtros. Se você depender apenas de expressões de pesquisa, a lematização pode fazer com que seja retornado um documento que não tenha o valor preciso da faceta em nenhum de seus campos. 
* **Campos de destino**<br/>
   Em busca detalhada facetada, você geralmente deseja incluir apenas os documentos que tenham o valor da faceta em um campo específico (facetado), não em qualquer lugar por todos os campos de pesquisa pesquisáveis. A adição de um filtro reforça o campo alvo, direcionando o serviço para pesquisar somente no campo facetado por um valor correspondente.
* **Eficiência de índice**<br/>
  Se seu aplicativo usa a navegação facetada exclusivamente (ou seja, sem nenhuma caixa de pesquisa), marque o campo como `searchable=false`, `facetable=true` para produzir um índice mais compacto. Além disso, a indexação ocorre somente em valores de faceta inteiros, sem nenhuma quebra de palavra nem indexação das partes componentes de um valor com várias palavras.
* **Desempenho**<br/>
   Filtros restringem o conjunto de documentos candidatos para pesquisa e exclui-los da classificação. Se você tem um grande conjunto de documentos, o uso de uma busca detalhada facetada muito seletiva geralmente resultará em um desempenho significativamente melhor.

<a name="tips"></a> 

## <a name="tips-on-how-to-control-faceted-navigation"></a>Dicas sobre como controlar a navegação facetada
Abaixo está uma folha de dicas com orientações sobre questões específicas.

**Adicionar rótulos para cada campo na navegação facetada**

Rótulos geralmente são definidos no HTML ou no formulário (**index.cshtml** no aplicativo de exemplo). Não há API na Pesquisa do Azure para rótulos de navegação facetada ou qualquer outro tipo de metadado.

**Definir os campos podem ser usados como facetas**

Lembre-se que o esquema de índice determina quais campos estão disponíveis para uso como uma faceta. Supondo que um campo seja passível de facetagem, a consulta especifica por quais campos realizar essa facetagem. O campo pelo qual você está realizando a facetagem fornece os valores que aparecem abaixo do rótulo. 

Os valores que aparecem em cada rótulo são recuperados do índice. Por exemplo, se o campo da faceta é *Cor*, os valores disponíveis para filtragem adicional serão os valores para esse campo (Vermelho, Preto e assim por diante).

Apenas para valores dos campos Numérico e DataHora, você pode definir explicitamente os valores no campo da faceta (por exemplo, `facet=Rating,values:1|2|3|4|5`). Uma lista de valores é permitida para esses tipos de campo para simplificar a separação dos resultados da faceta em intervalos contíguos (qualquer um dos intervalos com base em valores numéricos ou períodos de tempo). 

**Recortar resultados da faceta**

Resultados da faceta são documentos encontrados nos resultados da pesquisa que correspondem a um termo usado como faceta. No exemplo a seguir, nos resultados da pesquisa para *computação em nuvem*, 254 itens também têm *especificação interna* como um tipo de conteúdo. Os itens não são necessariamente mutuamente exclusivos. Se um item atende aos critérios de ambos os filtros, ele será contado em cada um deles. Isso é possível ao realizar a facetagem em campos `Collection(Edm.String)` , que geralmente são usados para implementar a marcação do documento.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Em geral, se você descobrir que resultados de faceta são persistentemente muito grandes, é recomendável adicionar mais filtros conforme explicado nas seções anteriores, para dar aos usuários do aplicativo mais opções para limitar a pesquisa.

**Limitar itens na navegação facetada**

Para cada campo facetado na árvore de navegação, há um limite padrão de 10 valores. Esse padrão faz sentido para estruturas de navegação porque mantém a lista de valores de um tamanho gerenciável. Você pode substituir o padrão, atribuindo um valor para a contagem.

* `&facet=city,count:5` especifica que apenas as 5 primeiras cidades localizadas nos resultados com a melhor classificação são retornadas como um resultado da faceta. Dado um termo de pesquisa "aeroporto" e 32 correspondências, se a consulta especificar `&facet=city,count:5`, somente as cinco primeiras cidades únicas com a maioria dos documentos nos resultados da pesquisa serão incluídas nos resultados da faceta.

Observe a diferença entre os resultados da pesquisa e os resultados da faceta. Os resultados da pesquisa são todos os documentos que correspondem à consulta. Os resultados da faceta são as correspondências para cada valor da faceta. No exemplo, os resultados da pesquisa incluem nomes de cidades que não estão na lista de classificação da faceta (5 em nosso exemplo). Os resultados que são filtrados por meio de navegação facetada se tornam visíveis ao usuário quando ele ou ela limpa facetas ou escolhe outras facetas além de Cidade. 

> [!NOTE]
> Discutir `count` quando há mais de um tipo pode ser confuso. A tabela a seguir oferece um breve resumo de como o termo é usado na API de Pesquisa do Azure, um código de exemplo e a documentação. 
> 
> 

* `@colorFacet.count`<br/>
   No código de apresentação, você deverá ver um parâmetro de contagem na faceta, usada para exibir o número de resultados da faceta. Nos resultados da faceta, a contagem indica o número de documentos que correspondam ao intervalo ou termo da faceta em questão.
* `&facet=City,count:12`<br/>
   Em uma consulta de faceta, você pode definir a contagem para um determinado valor.  O padrão é 10, mas você pode defini-lo com um valor maior ou menor. Definir `count:12` retorna as 12 correspondências melhor classificadas nos resultados da faceta pela contagem de documento.
* "`@odata.count`"<br/>
   Na resposta da consulta, esse valor indica o número de itens correspondentes nos resultados da pesquisa. Em média, ele é maior que a soma de todos os resultados de faceta combinados, devido à presença de itens que correspondem ao termo de pesquisa, mas não tem correspondências com o valor da faceta.

**Níveis na navegação facetada** 

Como observado, não há nenhum suporte direto para aninhamento de facetas em uma hierarquia. Navegação facetada pronta para uso imediato dá suporte a apenas um nível de filtros. No entanto, existem soluções alternativas. Você pode codificar uma estrutura hierárquica de facetas em um `Collection(Edm.String)` com um ponto de entrada por hierarquia. Implementar essa solução alternativa está além do escopo deste artigo, mas você pode ler sobre as coleções em [OData por meio de exemplos](http://msdn.microsoft.com/library/ff478141.aspx). 

**Validar campos**

Se você criar a lista de facetas dinamicamente com base em entrada de usuário não confiável, você deve validar que os nomes dos campos facetados são válidos ou então evadir os nomes de escape ao construir URLs usando `Uri.EscapeDataString()` em .NET ou o equivalente em sua plataforma de preferência.

**Contagens nos resultados da faceta**

Ao adicionar um filtro a uma consulta facetada, talvez você queira manter a instrução da faceta (por exemplo, `facet=Rating&$filter=Rating ge 4`). Tecnicamente, facet=Rating não é necessária, mas mantê-la retorna as contagens de valores de faceta para classificações iguais ou superiores a 4. Por exemplo, se um usuário clica em "4" e a consulta inclui um filtro para maior ou igual a "4", contagens são retornadas para cada classificação igual ou superior a 4.  

**Implicações da fragmentação nas contagens de facetas**

Em determinadas circunstâncias, você pode achar que as contagens de faceta não coincidem com os conjuntos de resultados (consulte [Navegação facetada na Pesquisa do Azure (postagem no fórum)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

As contagens de faceta podem ser imprecisas devido à arquitetura de fragmentação. Cada índice de pesquisa tem vários fragmentos, e cada um deles relata as N facetas com maior classificação por contagem de documento, que em seguida são combinadas em um único resultado. Se alguns fragmentos têm muitos valores correspondentes enquanto outros têm menos, você poderá descobrir que alguns valores de faceta estão faltando ou com contagem reduzida nos resultados.

Embora esse comportamento possa mudar a qualquer momento, se você encontrar esse problema hoje, você pode contorná-lo artificialmente aumentando count:<number> para um número muito grande para impor a emissão de relatórios completos de cada fragmento. Se o valor de contagem: é maior que ou igual ao número de valores exclusivos no campo, você tem a garantia de resultados precisos. No entanto, quando as contagens de documento são realmente altas há uma penalidade de desempenho, portanto, use essa opção criteriosamente.

<a name="rangefacets"></a>

## <a name="facet-navigation-based-on-a-range-values"></a>Navegação facetada com base em valores de intervalo
A facetagem em intervalos é um requisito comum de aplicativo de pesquisa. Intervalos têm suporte para dados numéricos e valores de DataHora. Você pode ler mais sobre cada abordagem em [Procurar documentos (API da Pesquisa do Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

A Pesquisa do Azure simplifica a construção do intervalo, fornecendo duas abordagens para a computação de um intervalo. Para ambas as abordagens, a Pesquisa do Azure cria os intervalos apropriados, considerando as entradas que você forneceu. Por exemplo, se você especificar valores de intervalo de 10|20|30, ele criará automaticamente intervalos de 0 -10, 10-20 e 20-30. O aplicativo de exemplo remove quaisquer intervalos que estejam vazios. 

**Abordagem 1: usar o parâmetro de intervalo**<br/>
Para definir facetas de preço em incrementos de US$ 10, você deve especificar: `&facet=price,interval:10`

**Abordagem 2: usar uma lista de valores**<br/>
 Para dados numéricos, você pode usar uma lista de valores.  Considere o intervalo da faceta para listPrice, renderizada como descrito a seguir:

  ![][5]

O intervalo é especificado no arquivo **CatalogSearch.cs** usando uma lista de valores:

    facet=listPrice,values:10|25|100|500|1000|2500

Cada intervalo é criado usando 0 como ponto de partida, um valor da lista como um ponto de extremidade e então recortado do intervalo anterior para criar intervalos discretos. A Pesquisa do Azure fará isso como parte da navegação facetada. Você não precisa escrever código para estruturar cada intervalo.

### <a name="build-a-filter-for-facet-ranges"></a>Compilar um filtro para intervalos de faceta
Para filtrar os documentos com base em um intervalo selecionado pelo usuário, você pode usar os operadores de filtragem `"ge"` e `"lt"` em uma expressão de duas partes que defina os pontos de extremidade do intervalo. Por exemplo, se o usuário escolher o intervalo 10-25, o filtro será `$filter=listPrice ge 10 and listPrice lt 25`.

No aplicativo de exemplo, a expressão de filtro usa os parâmetros **priceFrom** e **priceTo** para definir os pontos de extremidade. O método **BuildFilter** em **CatalogSearch.cs** contém a expressão de filtragem que fornece a você os documentos dentro de um intervalo.

  ![][6]

<a name="geofacets"></a> 

## <a name="filtered-navigation-based-on-geopoints"></a>Navegação filtrada com base em GeoPoints
É comum ver filtros que ajudam você a escolher uma loja, restaurante ou destino com base na proximidade desse local com relação ao local onde você está atualmente. Embora esse tipo de filtro possa parecer com a navegação facetada, trata-se na verdade apenas de um filtro. Mencionamos isso aqui para aqueles que estão procurando especificamente conselhos de implementação para um problema de design específico.

Há duas funções geoespaciais no Azure Search, **geo.distance** e **geo.intersects**.

* A função **geo.distance** retorna a distância em quilômetros entre dois pontos, um deles sendo um campo e o outro uma constante passada como parte do filtro. 
* A função **geo.intersects** retorna “true” se um determinado ponto está dentro de um polígono determinado, em que o ponto é um campo e o polígono é especificado como uma lista constante de coordenadas passadas como parte do filtro.

Você pode encontrar exemplos de filtros em [Sintaxe de expressão OData (Pesquisa do Azure)](http://msdn.microsoft.com/library/azure/dn798921.aspx).

<a name="tryitout"></a>

## <a name="try-it-out"></a>Experimentar
Demonstração do Adventure Works da Pesquisa do Azure em Codeplex contém os exemplos mencionados neste artigo. Ao trabalhar com os resultados da pesquisa, monitore a URL quanto a alterações na construção de consulta. Esse aplicativo acrescenta facetas ao URI conforme você seleciona cada uma.

1. Configure o aplicativo de exemplo para usar a URL de serviço e a chave de api. 
   
   Observe o esquema definido no arquivo Program.cs do projeto CatalogIndexer. Ele especifica os campos facetáveis para cor, listPrice, tamanho, peso, categoryName e modelName.  Apenas alguns deles (cor, listPrice e categoryName) são realmente implementados na navegação facetada.
2. Execute o aplicativo. 
   
   A princípio, a caixa de Pesquisa está visível. Você pode clicar no botão Pesquisar imediatamente para obter todos os resultados, ou digitar um termo de pesquisa.
   
   ![][7]
3. Insira um termo de pesquisa como “bicicleta” e clique em **Pesquisar**. A consulta é executada rapidamente.
   
   Uma estrutura de navegação facetada também é retornada com os resultados da pesquisa.  Na URL, facetas para Cores, Categorias e Preços são nulas. Na página de resultados de pesquisa, a estrutura de navegação facetada inclui contagens para cada resultado da faceta.
   
   ![][8]
4. Clique em uma cor, categoria e a faixa de preço. As facetas são nulas em uma pesquisa inicial, mas já que elas recebem valores, os resultados da pesquisa são recortados eliminando-se os itens que não correspondem mais. Observe que o URI seleciona as alterações à sua consulta.
   
   ![][9]
5. Para limpar a consulta facetada para que você possa tentar comportamentos de consulta diferentes, clique em **Catálogo AdventureWorks** na parte superior da página.
   
   ![][10]

<a name="nextstep"></a>

## <a name="next-step"></a>Próxima etapa
Para testar seu conhecimento, você pode adicionar um campo de faceta para *modelName*. O índice já está definido para esta faceta, portanto não é necessária nenhuma alteração ao índice. Mas você precisará modificar o HTML para incluir uma nova faceta para Modelos, ou adicionar o campo de faceta para o construtor de consultas.

Para obter mais informações sobre princípios de design para navegação facetada, recomendamos os seguintes links:

* [Design para pesquisa facetada](http://www.uie.com/articles/faceted_search/)
* [Padrões de design: navegação facetada](http://alistapart.com/article/design-patterns-faceted-navigation)

Você também pode assistir [Aprofundamento na Pesquisa do Azure](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Em 45:25, há uma demonstração de como implementar facetas.

<!--Anchors-->
[Como criá-la]: #howtobuildit
[Criar a camada de apresentação]: #presentationlayer
[Compilar o índice]: #buildindex
[Verificar a qualidade de dados]: #checkdata
[Compilar a consulta]: #buildquery
[Dicas sobre como controlar a navegação facetada]: #tips
[Navegação mista com base em valores de intervalo]: #rangefacets
[Navegação mista com base em GeoPoints]: #geofacets
[Experimentar]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Design para pesquisa facetada]: http://www.uie.com/articles/faceted_search/
[Padrões de design: navegação facetada]: http://alistapart.com/article/design-patterns-faceted-navigation
[Criar seu primeiro aplicativo]: search-create-first-solution.md
[Sintaxe de expressão OData (Pesquisa do Azure)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Demonstração do Adventure Works do Azure Search]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Facetas na postagem do Fórum do Azure Search]: ../faceting-on-azure-search.md?forum=azuresearch
[Pesquisar documentos (API da Pesquisa do Azure)]: http://msdn.microsoft.com/library/azure/dn798927.aspx




<!--HONumber=Nov16_HO3-->


