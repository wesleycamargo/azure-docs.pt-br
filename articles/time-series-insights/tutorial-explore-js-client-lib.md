---
title: 'Tutorial: Explorar a biblioteca de clientes JavaScript do Azure Time Series Insights | Microsoft Docs'
description: Saiba mais sobre a biblioteca de cliente JavaScript do Azure Time Series Insights e seu respectivo modelo de programação.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 8ed3213a40370b1ab2beb15a989a22017b058d65
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812065"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Tutorial: Explorar a biblioteca de cliente JavaScript do Azure Time Series Insights

Para ajudar a consulta de desenvolvedores Web e visualizar dados armazenados no Time Series Insights (TSI), foi desenvolvida a biblioteca de cliente do TSI baseada em JavaScript D3.  Usando um aplicativo Web de exemplo, este tutorial mostrará em detalhes a biblioteca de cliente do TSI e seu respectivo modelo de programação.

Os tópicos neste tutorial permitem que você experimente a biblioteca, entenda como acessar dados do TSI e use os controles de gráfico para renderizar e visualizar dados. O objetivo é fornecer detalhes suficientes para que você possa usar a biblioteca em seu próprio aplicativo Web.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * O aplicativo de exemplo do TSI.
> * A biblioteca de cliente JavaScript do TSI.
> * Como o aplicativo de exemplo usa a biblioteca para visualizar dados do TSI.

## <a name="video"></a>Vídeo: 

### <a name="in-this-video-we-introduce-the-open-source-time-series-insights-javascript-sdkbr"></a>Neste vídeo, apresentamos o SDK do JavaScript do Time Series Insights de software livre.</br>

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial usa o recurso de "Ferramentas para Desenvolvedores" (também conhecido como DevTools ou F12) encontrado na maioria dos navegadores da Web, como [Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/) e outros. Se você não ainda estiver familiarizado com este recurso, talvez queira explorá-lo em seu navegador antes de continuar.

## <a name="time-series-insights-sample-application"></a>Aplicativo de exemplo do Time Series Insights

Em todo este tutorial, o aplicativo de exemplo do Time Series Insights é usado para explorar o código-fonte por trás do aplicativo, incluindo o uso da biblioteca de cliente JavaScript do TSI. O exemplo é um aplicativo Web de página única (SPA) que demonstra como usar a biblioteca. O exemplo mostra como consultar e visualizar os dados de um ambiente do TSI de exemplo.

