---
title: Tutorial para criar um aplicativo LUIS para obter dados de localização - Azure | Microsoft Docs
description: Neste tutorial, saiba como criar um aplicativo de LUIS simples usando intenções e uma entidade hierárquica para extrair dados.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: v-geberr
ms.openlocfilehash: 2547407126943161ba604fa2f5e80b9186cae57e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266491"
---
# <a name="tutorial-create-app-that-uses-hierarchical-entity"></a>Tutorial: Criar um aplicativo que usa uma entidade hierárquica
Neste tutorial, crie um aplicativo que demonstra como localizar as partes relacionadas dos dados com base no contexto. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Entender as entidades hierárquicas e filhos contextualmente adquiridos 
> * Criar novo aplicativo de LUIS para domínio de viagem com intenção de reservar um voo
> * Adicionar a intenção _Nenhuma_ e adicione os enunciados de exemplo
> * Adicionar entidade hierárquica local com os filhos de origem e destino
> * Treinar e publicar o aplicativo
> * Consulte o ponto de extremidade do aplicativo para ver a resposta JSON do LUIS incluindo os filhos hierárquicos 

Para este artigo, você precisa de uma conta gratuita de [LUIS][LUIS] para criar o seu aplicativo LUIS.

## <a name="purpose-of-the-app-with-this-entity"></a>Finalidade do aplicativo com essa entidade
Este aplicativo determina se um usuário deseja reservar um voo. Ele usa a entidade hierárquica para determinar os locais, a cidade de origem e a cidade de destino dentro do texto do usuário. 

A entidade hierárquica é uma boa opção para esse tipo de dado porque as duas partes dos dados:

* São os locais, geralmente expressos como cidades ou códigos de aeroporto.
* Geralmente têm opções de palavra exclusiva para as palavras para determinar qual é a origem e qual é o destino. Essas palavras incluem: para, em direção a, de, partindo.
* Os locais estão frequentemente no mesmo enunciado. 

A finalidade da entidade **hierárquica** é encontrar dados relacionados dentro do enunciado com base no contexto. Considere o enunciado a seguir:

```JSON
1 ticket from Seattle to Cairo`
```

O enunciado tem dois locais especificados. Um é a cidade de origem, Seattle, e o outro é a cidade de destino, Cairo. Essas cidades são importantes para reservar um voo. Apesar delas poderem ser encontradas usando entidades simples, elas estão relacionados entre si e com frequência estarão no mesmo encunciado. Portanto, faz sentido que elas estejam agrupadas como filhos de uma entidade hierárquica, **"Local"**. 

Como são entidades aprendidas por máquina, o aplicativo precisa de enunciados de exemplo com as cidades de origem e destino rotuladas. Isso ensina o LUIS onde as entidades estão nos enunciados, o tamanho delas e as palavras que estão ao redor. 

## <a name="app-intents"></a>Intenções de aplicativo
As intenções são categorias do que o usuário deseja. Este aplicativo tem duas intenções: BookFlight e None. A intenção [None](luis-concept-intent.md#none-intent-is-fallback-for-app) é proposital, e indica qualquer coisa fora do aplicativo.  

## <a name="hierarchical-entity-is-contextually-learned"></a>A entidade hierárquica é aprendida contextualmente 
A finalidade da entidade é localizar e categorizar partes do texto no enunciado. Uma entidade [hierárquica](luis-concept-entity-types.md) é uma entidade pai-filho com base no contexto do uso. Uma pessoa pode determinar as cidades de origem e de destino em um enunciado com base no uso de `to` e `from`. Esses são um exemplo de uso contextual.  

Para este aplicativo de viagem, LUIS extrai os locais de origem e de destino de forma que uma reserva padrão possa ser criada e preenchida. O LUIS permite que o enunciado tenha variações, abreviações e gíria. 

Os exemplos de enunciados simples dos usuários incluem:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Researve a seat from New York to Paris on the first of April
```

Versões abreviadas ou com gírias de enunciados incluem:

```
LHR tomorrow
SEA to NYC next Monday
LA to MCO spring break
```
 
A entidade hierárquica corresponde ao local de origem e destino. Se apenas um filho (origem ou destino) de uma entidade hierárquica estiver presente, ele ainda assim é extraído. Não é necessário que se tenha todos filhos para que seja extraído um ou alguns. 

