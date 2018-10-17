---
title: 'Tutorial: personalização de artigos – Serviço de Decisão Personalizada'
titlesuffix: Azure Cognitive Services
description: Um tutorial para personalização de artigos para tomada de decisão contextual.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: b142fe2051c017d0c0ec3c4cac6aaedd563f6cd7
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366328"
---
# <a name="tutorial-article-personalization-for-contextual-decision-making"></a>Tutorial: personalização de artigos para tomada de decisão contextual

Este tutorial se concentra na personalização da seleção de artigos na página frontal de um site. O Serviço de Decisão Personalizada afeta *várias* listas de artigos na página frontal, por exemplo. Talvez a página seja um site de notícias que abrange apenas política e esportes. Ela mostra três listas classificadas de artigos: política, esportes e notícias recentes.

## <a name="applications-and-action-sets"></a>Aplicativos e conjuntos de ações

É assim como você ajusta seu cenário à estrutura. Vamos imaginar três aplicativos, um para cada lista que está sendo otimizada: app-politics, app-sports e app-recent. Para especificar os artigos candidatos a cada aplicativo, há dois conjuntos de ações: um para política e outro para esportes. A ação definida para app-recent é obtida automaticamente como uma união dos outros dois conjuntos.

> [!TIP]
> Os conjuntos de ações podem ser compartilhados entre aplicativos no Serviço de Decisão Personalizada.

## <a name="prepare-action-set-feeds"></a>Preparar feeds do conjunto de ações

O Serviço de Decisão Personalizada consome conjuntos de ações por meio de RSS feeds ou feeds Atom fornecidos pelo cliente. Forneça dois feeds: um para política e outro para esportes. Suponha que eles sejam fornecidos por `http://www.domain.com/feeds/<feed-name>`.

Cada feed fornece uma lista de artigos. No RSS, cada um é especificado por um elemento `<item>`, da seguinte maneira:

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

A ordem dos artigos é importante. Ela especifica a classificação padrão, que é a melhor estimativa de como os artigos devem ser ordenados. A classificação padrão é então usada para comparação de desempenho no [painel](#performance-dashboard).

Para obter mais informações sobre o formato de feed, confira a [referência de API](custom-decision-service-api-reference.md#action-set-api-customer-provided).

## <a name="register-a-new-app"></a>Registrar um novo aplicativo

1. Entre com sua [conta Microsoft](https://account.microsoft.com/account). Na faixa de opções, clique em **Meu Portal**.

2. Para registrar um novo aplicativo, clique no botão **Novo Aplicativo**.

    ![Portal do Serviço de Decisão Personalizada](./media/custom-decision-service-tutorial/portal.png)

3. Insira um nome exclusivo para o aplicativo na caixa de texto **ID do Aplicativo**. Se esse nome já estiver em uso por outro cliente, o sistema solicitará que você escolha outra ID do aplicativo. Marque a caixa de seleção **Avançado** e insira a [cadeia de conexão](../../storage/common/storage-configure-connection-string.md) de sua conta de armazenamento do Azure. Normalmente, você usa a mesma conta de armazenamento para todos os aplicativos.

    ![Caixa de diálogo Novo Aplicativo](./media/custom-decision-service-tutorial/new-app-dialog.png)

    Depois que você registrar todos os três aplicativos no cenário descrito acima, eles serão listados:

    ![Lista de aplicativos](./media/custom-decision-service-tutorial/apps.png)

    Volte a essa lista clicando no botão **Aplicativos**.

4. Na caixa de diálogo **Novo Aplicativo**, especifique um feed de ação. Os feeds de ação também podem ser especificados clicando no botão **Feeds** e, em seguida, clicando no botão **Novo Feed**. Insira um **Nome** para o novo feed, insira a **URL** da qual ele é fornecido e insira o **Tempo de Atualização**. O tempo de atualização especifica a frequência com que o Serviço de Decisão Personalizada deve atualizar o feed.

    ![Caixa de diálogo Novo feed](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    Os feeds de ação podem ser usados por qualquer aplicativo, seja qual for o local em que foram especificados. Depois que você especificar os dois feeds de ação em um cenário, eles serão listados:

    ![Lista de feeds](./media/custom-decision-service-tutorial/feeds.png)

    Volte a essa lista clicando no botão **Feeds**.

## <a name="use-the-apis"></a>Usar as APIs

O Serviço de Decisão Personalizada classifica os artigos por meio da API de Classificação. Para usar essa API, insira o seguinte código no cabeçalho HTML da página frontal:

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

A resposta HTTP da API de Classificação é uma cadeia de caracteres formatada em JSONP. Para app-politics, por exemplo, a cadeia de caracteres é semelhante a:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

Em seguida, o navegador executa essa cadeia de caracteres como uma chamada à função `callback()`. O argumento `data` na função `callback()` contém a ID do aplicativo e a classificação das URLs a serem renderizadas. Em particular, `callback()` deve usar `data.appId` para distinguir entre os três aplicativos. `eventId` é usada internamente pelo Serviço de Decisão Personalizada para corresponder à classificação fornecida com o clique correspondente, se houver.

> [!TIP]
> `callback()` pode verificar a atualização de cada feed de ação usando o campo `lastRefresh`. Se determinado feed não é suficientemente atual, `callback()` pode ignorar a classificação fornecida, chamar esse feed diretamente e usar a classificação padrão fornecida pelo feed.

Para obter mais informações sobre as especificações e as opções adicionais fornecidas pela API de Classificação, confira a [referência de API](custom-decision-service-api-reference.md).

As opções de artigo principal do usuário são retornadas ao chamar a API de Recompensa. Quando uma opção de artigo principal é recebida, o seguinte código deve ser invocado na página frontal:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

O uso de `appId` e `eventId` no código de tratamento de cliques exige um pouco de cuidado. Por exemplo, você pode implementar a função `callback()` da seguinte maneira:

```javascript
function callback(data) {
    $.map(data.ranking, function (element) {
        //custom rendering function given content info
        render({appId:data.appId,
                article:element,
                onClick: element.id != data.rewardAction ? null :
                   function() {
                       $.ajax({
                       type: "POST",
                       url: '//ds.microsoft.com/api/v2/' + data.appId + '/reward/' + data.eventId,
                       contentType: "application/json" })}
                });
}}
```

Nesse exemplo, implemente a função `render()` para renderizar determinado artigo de um aplicativo específico. Essa função insere a ID do aplicativo e o artigo (no formato da API de Classificação). O parâmetro `onClick` é a função que deve ser chamada de `render()` para manipular um clique. Ele verifica se o clique está no slot superior. Em seguida, ele chama a API de Recompensa com a ID do aplicativo e a ID do evento apropriadas.

## <a name="next-steps"></a>Próximas etapas

* Confira a [Referência de API](custom-decision-service-api-reference.md) para saber mais sobre a funcionalidade fornecida.