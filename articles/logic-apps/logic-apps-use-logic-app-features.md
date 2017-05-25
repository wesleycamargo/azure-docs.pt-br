---
title: "Adicionar condições e gatilhos para executar fluxos de trabalho – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Controle como os fluxos de trabalho são executados em Aplicativos Lógicos do Azure adicionando parâmetros, gatilhos, ações e lógica condicional."
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: LADocs; stepsic
ms.translationtype: Human Translation
ms.sourcegitcommit: 9f7d623ec213de6d46f59547aff9d4417ac95ede
ms.openlocfilehash: 41aafe94d24f0e22fe2256ab213c7668b670764c
ms.contentlocale: pt-br
ms.lasthandoff: 02/15/2017


---
# <a name="use-logic-apps-features"></a>Usar recursos de aplicativos lógicos
No [tópico anterior](../logic-apps/logic-apps-create-a-logic-app.md), você criou seu primeiro aplicativo lógico. Agora, você criará um processo mais completo com o Aplicativo Lógico do Azure. Este tópico apresenta novos conceitos do Aplicativo Lógico do Azure:

* Lógica condicional, que executa uma ação somente quando uma determinada condição for atendida.
* Exibição de código para editar um aplicativo lógico existente.
* Opções para iniciar um fluxo de trabalho.

Antes de concluir este tópico, você deve concluir as etapas em [Criar um novo aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md). No [Portal do Azure], navegue até o aplicativo lógico e clique em **Gatilhos e Ações** em resumo para editar a definição de aplicativo lógico.

## <a name="reference-material"></a>Material de referência
Os documentos a seguir podem ser úteis:

* [Gerenciamento e APIs REST de tempo de execução](https://msdn.microsoft.com/library/azure/mt643787.aspx) - incluindo como chamar aplicativos de lógica diretamente
* [Referência de linguagem](https://msdn.microsoft.com/library/azure/mt643789.aspx) - uma lista abrangente de todas as expressões/funções com suporte
* [Tipos ação e gatilho](https://msdn.microsoft.com/library/azure/mt643939.aspx) - os diferentes tipos de ações e as entradas que aceitam
* [Visão geral do serviço de aplicativo](../app-service/app-service-value-prop-what-is.md) - descrição de quais componentes podem ser escolhidos ao criar uma solução

## <a name="add-conditional-logic-to-your-logic-app"></a>Adicionar lógica condicional ao seu aplicativo lógico

Embora o fluxo original do seu aplicativo lógico funcione, poderíamos aprimorar algumas áreas.

### <a name="conditional"></a>Condicional

Seu primeiro aplicativo lógico pode resultar em você receber emails demais. As etapas a seguir adicionam uma lógica condicional de modo que você recebe apenas um email quando o tweet vem de alguém com um determinado número de seguidores.

0. No Designer de Aplicativos Lógicos, selecione **Nova Etapa** (+) > **Adicionar uma ação**.
0.    Localize e adicione a ação **Obter Usuário** do Twitter.
0. Localize e adicione o campo **Tweet publicado por** do gatilho para obter informações sobre o usuário do Twitter.

    ![Obter Usuário](media/logic-apps-use-logic-app-features/getuser.png)

0. Escolha **Nova etapa** (+) > **Adicionar uma condição**.
0. Para filtrar o número de seguidores que os usuários têm, em **Nome do objeto**, escolha **Adicionar conteúdo dinâmico**. 
0.    Na caixa de pesquisa, localize e adicione o campo **Contagem de seguidores**.
0. Em **Relação**, selecione **é maior que**.
0. Na caixa **Valor**, digite o número de seguidores que você deseja que os usuários tenham.

    ![Condicional](media/logic-apps-use-logic-app-features/conditional.png)

0. Por fim, arraste e solte a caixa **Enviar email** na caixa **Se Sim**. 

Agora você recebe emails somente quando a contagem de seguidores atende à condição estabelecida por você.

## <a name="repeat-actions-over-a-list-with-foreach"></a>Repita ações em uma lista com forEach

O loop forEach especifica uma matriz para repetir uma ação. Se ela não for uma matriz, o fluxo falhará. Por exemplo, se você tiver action1 que gere uma matriz de mensagens e desejar enviar cada mensagem, você poderá incluir essa instrução forEach nas propriedades da ação: `forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>Editar a definição de código para um aplicativo lógico

Embora você tenha o Designer de Aplicativos Lógicos, você pode editar diretamente o código que define um aplicativo lógico.

1. Na barra de comandos, escolha **Exibição de código**.

    Isso abre um editor completo que mostra a definição que você editou.

    ![Visualização do código](media/logic-apps-use-logic-app-features/codeview.png)

    No editor de texto, você pode copiar e colar qualquer número de ações dentro do mesmo aplicativo lógico ou entre os aplicativos lógicos. 
    Você também pode facilmente adicionar ou remover seções inteiras da definição e compartilhar definições com outras pessoas.

2. Para salvar suas edições, escolha **Salvar**.

### <a name="parameters"></a>Parâmetros

Alguns recursos de aplicativos lógicos como parâmetros estão disponíveis apenas na exibição de código. Parâmetros facilitam a reutilização valores ao longo do seu aplicativo lógico. Por exemplo, se você tiver um endereço de email que deseje usar em várias ações, deverá defini-lo como um parâmetro.

Os parâmetros são uma boa maneira de recuperar valores que você provavelmente alterará várias vezes. Eles são especialmente úteis quando você precisa substituir parâmetros em ambientes diferentes. Para saber como substituir parâmetros com base no ambiente, consulte a [Documentação da API REST](https://docs.microsoft.com/rest/api/logic).

Este exemplo mostra como atualizar seu aplicativo lógico existente para que você possa usar parâmetros para o termo de consulta.

1. No modo de exibição de código, localize o objeto `parameters : {}` e adicione um objeto de tópico:

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }

2. Vá para a ação `twitterconnector`, localize o valor de consulta e substitua esse valor por `#@{parameters('topic')}`. 

    Para unir duas ou mais cadeias de caracteres, você também pode usar a função `concat`. 
    Por exemplo, `@concat('#',parameters('topic'))` funciona como demonstrado acima.

3.    Quando terminar, escolha **Salvar**. 

    Agora, a cada hora, você receberá os novos tweets com mais de cinco retweets entregues em uma pasta chamada **tweets** em seu Dropbox.

Para saber mais sobre as definições de aplicativos lógicos, consulte [Criar definições de aplicativos lógicos](../logic-apps/logic-apps-author-definitions.md).

## <a name="start-logic-app-workflows"></a>Iniciar fluxos de trabalho de aplicativo lógico

Você tem diferentes opções para iniciar o fluxo de trabalho definido no aplicativo lógico. Você sempre pode iniciar um fluxo de trabalho por demanda no [Portal do Azure].

### <a name="recurrence-triggers"></a>Gatilhos de recorrência

Um gatilho de recorrência é executado em um intervalo especificado por você. Quando o gatilho tem lógica condicional, ele determina se o fluxo de trabalho precisa ou não ser executado. Um gatilho indica que o fluxo de trabalho deve ser executado retornando um código de status `200`. Quando o fluxo de trabalho não precisa ser executado, o gatilho retorna um código de status `202`.

### <a name="callback-using-rest-apis"></a>Retorno de chamada usando APIs REST

Para iniciar um fluxo de trabalho, os serviços podem chamar um ponto de extremidade do aplicativo lógico. Para iniciar esta variante de aplicativo lógico sob demanda, escolha **Executar agora** na barra de comandos. Consulte [Iniciar fluxos de trabalho chamando pontos de extremidade de aplicativo lógico como gatilhos](../logic-apps/logic-apps-http-endpoint.md). 

<!-- Shared links -->
[Portal do Azure]: https://portal.azure.com

