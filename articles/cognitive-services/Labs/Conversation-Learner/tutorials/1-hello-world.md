---
title: Como criar um modelo de Aprendiz de Conversa "Olá, Mundo" - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como criar um modelo de Aprendiz de Conversa "Olá, Mundo".
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 271141f24ff729fc99210af67ad769a5ef83a65c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242714"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Como criar um modelo "Olá, Mundo" com o Aprendiz de Conversa

Este tutorial mostra uma introdução ao Aprendiz de Conversa, incluindo a criação de ações, ensino interativo e correções de diálogos registrados dos usuários finais.

## <a name="video"></a>Vídeo

[![Versão prévia do Tutorial 1](https://aka.ms/cl-tutorial-01-preview)](https://aka.ms/blis-tutorial-01)


## <a name="requirements"></a>Requisitos
Se você ainda não fez isso, conclua todas as etapas de configuração, incluindo a criação de um arquivo `.env` com sua chave de criação do LUIS.  Consulte [Início Rápido](https://github.com/Microsoft/ConversationLearner-Samples) para obter detalhes.

Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="steps"></a>Etapas

Inicie na home page na interface de usuário da Web.

### <a name="create-the-model"></a>Criar o modelo
1. Clique em Novo Modelo
2. No campo Nome, insira Olá, Mundo
3. Clique em Criar. 

### <a name="create-an-action"></a>Criar uma ação

1. Clique no modelo Olá, Mundo para iniciá-lo
2. Clique em Ações e, em seguida, em Nova Ação
    - Uma Ação pode ser uma mensagem de texto retornada pelo Aprendiz de Conversa para o usuário, uma chamada de API ou um cartão.
3. Em Resposta, digite "Olá, Mundo!"
    - Esta é a resposta que o bot retornará
4. Clique em Criar. 

Você criou a primeira coisa que o bot pode fazer, ou seja, retornar uma resposta de texto.

### <a name="train-the-bot"></a>Treinar o bot

#### <a name="create-the-first-dialog"></a>Criar o primeiro diálogo

1. Clique em Diálogos de Treinamento e, em seguida, em Novo Diálogo de Treinamento
2. Insira um exemplo do que o usuário dirá no início da conversa, por exemplo, "olá".
3. Clique em Ações de Pontuação
4. Selecione "Olá, Mundo!"
    - Isso cria um diálogo de exemplo único. 
2. Insira "adeus"
3. Clique em Ações de Pontuação
4. Clique em Adicionar Ação e depois insira "Adeus!" em Resposta, clique em "Criar"
5. Clique em Ensino Concluído. Isso terminará esse diálogo de treinamento.

Agora você tem um diálogo de ensino no sistema.

#### <a name="continue-teaching-the-bot"></a>Continuar ensinando o bot
Vamos fazer mais um treinamento e ver como o bot responde.

1. Clique em Novo Diálogo de Treinamento
2. Insira "oi"
    - Isso é semelhante ao primeiro diálogo, e esperamos obter uma boa pontuação do bot.
2. Clique em Ação de Pontuação
    - Talvez a posição e a pontuação ainda não sejam precisas o bastante e exijam ensino adicional.
3. Clique em Selecionar ao lado de "Olá, Mundo!"
4. Depois, insira "tchau"
5. Clique em Ações de Pontuação
6. Selecione "Adeus!"
7. Clique em Ensino Concluído

![](../media/tutorial1_actions.PNG)

Faremos outra sessão de ensino para ver como o bot está funcionando.

Repita as etapas acima usando "oi" e "tchau tchau" e anote as alterações na posição e pontuação da resposta dos bots quando você clicar em Ação de Pontuação.

Agora, você pode repetir as etapas usando "e aí" e "até logo" e observe que a pontuação mostra aprimoramentos em pontuações indicando que o bot aprendeu essa interação.

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>Testar o bot como usuário final

1. Clique Registrar Diálogos, depois em Novo Registro de Diálogo
2. Digite "olá"
3. Depois, "tchau"

Você também pode tentar iniciar uma conversa com "tchau" e observar a resposta do bot.

### <a name="view-conversations-in-the-log-dialogs"></a>Exibir conversas no Registro de Diálogos

Em Registrar Diálogos, veja a lista de conversas, atualize e salve as interações como diálogos de treinamento. Para fazer isso:

1. Clique no registro de uma conversa
2. Se a conversa for boa, clique na última ação, por exemplo "Adeus".
3. Clique para selecionar a resposta sugerida. 
    - Você também pode selecionar ou adicionar outra ação.
4. Em seguida, clique em Concluir para salvar isso como um diálogo de treinamento.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ações de espera e não espera](./2-wait-vs-nonwait-actions.md)