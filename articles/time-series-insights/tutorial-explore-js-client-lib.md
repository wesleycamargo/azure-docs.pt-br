---
title: Explorar a biblioteca de cliente Time Series Insights JavaScript
description: Saiba mais sobre a biblioteca de cliente Time Series Insights JavaScript e o modelo de programação relacionado.
documentationcenter: ''
services: time-series-insights
author: BryanLa
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: time-series-insights
ms.workload: na
ms.tgt_pltfrm: ''
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2018
ms.author: bryanla
ms.openlocfilehash: 5b845f36dbb65b38d0e4ac2a118277027239b3d6
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-explore-the-time-series-insights-javascript-client-library"></a>Tutorial: explorar a biblioteca de cliente Time Series Insights JavaScript

Para ajudar a consulta de desenvolvedores e visualizar dados armazenados no Time Series Insights (TSI), desenvolvemos uma biblioteca de controle baseado em JavaScript D3 que facilita o trabalho. Usando um aplicativo da web de exemplo, este tutorial irá guiá-lo por meio de uma exploração da biblioteca de cliente TSI JavaScript e o modelo de programação relacionado. 

Os tópicos abordados fornecem oportunidades para experimentar, obter um entendimento mais profundo de como acessar dados TSI e usar os controles de gráfico para renderizar e visualizar dados. O objetivo é fornecer detalhes suficientes, que você pode usar a biblioteca em seu próprio aplicativo web.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Aplicativo de exemplo TSI 
> * Biblioteca de clientes TSI JavaScript
> * Como o aplicativo de exemplo usa a biblioteca para visualizar dados TSI

## <a name="prerequisites"></a>pré-requisitos

Este tutorial usa o recurso de "Ferramentas para Desenvolvedores” (também conhecida como DevTools ou F12) encontrado na maioria dos navegadores da web, como [Borda](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)e outros. Se você não ainda estiver familiarizado, talvez queira explorar esse recurso em seu navegador antes de continuar. 

## <a name="the-time-series-insights-sample-application"></a>Aplicativo de exemplo Time Series Insights 

Em todo este tutorial, o aplicativo de exemplo de Insights de série de tempo é usado para explorar o código-fonte do aplicativo, incluindo o uso da biblioteca de cliente TSI JavaScript. O aplicativo é uma aplicativo de página única (SPA), mostrando o uso da biblioteca para consultar e visualizar os dados de um ambiente de exemplo de TSI. 