1. Navegue até o [aplicativo de exemplo do Time Series Insights](https://insights.timeseries.azure.com/clientsample). Você verá uma página semelhante à imagem a seguir com uma solicitação para entrar:

   ![Solicitação de entrada do exemplo de Cliente TSI](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Selecione **Entrar** e insira ou selecione suas credenciais. Use uma conta de empresa/organização (Azure Active Directory) ou uma conta pessoal (conta da Microsoft ou MSA).

   ![Solicitação de credenciais de exemplo do Cliente TSI](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. Após entrar com êxito, você verá uma página semelhante à imagem a seguir. A página mostra vários estilos de gráficos de exemplo que são preenchidos com os dados do TSI. Sua conta de usuário e a opção **Logoff** são visíveis no canto superior direito:

   ![Página principal de exemplo do Cliente TSI após entrar](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Estrutura e fonte da página

Primeiro, vamos visualizar o código-fonte HTML e JavaScript por trás da página que é renderizada no navegador. Não abordaremos todos os elementos, mas você pode conhecer as principais seções e obter uma ideia de como funciona a página:

1. Abra **Ferramentas para Desenvolvedores** em seu navegador. Inspecione os elementos HTML que compõem a página atual (também conhecidos como árvore DOM ou de HTML).

2. Expanda os elementos `<head>` e `<body>` elementos e observe as seguintes seções:

   - No elemento `<head>`, você encontrará os elementos que extraem arquivos adicionais para ajudar no funcionamento da página:
     - Um elemento `<script>` que é usado para referenciar a biblioteca de autenticação do Azure Active Directory **adal.min.js** (também conhecido como ADAL). ADAL é uma biblioteca do JavaScript que fornece autenticação OAuth 2.0 (entrada) e aquisição de token para acessar APIs.
     - Vários `<link>` elementos para folhas de estilos (também conhecidos como CSS) como **sampleStyles.css** e **tsiclient.css**. As folhas de estilo são usadas para controlar os detalhes de estilo visual da página, como cores, fontes, espaçamento e assim por diante.
     - Um `<script>` elemento que é usado para fazer referência à biblioteca JavaScript do Cliente TSI **tsiclient.js**. A biblioteca é usada pela página para chamar as APIs do serviço TSI e renderizar controles de gráfico na página.

     >[!NOTE]
     > O código-fonte para a biblioteca JavaScript ADAL está disponível no [repositório azure-activedirectory-library-for-js repository](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > O código-fonte para a biblioteca TSI cliente JavaScript está disponível no repositório [tsiclient](https://github.com/Microsoft/tsiclient).

   - No elemento `<body>`, você encontrará os elementos `<div>`, que atuam como contêineres para definir o layout dos itens na página e outro elemento `<script>`:
     - O primeiro elemento `<div>` especifica a caixa de diálogo **Entrar** (`id="loginModal"`).
     - O segundo elemento `<div>` atua como um pai para:
       - Um cabeçalho do elemento `<div>`, usado para mensagens de status e informações de entrada na parte superior da página (`class="header"`).
       - Um elemento `<div>` para o restante dos elementos do corpo da página, incluindo todos os gráficos (`class="chartsWrapper"`).
       - Uma seção `<script>` que contém todos os elementos do JavaScript usados para controlar a página.

   [![Exemplo de Cliente TSI com as Ferramentas para Desenvolvedores](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. Expanda o elemento `<div class="chartsWrapper">` para descobrir mais elementos filho `<div>`. Esses elementos são usados para posicionar cada exemplo de controle de gráfico. Observe que há vários pares de elementos `<div>`, um para cada exemplo de gráfico:

   - O primeiro elemento (`class="rowOfCardsTitle"`) contém um título descritivo para resumir o que ilustra os gráficos. Por exemplo:  “Gráficos de linhas estáticas com legendas em tamanho natural”.
   - O segundo elemento (`class="rowOfCards"`) é um pai, que contém os elementos filho `<div>` que posicionam o controle de gráfico real em uma linha.

   ![Elementos do corpo div](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. Agora, expanda o elemento `<script type="text/javascript">` logo abaixo do elemento `<div class="chartsWrapper">`. Observe o início da seção do JavaScript de nível de página, usada para lidar com toda a lógica da página: autenticação, chamada de APIs do serviço TSI, renderização de controles de gráfico e muito mais:

   ![Script de corpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-javascript-client-library-concepts"></a>Conceitos de biblioteca de cliente JavaScript do TSI

Embora não a revisemos em detalhes, a biblioteca de cliente TSI **tsclient.js** fornece, basicamente, uma abstração para duas categorias importantes:

- **Métodos de wrapper para chamar as APIs de consulta TSI**: APIs REST que permitem consultar dados TSI usando expressões de agregação. Os métodos estão organizados no namespace `TsiClient.Server` da biblioteca.
- **Métodos para criar e popular os vários tipos de controles de criação de gráficos**: métodos que são usados para renderizar o TSI agregam dados em uma página da Web. Os métodos estão organizados no namespace `TsiClient.UX` da biblioteca.

Os conceitos a seguir são aplicáveis e universais para a APIS da biblioteca de cliente TSI m geral.

### <a name="authentication"></a>Authentication

Como mencionado anteriormente, este exemplo é um SPA que usa o suporte do OAuth 2.0 em ADAL para autenticação do usuário. Aqui estão alguns pontos de interesse nesta seção do script:

1. Ao usar o ADAL para autenticação, o aplicativo do cliente deve se registrar no registro de aplicativo do Azure Active Directory (Azure AD). Como um SPA, este aplicativo está registrado para usar o fluxo de concessão de autorização OAuth 2.0 "implícito". De forma correspondente, o aplicativo especifica algumas das propriedades de registro em tempo de execução, como o GUID da ID do cliente (`clientId`) e o URI de redirecionamento (`postLogoutRedirectUri`), para participar do fluxo.

2. Posteriormente, o aplicativo solicita um “token de acesso" Azure Active Directory O token de acesso é emitido para um conjunto finito de permissões para um identificador de API/serviço específico https://api.timeseries.azure.com. O identificador de API/serviço também é conhecido como o token "público". As permissões de token são emitidas em nome do usuário conectado. O identificador para o serviço/API ainda é outra propriedade contida no registro do Azure AD do aplicativo. Depois que o ADAL retorna o token de acesso para o aplicativo, ele é passado como um “token de portador” quando acessar o APIS de serviço TSI.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=145-204&highlight=4-9,36-39)]

### <a name="control-identification"></a>Identificação de controle

Como discutido anteriormente, os elementos `<div>` dentro do elemento `<body>` fornecem o layout para todos os controles de gráfico demonstrados na página. Cada elemento `<div>` especifica as propriedades para o posicionamento e os atributos visuais do controle do gráfico, incluindo uma propriedade `id`. A propriedade `id` fornece um identificador exclusivo que é usado no código JavaScript para identificar e associar cada controle de renderização e atualização.

### <a name="aggregate-expressions"></a>Expressões de agregação

As APIs da biblioteca de cliente TSI tornam pesadas o uso expressões de agregação. Uma expressão de agregação oferece a capacidade de construir um ou mais "termos de pesquisa." As APIs são projetadas de forma semelhante à forma como o [explorador do Azure Time Series Insights](https://insights.timeseries.azure.com/demo), que usa extensão de pesquisa, em que prevê mede e divide por valor. A maioria das APIs da biblioteca usa uma matriz de expressões de agregação que são usadas pelo serviço para criar uma consulta de dados TSI.

### <a name="call-pattern"></a>Padrão de chamada

O preenchimento e a renderização de controles de gráfico seguem um padrão geral. Você encontra esse padrão usado em toda a página JavaScript, que instancia e carrega os controles do aplicativo de exemplo TSI:

1. Declare uma `array` para manter uma ou mais expressões de agregação TSI:

   ```javascript
   var aes =  [];
   ```

2. Compile 1 a n objetos de expressão de agregação e adicione-os à matriz de expressão de agregação:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **parâmetros de aggregateExpression**

   | Parâmetro | DESCRIÇÃO | Exemplo |
   | --------- | ----------- | ------- |
   | `predicateObject` | Expressão de filtragem de dados. |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | O nome da propriedade da medida que está sendo usada. | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | As agregações desejadas da propriedade de medida. | `['avg', 'min']` |
   | `searchSpan`      | O tamanho de intervalo e a duração da expressão de agregação. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | A propriedade de cadeia de caracteres que você deseja dividir (opcional – pode ser nulo). | `{property: 'Station', type: 'String'}` |
   | `color`         | A cor dos objetos que você deseja renderizar. | `'pink'` |
   | `alias`           | Um nome fácil para a expressão de agregação. | `'Factory3Temperature'` |
   | `contextMenuActions` | Uma matriz de ações a ser associada aos objetos de série de tempo em uma visualização (opcional). | Para obter mais informações, confira Menus de contexto pop-up na seção Recursos avançados. |

3. Chame uma consulta TSI usando as APIs `TsiClient.Server` para solicitar os dados de agregação:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **parâmetros getAggregates**

   | Parâmetro | DESCRIÇÃO | Exemplo |
   | --------- | ----------- | ------- |
   | `token`     | O token de acesso para a API TSI. |  `authContext.getTsiToken()` Para mais informações, consulte a [seção de autenticação.](#authentication) |
   | `envFQDN`   | O nome de domínio totalmente qualificado (FQDN) para o ambiente TSI. | No portal do Azure, por exemplo: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Uma matriz de expressões de consulta TSI. | Use a variável `aes`, conforme descrito anteriormente: `aes.map(function(ae){return ae.toTsx()}`. |

4. Transforme o resultado compactado que é retornado da consulta TSI em JSON para visualização:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Crie um controle de gráfico usando as APIs `TsiClient.UX` e associe-o a um dos elementos `<div>` na página:

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Preencha o controle de gráfico com os objetos de dados JSON renderize o controle na página:

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Controles de renderização

A biblioteca do Cliente TSI atualmente expõe oito controles de análise únicos: gráfico de linhas, gráfico de pizza, gráfico de barras, mapa de calor, controles de hierarquia, uma grade acessível, cronogramas de evento distintos e linhas de tempo de transição de estado.

### <a name="line-bar-pie-chart-examples"></a>Exemplos de gráfico de linha, barras e de pizza

Vamos examinar o código por trás de alguns controles de gráfico padrão demonstrados no aplicativo e os modelo/padrões de programação para criar os controles. Especificamente, examine a seção de HTML no comentário `// Example 3/4/5`, que renderiza os controles com valores de ID `chart3`, `chart4` e `chart5`.

Lembre-se da etapa 3 na seção [Estrutura e origem de página](#page-source-and-structure), na qual os controles de gráfico são organizados em linhas na página, cada qual com uma linha de título descritivo. Neste exemplo, os três gráficos são preenchidos no elemento `<div>` no título "Vários tipos de gráfico dos mesmos dados" e são vinculados a três elementos `<div>` abaixo dele:

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

A seção de código JavaScript a seguir usa o padrões descritos anteriormente: crie expressões de agregação TSI, use-os para consultar dados TSI e renderize os três gráficos. Observe os três tipos usados no namespace `tsiClient.ux`: `LineChart`, `BarChart` e `PieChart`, para criar e renderizar os respectivos gráficos. Observe também que todos os três gráficos podem usar os mesmos dados de expressão de agregação `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Os três gráficos da seguinte maneira aparecem quando renderizados:

[![Vários tipos de gráfico dos mesmos dados](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Recursos avançados

A biblioteca de Cliente TSI também apresenta alguns recursos avançados opcionais que você talvez queira aproveitar.

### <a name="states-and-events"></a>Estados e eventos

Um exemplo da funcionalidade avançada fornecida é a capacidade de adicionar transações de estado e eventos discretos aos gráficos. Esse recurso é útil para realçar a incidentes, alertas, e opções de estado e o estado muda como ligar/desligar.

Observe o código por trás da seção de HTML no comentário `// Example 10`. O código renderiza um controle de linha no título "Gráficos de linha com vários tipos de série", associando-o ao elemento `<div>` com o valor de ID `chart10`.

1. Primeiro, uma estrutura denominada `events4` é definida para manter os elementos de alteração de estado a serem rastreados. A estrutura contém:

   - Uma chave de cadeia de caracteres chamada `Component States`.
   - Uma matriz de objetos de valor que representam os estados. Cada objeto inclui:
     - Uma chave de cadeia de caracteres que contém um carimbo de data/hora ISO JavaScript.
     - Uma matriz que contém as características do estado: uma cor e uma descrição.

2. Em seguida, a estrutura `events5` é definida para “Incidentes”, que contém uma matriz de elementos de evento a ser rastreada. A estrutura de matriz tem a mesma forma que a descrita por `events4`.

3. Por fim, o gráfico de linha é renderizado, passando as duas estruturas com os parâmetros de opções de gráfico: `events:` e `states:`. Observe os outros parâmetros de opção para especificar um `tooltip:`, `theme:`, ou `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Visualmente, os marcadores de losango/janelas pop-up, que são usados para indicar incidentes e as barras coloridas/janelas pop-up ao longo da escala de tempo, indicam as mudanças de estado:

[![Gráficos de linha com vários tipos de série](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Menus de contexto pop-up

Outro exemplo de funcionalidade avançada são os menus de contexto personalizados (menus pop-up do botão direito do mouse). Menus de contexto personalizados são úteis para habilitar ações e próximas etapas lógicas dentro do escopo de seu aplicativo.

Observe o código por trás da seção de HTML no comentário `// Example 13/14/15`. Esse código inicialmente renderiza um gráfico de linhas com o título “Gráfico de linhas com menu de contexto para criar gráficos de pizza/barra”, e o gráfico é vinculado ao elemento `<div>` com valor de ID `chart13`. Usando os menus de contexto, o gráfico de linhas fornece a capacidade de criar dinamicamente um gráfico de piza e barra associado aos elementos `<div>` com IDs `chart14` e `chart15`. Além disso, gráficos de barras e pizza também usam menus de contexto para habilitar seus próprios recursos: a capacidade de copiar dados de gráfico de pizza para barra e imprimir os dados de gráfico de barras para a janela de console do navegador, respectivamente.

1. Primeiro, uma série de ações personalizadas são definidas. Cada ação contém uma matriz com um ou mais elementos. Cada elemento define um item de menu de contexto único:

   - `barChartActions`: esta ação define o menu de contexto para o gráfico de pizza, que contém um elemento para definir um único item:
     - `name`: O texto que é usado para o item de menu: “Parâmetros de impressão para o console”.
     - `action`: a ação associada ao item de menu. A ação é sempre uma função anônima que usa três argumentos com base na expressão de agregação usada para criar o gráfico. Nesse caso, os argumentos são gravados na janela de console do navegador:
       - `ae`: a matriz de expressão de agregação.
       - `splitBy`: o valor de splitBy.
       - `timestamp`: o carimbo de data/hora.

   - `pieChartActions`: esta ação define o menu de contexto do gráfico de barras, que contém um elemento para definir um único item. A forma e o esquema são os mesmos que os anteriores `barChartActions`, mas observe que a função `action` é drasticamente diferente, pois cria e processa o gráfico de barras. Observe também que o argumento `ae` é usado para especificar a matriz de expressão de agregação, que é passada no tempo de execução quando o item de menu se abre. A função também define a `ae.contextMenu` propriedade com o `barChartActions` menu de contexto.
   - `contextMenuActions`: esta ação define o menu de contexto do gráfico de linhas, que contém três elementos para definir três itens de menu. A forma e o esquema para cada elemento é o mesmo dos elementos anteriormente descritos. Assim como o elemento `barChartActions`, o primeiro item grava os argumentos das três funções na janela do navegador do console. Semelhante ao elemento `pieChartActions`, os dois itens instanciam e renderizam os gráficos de pizza e de barras, respectivamente. Os dois itens também definem suas `ae.contextMenu` propriedades com o `pieChartActions` e `barChartActions` menus de contexto, respectivamente.

2. Em seguida, duas expressões de agregação são enviadas por push para a matriz de expressão de agregação `aes`, especificando a matriz `contextMenuActions` para cada item. As expressões são usadas com o controle de gráfico de linha.

3. Por fim, o gráfico de linhas é inicialmente renderizado, do qual a pizza e um gráfico de barras podem ser renderizados em tempo de execução.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

A captura de tela mostra os gráficos, com seus respectivos menus de contexto pop-up. Os gráficos de pizza e de barras foram criados dinamicamente usando as opções de menu de contexto de gráfico de linha.

[![Gráfico de linha com o Menu de contexto para criar o gráfico de pizza/barra](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pincéis

Os pincéis são usados para examinar um intervalo de tempo para definir ações, como ampliar e explorar.

O código usado para ilustrar pincéis é mostrado no exemplo anterior "Gráfico de linhas com o menu de contexto para criar gráfico de pizza/barras", que descreve os Menus de contexto pop-up.

1. As ações de pincel são semelhantes a um menu de contexto, que definem uma série de ações personalizadas para o pincel. Cada ação contém uma matriz com um ou mais elementos. Cada elemento define um item de menu de contexto único:
   - `name`: O texto que é usado para o item de menu: “Parâmetros de impressão para o console”.
   - `action`: a ação associada ao item de menu, que é sempre uma função anônima que usa dois argumentos. Nesse caso, os argumentos são gravados na janela de console do navegador:
      - `fromTime`: o carimbo de data/hora “de” da seleção do pincel.
      - `toTime`: o carimbo de data/hora “para” da seleção do pincel.

2. Ações de pincel são adicionadas como outra propriedade de opção de gráfico. Observe a propriedade `brushContextMenuActions: brushActions`, que é transferida para a chamada `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

![Gráfico de linha com menu de contexto para criar o gráfico de pizza/barra usando pincéis](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Entre e explore o aplicativo de exemplo TSI e sua origem.
> * Use APIs na biblioteca de clientes JavaScript do TSI.
> * Use o JavaScript para criar e preencher os controles de gráfico com dados TSI.

Como discutido, o aplicativo de exemplo TSI usa um conjunto de dados de demonstração. Para saber como você pode criar seu próprio ambiente TSI e o conjunto de dados, vá para o seguinte artigo:

> [!div class="nextstepaction"]
> [Tutorial: criar um ambiente do Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)