## <a name="what-luis-does"></a>O que o LUIS faz
Quando a intenção e as entidades do enunciado são identificadas, [extraídas](luis-concept-data-extraction.md#list-entity-data) e retornadas em JSON do [ponto de extremidade](https://aka.ms/luis-endpoint-apis), o LUIS conclui sua ação. O aplicativo de chamada ou chatbot usa essa resposta JSON e atende a solicitação, conforme a maneira como o aplicativo ou chatbot foram projetados. 

## <a name="create-a-new-app"></a>Criar um novo aplicativo
1. Faça logon no site do [LUIS][LUIS]. Certifique-se de fazer logon na [região][LUIS-regions] onde você precisa dos pontos de extremidade de LUIS publicados.

2. No site do [LUIS][LUIS], selecione **Create new app** (Criar novo aplicativo).  

    [![](media/luis-quickstart-intent-and-hier-entity/app-list.png "Captura de tela de Listas de aplicativos")](media/luis-quickstart-intent-and-hier-entity/app-list.png#lightbox)

3. Na caixa de diálogo pop-up, insira o nome `MyTravelApp`. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-app.png "Captura de tela da caixa de diálogo pop-up Criar novo aplicativo")](media/luis-quickstart-intent-and-hier-entity/create-new-app.png#lightbox)

4. Após a conclusão desse processo, o aplicativo mostrará a página **Intenções** com a intenção **None**. 

    [![](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png "Captura de tela da lista de Intenções somente com a intenção None")](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Criar uma nova intenção

1. Na página **Inteções**, selecione **Criar nova intenção**. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png "Captura de tela da lista de Intenções com o botão Criar nova intenção realçado")](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png#lightbox)

2. Insira o nome da nova intenção `BookFlight`. Essa intenção deve ser selecionada sempre que um usuário quiser reservar um voo.

    Ao criar uma intenção, você está criando a categoria principal de informações que você deseja identificar. Ao nomear a categoria, qualquer outro aplicativo que use os resultados de consulta do LUIS poderá usar esse nome de categoria para encontrar uma resposta ou executar uma ação apropriada. O LUIS não responderá a essas perguntas, apenas identifica o tipo de informação que está sendo solicitado em linguagem natural. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png "Captura de tela da caixa de diálogo pop-up Criar nova intenção")](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png#lightbox)

3. Adicione vários enunciados à intenção `BookFlight` os quais você espera que um usuário solicite, por exemplo:

    | Exemplo de enunciados|
    |--|
    |Reservar 2 voos de Seattle para Cairo na próxima segunda-feira|
    |Reservar uma passagem para Londres amanhã|
    |Escolher 4 assentos de Paris para Londres para 1º de abril|

    [![](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png "Captura de tela de inserção do enunciado na página da intenção BookFlight")](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Adicionar enunciados à intenção None

No momento, o aplicativo LUIS não tem enunciados para a intenção **None**. Ele precisa de enunciados que você não deseja que o aplicativo responda, então ele deve ter enunciados na intenção **None**. Não deixe em branco. 

1. Selecione **Intenções** no painel esquerdo. 

    [![](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png "Captura de tela da página de intenção BookFlight com o botão Intenções realçado")](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png#lightbox)

2. Selecione a intenção **None**. Adicione três enunciados que o usuário pode inserir, mas que não são relevantes para o seu aplicativo:

    | Exemplo de enunciados|
    |--|
    |Cancelar!|
    |Até logo|
    |O que está acontecendo?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>Quando o enunciado for previsto para a intenção None
No aplicativo que chama o LUIS (como um chatbot), quando o LUIS retornar a intenção **None** para um enunciado, o bot poderá perguntar se o usuário deseja terminar a conversa. O bot também pode fornecer mais orientações para continuar a conversa, se o usuário não quiser encerrá-lo. 

Trabalho das entidades na intenção **None**. Se a intenção com maior pontuação for **None**, mas ocorrer a extração de uma entidade que faça sentido para seu chatbot, o chatbot poderá enviar uma pergunta seguinte enfatizando a intenção do cliente. 

## <a name="create-a-location-entity-from-the-intent-page"></a>Criar uma entidade de local na página Intenção
Agora que as duas intenções possuem enunciados, LUIS precisa entender é que um local. Navegue de volta até a intenção `BookFlight` e rotule (marque) o nome da cidade em um enunciado, seguindo as etapas:

1. Volte para a intenção `BookFlight` selecionando **Intenções** no painel esquerdo.

2. Selecione `BookFlight` na lista de intenções.

3. No enunciado, `Book 2 flights from Seattle to Cairo next Monday`, selecione a palavra `Seattle`. Um menu suspenso é exibido com uma caixa de texto na parte superior para criar uma nova entidade. Insira o nome da entidade `Location` na caixa de texto, selecione **Criar nova entidade** no menu suspenso. 

    [![](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png "Captura de tela da página da intenção BookFlight, criando uma nova entidade a partir do texto selecionado")](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png#lightbox)

4. Na janela pop-up, selecione o tipo de entidade **Hierárquica** com `Origin` e `Destination` como as entidades filho. Selecione **Concluído**.

    [![](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png "Captura de tela da caixa de diálogo pop-up de criação de entidade para a nova entidade de Local")](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png#lightbox)

    O rótulo de `Seattle` está marcado como `Location` porque o LUIS não sabe se o termo foi a origem, o destino ou nenhum deles. Selecione `Seattle`, em seguida, selecione Local e siga o menu à direita e selecione `Origin`.

5. Agora que a entidade foi criada e um enunciado foi rotulado, rotule as outras cidades selecionando o nome da cidade e, em seguida, selecionando o Local, e seguindo o menu à direita para selecionar `Origin` ou `Destination`.

    [![](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png "Captura de tela da entidade Bookflight com o texto do enunciado selecionado para seleção de entidade")](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png#lightbox)

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS
O LUIS não fica ciente das alterações nas intenções e entidades (o modelo) até que seja treinado. 

1. No canto superior direito do site do LUIS, selecione o botão **Train** (Treinar).

    ![Treinar o aplicativo](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. O treinamento é concluído quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

    ![Treinamento bem-sucedido](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar o aplicativo para obter a URL do ponto de extremidade
Para obter uma previsão do LUIS em um chatbot ou outro aplicativo, você precisa publicar o aplicativo. 

1. No canto superior direito do site do LUIS, selecione o botão **Publish** (Publicar). 

    [![](media/luis-quickstart-intent-and-hier-entity/publish.png "Captura de tela da intenção BookFlight com o botão Publicar realçado")](media/luis-quickstart-intent-and-hier-entity/publish.png#lightbox)

2. Selecione o Slot de produção e o botão **Publicar**.

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "Captura de tela da página Publicar com o botão Publicar no slot de produção realçado")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. A publicação é concluída quando você vir a barra de status verde na parte superior do site confirmando o sucesso.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Consultar o ponto de extremidade com um enunciado diferente
1. Na página **Publicar**, selecione o link do **ponto de extremidade** na parte inferior da página. Essa ação abre outra janela do navegador com a URL de ponto de extremidade na barra de endereços. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "Captura de tela da página Publicar com a URL de ponto de extremidade realçada")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. Vá até o final da URL no endereço e insira `1 ticket to Portland on Friday`. O último parâmetro de querystring é `q`, o enunciado **q**uery. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `BookFlight` com a entidade hierárquica extraída.

```
{
  "query": "1 ticket to Portland on Friday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9998226
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.9998226
    },
    {
      "intent": "None",
      "score": 0.221926212
    }
  ],
  "entities": [
    {
      "entity": "portland",
      "type": "Location::Destination",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.564448953
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Esse aplicativo, com apenas duas intenções e uma entidade hierárquica, identificou uma intenção de consulta de linguagem natural e retornou os dados extraídos. 

Agora seu chatbot tem informações suficientes para determinar a ação primária, `BookFlight`, e as informações de localização encontradas no enunciado. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode levar o resultado de topScoringIntent e os dados da entidade para realizar a próxima etapa. O LUIS não realiza esse trabalho de programação para o bot ou para o aplicativo de chamada. O LUIS só determina qual é a intenção do usuário. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo do LUIS. Para fazer isso, selecione o menu de três pontos (...) à direita do nome do aplicativo na lista de aplicativos e selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Saiba como adicionar uma entidade de lista](luis-quickstart-intent-and-list-entity.md) 

Adicione a **entidade predefinida** [number](luis-how-to-add-entities.md#add-prebuilt-entity) para extrair o número. 

Adicione a **entidade predefinida** [datetimeV2](luis-how-to-add-entities.md#add-prebuilt-entity) para extrair as informações de data.


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
