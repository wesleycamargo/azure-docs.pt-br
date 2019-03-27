---
title: 'Início rápido: Criar um aplicativo com o portal do LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Neste guia de início rápido, você cria um aplicativo no portal do LUIS. Crie as partes básicas de um aplicativo, as intenções e as entidades e, em seguida, teste fornecendo um enunciado de usuário de exemplo no painel de teste interativo para obter a intenção prevista. Criar um aplicativo é gratuito e não requer uma assinatura do Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: e97be28261d28c2a72e507adcdac0248691745c7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783165"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Início rápido: Criar um aplicativo no portal do LUIS

Neste guia de início rápido, você cria um aplicativo no [portal do LUIS](https://www.luis.ai). Crie as partes básicas de um aplicativo, as **intenções** e as **entidades** e, em seguida, teste fornecendo um enunciado de usuário de exemplo no painel de teste interativo para obter a intenção prevista.

Criar um aplicativo é gratuito e não requer uma assinatura do Azure. Quando você estiver pronto para implantar seu aplicativo, em seguida, crie um recurso do Serviço Cognitivo do Azure e atribua-o ao aplicativo. Esse processo de implantação está no [próximo início rápido](get-started-portal-deploy-app.md).

## <a name="create-app"></a>Criar aplicativo 

1. Abra o [portal do LUIS](https://www.luis.ai) em um navegador e entre. Se esta for a primeira vez que você entra, você precisará criar uma conta de usuário gratuita do portal do LUIS.

1. Selecione **Criar aplicativo** da barra de ferramentas de contexto.

    [![Criar aplicativo no portal do LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. Na janela pop-up, configure o aplicativo com as configurações a seguir e selecione **Concluído**.

    |Nome da configuração| Valor | Finalidade|
    |--|--|--|
    |NOME|`myEnglishApp`|Nome exclusivo de aplicativo LUIS<br>obrigatório|
    |Cultura|**Inglês**|Linguagem de enunciados de usuários, **pt-br**<br>obrigatório|
    |DESCRIÇÃO|`App made with LUIS Portal`|Descrição do aplicativo<br>opcional|

    ![Inserir configurações do novo aplicativo](./media/get-started-portal-build-app/create-new-app-settings.png)


## <a name="create-intent"></a>Criar a intenção 

Depois que esse aplicativo é criado, a próxima etapa é criar as intenções. Intenções são uma forma de categorizar o texto dos usuários. Se você tiver um aplicativo de recursos humanos que tem duas funções, a primeira para ajudar as pessoas a encontrar vagas de emprego e se candidatar a elas e a segunda para encontrar formas de se candidatar a vagas de emprego, essas duas diferentes _intenções_ se alinharão às seguintes intenções:

|Intenção|Exemplo de texto do usuário<br>conhecido como um _enunciado_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

1. Depois que o aplicativo é criado, você está na página **Intenções** da seção **Build**. Selecione **Criar nova intenção**. 

    [![Selecione o botão Criar nova intenção](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Insira o nome da intenção, `FindForm`, e selecione **Concluído**.

    ![Insira o nome da intenção do FindForm](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-example-utterance"></a>Adicionar exemplo de enunciado 

Depois de criar a intenção, a próxima etapa é adicionar exemplos de enunciado. Eles são texto inserido pelo usuário em um chatbot ou outro aplicativo cliente, que mapeia a intenção do texto do usuário para uma intenção do LUIS. 

Para a intenção `FindForm` deste aplicativo de exemplo, exemplos de enunciado incluirão o número do formulário, que é uma informação importante dentro do enunciado que o aplicativo cliente precisa em ordem para cumprir a solicitação do usuário. 

Adicionar os seguintes quinze exemplos de enunciado à intenção `FindForm`. 

|#|Exemplo de enunciados|
|--|--|
|1|Procurando por hrf-123456|
|2|Onde está o formulário de recursos humanos hrf-234591?|
|3|hrf-345623, onde ele está|
|4|É possível enviar-me o hrf-345794|
|5|Eu preciso do hrf-234695 para candidatar-me a uma vaga de emprego interna?|
|6|Meu gerente precisa saber que estou me candidatando a um emprego com hrf-234091|
|7|Para onde envio o hrf-234918? Eu obtenho uma resposta por email de que ele foi recebido?|
|8|hrf-234555|
|9|Quando o hrf-234987 foi atualizado?|
|10|Posso usar o formulário hrf-876345 me candidatar a vagas de engenharia|
|11|Uma nova versão do hrf-765234 foi enviada para minha solicitação em aberto?|
|12|Devo usar o hrf-234234 para vagas de emprego internacionais?|
|13|Erro de ortografia no hrf-234598|
|14|o hrf-234567 será editado para novos requisitos|
|15|hrf-123456, hrf-123123, hrf-234567|

Esses exemplos de enunciado variam, propositalmente, das seguintes maneiras:

* tamanho do enunciado
* pontuação
* escolha de palavras
* tempos verbais (é, foi, será)
* ordem das palavras

[![Inserir os exemplos de enunciado para a intenção do FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-regular-expression-entity"></a>Criar entidade de expressão regular 

Para obter o número de formulário retornado como parte da resposta à previsão do tempo de execução, o formulário precisa ser marcado como uma entidade. Uma vez que o texto do número de formulário é altamente estruturado, ele pode ser marcado usando uma entidade de expressão regular. Crie a entidade com as etapas a seguir. 

1. Selecione **Entidades** no menu de navegação à esquerda. 

1. Selecione **Criar nova entidade** na página Entidades.

1. Insira o nome `Human Resources Form Number`, selecione o tipo de entidade **Regex** e insira a expressão regular, `hrf-[0-9]{6}`. Isso corresponde aos caracteres literais, `hrf-`, e permite exatamente seis dígitos. 

    ![Insira as informações de entidade da entidade de expressão regular](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Selecione **Concluído**. 

## <a name="add-example-utterances-to-none-intent"></a>Adicionar exemplos de enunciado à intenção None

A intenção **Nenhum** é a intenção de fallback e não deve ser deixada vazia. Essa intenção precisa ter um enunciado para cada dez no restante das intenções do aplicativo. 

Os exemplos de enunciado da intenção **Nenhum** devem ficar fora do seu domínio do aplicativo cliente. 

1. Selecione **Intenções** no menu à esquerda e selecione **Nenhum** na lista Intenções.

1. Adicione os quinze exemplos de enunciado a seguir à intenção:

    |Exemplos de enunciado da intenção Nenhum|
    |--|
    |Cachorros que latem demais são irritantes|
    |Peça uma pizza para mim|
    |Pinguins no oceano|

    Para este aplicativo de recursos humanos, esses exemplos de enunciado estão fora do domínio. Se o seu domínio de recursos humanos inclui animais, comida ou o oceano, esses exemplos de enunciado não devem ser usados para a intenção **Nenhum**. 

## <a name="train-the-app"></a>Treinar o aplicativo

No painel de navegação superior direito, selecione **Treinar** para aplicar a intenção e a entidade de modelo é alterada para a versão atual do aplicativo. 

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Examinar a entidade de expressão regular nos exemplos de enunciado

1. Verifique se a entidade foi encontrada na intenção **FindForm** selecionando **Intenções** no menu à esquerda e, em seguida, selecionando a intenção **FindForm**. 

    A entidade é marcada onde ela aparece nos exemplos de enunciado. Se você quiser ver o texto original, em vez do nome de entidade, ative/desative a **Exibição de Entidades** na barra de ferramentas.

    [![Todos os exemplos de enunciado marcados com entidades](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testar seu novo aplicativo com o painel de teste interativo

Use o painel de **Teste** interativo no portal do LUIS para validar que a entidade é extraída de novos enunciados que o aplicativo ainda não viu.

1. Selecione **Teste** no menu superior direito.

1. Adicione um novo enunciado e pressione Enter:

    ```Is there a form named hrf-234098```

    ![Testar o novo enunciado no painel de teste](./media/get-started-portal-build-app/test-new-utterance.png)

    A intenção pretendida com a pontuação mais elevada é (corretamente) a **FindForm**, com mais de 90% confiança (0.977) e a entidade **Número de formulário de recursos humanos** é extraída com um valor hrf-234098. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando você concluir este guia de início rápido e não estiver passando para o próximo guia de início rápido, selecione **Meus aplicativos** no menu de navegação superior. Em seguida, selecione na lista e à esquerda a caixa de seleção do aplicativo e selecione **Excluir** na barra de ferramentas de contexto acima da lista. 

[![Excluir aplicativo da lista Meus aplicativos](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [2. Implantar um aplicativo](get-started-portal-deploy-app.md)