1. Navegue até o [Aplicativo de exemplo Time Series Insights](https://insights.timeseries.azure.com/clientsample). Você verá uma página semelhante à seguinte, solicitando que você entre: ![prompt de entrada do cliente de exemplo TSI](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Clique no botão "login" e insira ou selecione suas credenciais. Você pode usar uma conta de empresa/organização (Azure Active Directory) ou uma conta pessoal (conta  da Microsoft ou MSA). 

   ![Prompt de credenciais do cliente de exemplo TSI](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. Depois da entrada bem-sucedida, você verá uma página semelhante à seguinte, contendo vários estilos de exemplos de gráficos, preenchido com dados TSI. Observe também a sua conta de usuário e o link "fazer logoff" no canto superior direito: ![página principal do cliente de exemplo TSI após entrar](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Estrutura e fonte da página

Primeiro vamos visualizar o código-fonte HTML e JavaScript por trás da página que é renderizada no navegador. Não vemos todos os elementos, mas você pode aprender sobre as principais seções com uma ideia de como funciona a página:

1. Abra "Ferramentas para Desenvolvedores" em seu navegador e inspecione os elementos HTML que compõem a página atual, também conhecidos como árvore DOM ou de HTML.

2. Expanda os elementos `<head>` e `<body>` elementos e observe as seguintes seções:
   - Em `<head>`, localize elementos que extraem arquivos adicionais para ajudar no funcionamento da página:
     - um `<script>` elemento para referenciar a biblioteca Azure Active Directory Authentication (adal.min.js) - também conhecido como ADAL, ou seja, uma biblioteca de JavaScript que fornece autenticação OAuth 2.0 (entrada) e aquisição de token para acessar APIs:
     - `<link>` elementos de folhas de estilo (sampleStyles.css, tsiclient.css) - também conhecidos como CSS, são usados para controlar os detalhes de estilo visual de página, como cores, fontes, espaçamento, etc. 
     - um `<script>` elemento para referenciar a biblioteca de cliente TSI (tsiclient.js) - uma biblioteca JavaScript usada pela página para chamar APIs do serviço TSI e renderizar controles de gráfico na página.

     >[!NOTE]
     > O código-fonte para a biblioteca JavaScript ADAL está disponível no [repositório azure-activedirectory-library-for-js repository](https://github.com/AzureAD/azure-activedirectory-library-for-js).  
     > O código-fonte para a biblioteca TSI cliente JavaScript está disponível no repositório [tsiclient](https://github.com/Microsoft/tsiclient).

   - Em `<body>`, encontrar `<div>` elementos, que atuam como contêineres para definir o layout de itens na página e outro `<script>` elemento:
     - a primeiro `<div>` especifica a caixa de diálogo "Fazer logon" (`id="loginModal"`).
     - o segundo `<div>` atua como um pai para:
       - um cabeçalho `<div>`, usado para mensagens de status e informações de entrada na parte superior da página (`class="header"`).
       - um `<div>` para o restante dos elementos do corpo da página, incluindo todos os gráficos (`class="chartsWrapper"`).
       - uma seção `<script>` que contém todos os do JavaScript usado para controlar a página.

   [![Exemplo de cliente TSI com DevTools](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. Expanda o `<div class="chartsWrapper">` elemento e descubra mais elementos filho `<div>`, usados para posicionar cada exemplo de controle de gráfico. Observe que há vários pares de `<div>` elementos, um para cada exemplo de gráfico:
   - O primeiro (`class="rowOfCardsTitle"`) contém um título descritivo para resumir o que ilustra os gráficos. Por exemplo: "Gráficos de linha estática com legendas em tamanho natural"
   - O segundo (`class="rowOfCards"`) é um pai, que contém os elementos filho `<div>` que posicionam o controle de gráfico real em uma linha. 

  ![Exibindo o corpo "divs"](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. Expanda agora o `<script type="text/javascript">` elemento, diretamente abaixo do `<div class="chartsWrapper">` elemento. Você verá o início da seção de JavaScript em nível de página, usado para lidar com toda a lógica da página para tarefas como autenticação, chamando APIs do serviço TSI, renderização de controles de gráfico e muito mais:

  ![Visualização do script de corpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-client-javascript-library-concepts"></a>Conceitos de biblioteca JavaScript de Cliente TSI

Embora nós não iremos revisar em detalhes, basicamente a biblioteca de cliente TSI (tsclient.js) fornece uma abstração para duas categorias importantes:

- **Métodos de wrapper para chamar as APIs de consulta TSI** -APIs REST que permitem consultar dados TSI usando expressões de agregação e estão organizados em `TsiClient.Server` namespace da biblioteca. 
- **Métodos para criar e preencher os vários tipos de controles de gráfico** - usado para renderizar os dados agregados TSI em uma página da web e estão organizados em `TsiClient.UX` namespace da biblioteca. 

Os conceitos a seguir são aplicáveis e universais para a APIS da biblioteca de cliente TSI m geral. 

### <a name="authentication"></a>Autenticação

Como mencionado anteriormente, este exemplo é um aplicativo de página única e usa o suporte do OAuth 2.0 em ADAL para autenticação do usuário. Aqui estão alguns pontos de interesse nesta seção do script:

1. Usar o ADAL para autenticação requer que o aplicativo do cliente se registre no registro de aplicativo do Azure do aplicativo Microsoft Azure Active Directory (Azure AD). Como um SPA, este aplicativo está registrado para usar o fluxo de concessão de autorização OAuth 2.0 "implícito". De forma correspondente, o aplicativo especifica algumas das propriedades de registro em tempo de execução, como o GUID da ID do cliente (`clientId`) e o URI de redirecionamento (`postLogoutRedirectUri`), para participar do fluxo.

2. Posteriormente, o aplicativo solicita um “token de acesso" Azure Active Directory O token de acesso é emitido para um conjunto finito de permissões para um identificador de API/serviço específico (https://insights.timeseries.azure.com), também conhecido como o “público” do token. As permissões de token são emitidas em nome do usuário conectado. O identificador para o serviço/API ainda é outra propriedade contida no registro do Microsoft Active Directory do aplicativo. Depois que o ADAL retorna o token de acesso para o aplicativo, ele é passado como um “token de portador” quando acessar o APIS de serviço TSI. 

   [!code-javascript[head-sample](~/samples-javascript/pages/index.html?range=140-199&highlight=4-9,36-39)]

### <a name="control-identification"></a>Identificação de controle

Como discutido anteriormente, os `<div>` elementos dentro de `<body>` fornecem o layout para todos os controles de gráfico demonstrados na página. Cada um deles especifica propriedades para o posicionamento e os atributos visual do controle do gráfico, incluindo uma `id` propriedade. A `id` propriedade fornece um identificador exclusivo, que é usado no código JavaScript para identificar e associar a cada controle de renderização e atualização. 

### <a name="aggregate-expressions"></a>Expressões de agregação

As APIs da biblioteca de cliente TSI tornam pesadas o uso expressões de agregação. Uma expressão de agregação oferece a capacidade de construir um ou mais "termos de pesquisa." As APIs são semelhantes à forma como o [explorador Azure Time Series Insights](https://insights.timeseries.azure.com/demo) usa extensão de pesquisa, em que prevê mede e divide por valor. A maioria das APIs da biblioteca leva a uma matriz de expressões de agregação, que são usadas pelo serviço para criar uma consulta de dados TSI.

### <a name="call-pattern"></a>Padrão de chamada

Preenchimento e renderização de controle de gráfico seguem um padrão geral. Você encontra esse padrão usado em toda a página JavaScript, que instancia e carrega os controle do Aplicativo de Exemplo TSI:

1. Declara uma matriz para manter uma ou mais expressões de agregação TSI.  

   ```javascript
   var aes =  [];
   ```

2. Agregar 1 para objetos de expressão de agregação n e adicioná-los à matriz de expressão de agregação.  

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```
   **parâmetros de aggregateExpression**

   | Parâmetro | DESCRIÇÃO | Exemplo |
   | --------- | ----------- | ------- |
   | predicateObject | Expressão de filtragem de dados. |`{predicateString: "Factory = 'Factory3'"}` |
   | measureObject   | O nome da propriedade da medida que está sendo usada. | `{property: 'Temperature', type: "Double"}` |
   | measureTypes    | Agregações desejadas da propriedade medida. | `['avg', 'min']` |
   | searchSpan      | O tamanho de intervalo e a duração da expressão de agregação. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | splitByObject   | A propriedade de cadeia de caracteres que você deseja dividir (opcional – pode ser nulo). | `{property: 'Station', type: 'String'}` |
   | cor           | A cor dos objetos que você deseja processar. | `'pink'` |
   | alias           | Um nome fácil para a expressão de agregação. | `'Factory3Temperature'` |
   | contextMenuActions | Uma matriz de ações a ser associada aos objetos de série de tempo em uma visualização (opcional). | Consulte [menus de contexto pop-up na seção de recursos avançados.](#popup-context-menus) |

3. Chame uma consulta TSI usando `TsiClient.Server` APIs para solicitar os dados de agregação.  

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```
   **parâmetros getAggregates**

   | Parâmetro | DESCRIÇÃO | Exemplo |
   | --------- | ----------- | ------- |
   | token     | Token de acesso para a API TSI | `authContext.getTsiToken()` Consulte [a seção autenticação.](#authentication) |
   | envFQDN     | Use um nome de domínio totalmente qualificado para o ambiente TSI | No Portal do Azure, por exemplo `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com` |
   | aeTsxArray | Matriz de expressões de consulta TSI | Use a `aes` variável, conforme descrito anteriormente: `aes.map(function(ae){return ae.toTsx()}` |

4. Transforme o resultado compactado da consulta TSI, em JSON para visualização.

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Crie um controle de gráfico usando `TsiClient.UX` APIs e associe-o a um da `<div>` elementos na página.

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Preencha o controle de gráfico com os objetos de dados JSON transformados e processá-os na página.

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Controles de renderização

A biblioteca atualmente expõe oito controles de análise exclusivos. Incluem um gráfico de linhas, gráfico de pizza, gráfico de barras, mapa de calor, controles de hierarquia, uma grade acessível, cronogramas de evento distintos e linhas de tempo de transição de estado.   

### <a name="line-bar-pie-chart-examples"></a>Exemplos de gráfico de linha, barras e de pizza 

Primeiro, vamos examinar o código por trás de alguns controles de gráfico padrão demonstrados no aplicativo e os modelo/padrões de programação para criá-los. Especificamente, examine a seção de HTML no comentário `// Example 3/4/5`, que processa os controles com valores de ID `chart3`, `chart4`, e `chart5`. 

Lembre-se da etapa 3 na página [seção de origem e a estrutura de página](#page-source-and-structure), os controles de gráfico são organizados em linhas na página, cada qual com uma linha de título descritivo. Neste exemplo, os três gráficos sendo preenchidos estão sob o título "Vários tipos de gráfico dos mesmos dados" `<div>`, vinculados a três `<div>` elementos abaixo dele:

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/index.html?range=60-74&highlight=1,5,9,13)]

A seção de código JavaScript a seguir usa o padrão descrito anteriormente, para criar expressões de agregação TSI, use os para consultar dados TSI e renderizar os três gráficos. Observe os três tipos usados no `tsiClient.ux` namespace, `LineChart`, `BarChart`, `PieChart`, para criar e renderizar os respectivos gráficos. Observe também que todos os três gráficos podem usar os mesmos dados de expressão de agregação, `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/index.html?range=236-257&highlight=13-14,16-17,19-20)]

Os três gráficos da seguinte maneira aparecem quando renderizados:

[![Vários tipos de gráfico dos mesmos dados](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Recursos avançados

A biblioteca também apresenta alguns recursos avançados opcionais que talvez você queira aproveitar.  

### <a name="states-and-events"></a>Estados e eventos

Um exemplo da funcionalidade avançada fornecida é a capacidade de adicionar transações de estado e eventos discretos aos gráficos. Esse recurso é útil para realçar a incidentes, alertas, e opções de estado e o estado muda como ligar/desligar. 

Aqui você vê o código por trás da seção de HTML no comentário `// Example 10`. O código renderiza um controle de linha no título "Gráficos de linha com vários tipos de série", associando-o ao `<div>` valor de ID `chart10`:

1. Primeiro uma estrutura denominada `events4` é definida para manter os elementos de alteração de estado a ser rastreado. Ele contém:
   - Uma chave de cadeia de caracteres chamada `"Component States"` 
   - Uma matriz de objetos de valor que representam os estados, cada uma deles inclui:
     - Uma chave de cadeia de caracteres que contém um carimbo de data/hora ISO JavaScript
     - Uma matriz que contém características do estado
       - uma cor
       - uma descrição

2. Em seguida, a `events5` estrutura é definida para `"Incidents"`, que contém uma matriz de elementos de evento a ser rastreada. A estrutura de matriz é a mesma forma, conforme o descrito para `events4`. 

3. Finalmente, o gráfico de linhas é processado, passando as duas estruturas com o gráfico de parâmetros de opções: `events:` e `states:`. Observe os outros parâmetros de opção, para especificar um `tooltip:`, `theme:` ou `grid:`. 

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/index.html?range=332-384&highlight=5,26,51)]

Visualmente, marcadores/pop-ups de losango são usados para indicar incidentes e as barras/popups coloridos ao longo da escala de tempo indicam as mudanças de estado:

[![Gráficos de linha com vários tipos de série](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="popup-context-menus"></a>Menus de contexto popup

Outro exemplo de recursos avançados são menus de contexto personalizado (clique com o botão direito do mouse nos menus de popup), que são úteis para habilitar ações e as próximas etapas lógicas dentro do escopo do seu aplicativo.

Aqui vamos examinar o código HTML em `// Example 13/14/15`. Esse código inicialmente renderiza um gráfico de de linhas com o título “Gráfico de linhas com Menu de contexto para criar gráficos de pizza/barra”, vinculado `<div>` elemento com valor de ID `chart13`. Usando os menus de contexto, o gráfico de linhas fornece a capacidade de criar dinamicamente um gráfico de piza e barra associado aos `<div>` elementos com IDs `chart14` e `chart15`. Além disso, gráficos de barras e pizza também usam menus de contexto para habilitar seus próprios recursos: a capacidade de copiar dados de gráfico de pizza para barra e imprimir os dados de gráfico de barras para a janela de console do navegador, respectivamente.

1. Primeiro, uma série de ações personalizadas são definidas. Cada uma contém uma matriz com um ou mais elementos, onde cada elemento define um item de menu de contexto único: 
   - `barChartActions`: define o menu de contexto para o gráfico de pizza, que contém um elemento para definir um único item:
     - `name`: o texto usado para o item de menu: "Imprimir parâmetros para o console"
     - `action`: a ação associada ao item de menu, que é sempre uma função anônima que usa três argumentos com base na expressão de agregação usada para criar o gráfico. Nesse caso, eles são gravados na janela de console do navegador:
       - `ae`: a matriz de expressão de agregação
       - `splitBy`: o valor de splitBy
       - `timestamp`: carimbo de data/hora UTC
   - `pieChartActions`: define o menu de contexto para o gráfico de barra, que contém um elemento para definir um único item: A forma e o esquema são os mesmos que os anteriores `barChartActions`, mas observe que a `action` função é drasticamente diferente, pois cria e processa o gráfico de barras. Observe também que usa o `ae` argumento para especificar a matriz de expressão de agregação, passado em tempo de execução durante a janela pop-up do item de menu. A função também define a `ae.contextMenu` propriedade com o `barChartActions` menu de contexto.
   - `contextMenuActions`: define o menu de contexto para o gráfico de linha, que contém três elementos para definir três itens de menu. A forma e o esquema para cada elemento é o mesmo que os anteriores. Assim como `barChartActions`, o primeiro item grava os argumentos da três funções à janela do navegador do console. Semelhante a `pieChartActions`, os dois itens instanciam e renderizam o gráfico de pizza e gráficos de barras, respectivamente. Os dois itens também definem suas `ae.contextMenu` propriedades com o `pieChartActions` e `barChartActions` menus de contexto, respectivamente.

2. Em seguida, duas expressões de agregação são enviadas por push para o `aes` matriz de expressão de agregação, especificando a `contextMenuActions` matriz para cada. São usados com o controle de gráfico de linha.

3. Por fim, o gráfico de linhas é inicialmente renderizado, do qual a pizza e um gráfico de barras podem ser renderizados em tempo de execução.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/index.html?range=456-535&highlight=7,16,29,61-64,78)]

A captura de tela mostra os gráficos, com seus menus de contexto pop-up respectivos. O gráfico de pizza e gráficos de barras foram criados dinamicamente, usando as opções de menu de contexto de gráfico de linha:

[![Gráfico de linha com o Menu de contexto para criar o gráfico de pizza/barra](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pincéis

Os pincéis podem ser usados para definir o escopo de um intervalo de tempo para definir ações, como ampliar e explorar. 

O código usado para ilustrar pincéis também é mostrado no exemplo anterior de “Gráfico de linha com o menu Contexto para criar gráfico de pizza/barra", que abrange [menus de contexto pop-up](#popup-context-menus-section). 

1. Ações de pincel são muito semelhantes a um menu de contexto, definindo uma série de ações personalizadas para o pincel. Cada uma contém uma matriz com um ou mais elementos, onde cada elemento define um item de menu de contexto único:
   - `name`: o texto usado para o item de menu: "Imprimir parâmetros para o console"
   - `action`: a ação associada ao item de menu, que é sempre uma função anônima que leva dois argumentos. Nesse caso, eles são gravados na janela de console do navegador:
      - `fromTime`: carimbo de data/hora “de” da seleção do pincel
      - `toTime`: carimbo de data/hora “para” da seleção do pincel

2. Ações de pincel são adicionadas como outra propriedade de opção de gráfico. Observe que a `brushContextMenuActions: brushActions` propriedade que está sendo passada para a `linechart.Render` chamada.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/index.html?range=521-535&highlight=1,13)]

![Gráfico de linha com o Menu de contexto para criar o gráfico de pizza/barra usando pincéis](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Entrar e explorar o aplicativo de exemplo TSI e sua origem
> * Use APIS da Biblioteca de clientes TSI JavaScript
> * Usar JavaScript para criar e preencher os controles de gráfico com dados TSI

Como discutido, o aplicativo de exemplo TSI usa um conjunto de dados de demonstração. Para saber mais sobre como você pode criar seu próprio ambiente TSI e o conjunto de dados, vá para o seguinte artigo:

> [!div class="nextstepaction"]
> [Planejar o ambiente do Azure Time Series Insights](time-series-insights-environment-planning.md)


