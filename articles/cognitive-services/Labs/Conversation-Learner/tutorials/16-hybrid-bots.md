---
title: Como usar o Conversation Learner com outras tecnologias de criação de bots - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como a usar o Conversation Learner com outras tecnologias de criação de bots.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: v-jaswel
ms.openlocfilehash: 0e2a9918108012e0d0ef1defbff044d7ab9bd83d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796435"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Como usar o Conversation Learner com outras tecnologias de criação de bots

Este tutorial aborda como usar o Conversation Learner com outras tecnologias de criação de bots e como a memória (ou estado) pode ser compartilhada entre essas tecnologias. 

## <a name="video"></a>Vídeo

[![Versão prévia do tutorial de bots híbridos](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>Requisitos
Este tutorial requer o uso do emulador de bot para criar diálogos de registro, e não a interface do usuário da Web do Diálogo de Registro. Mais informações sobre como configurar o Bot Framework Emulator estão disponíveis [aqui](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0). 

Este tutorial exige que o bot de tutorial híbrido esteja em execução:

    npm run tutorial-hybrid

## <a name="details"></a>Detalhes

Enquanto o Conversation Learner estiver no controle, todos os estados relativos à Sessão do Conversation Learner deverão ser armazenados no gerenciador de memória do Conversation Learner. Isso é necessário, pois o aprendizado de máquina usa o estado para determinar como conduzir a conversa. O estado externo pode ser passado para o Conversation Learner no OnSessionStartCallback que é chamado quando a sessão é iniciada. O estado interno pode ser retornado pelo OnSessionEndCallback quando a sessão termina.

É possível praticamente pensar no Conversation Learner como uma chamada de função que leva algum estado inicial e retorna valores.

Neste exemplo, você criará um bot híbrido usando dois sistemas diferentes:
1. Um modelo do Conversation Learner <br/>
    Use o modelo do Conversation Learner para determinar a próxima ação do bot com base na sessão atual. Essa parte do bot leva uma parte do estado inicial `isOpen` (que indica se uma loja está aberta ou fechada) e retorna uma outra parte de estado `purchaseItem` (o nome de um item que o usuário compra).

2. Correspondência de texto <br />
    Simplesmente analisa o texto recebido das cadeias de caracteres específicas e responde. Essa parte do bot gerencia os outros mecanismos de armazenamento dos Bots e é responsável por iniciar a sessão do CL. Especificamente, ele gerencia três variáveis: `usingConversationLearner`, `storeIsOpen` e `purchaseItem`.

Vamos começar dando uma olhada no modelo usado nesta demonstração.

### <a name="open-the-demo"></a>Abrir a demonstração

Na interface do usuário da Web clique em "Importar Tutoriais" e selecione o modelo nomeado "Tutorial-16-HybridBot.

## <a name="entities"></a>Entidades

Abra a página de entidades e observe duas entidades: `isOpen` e `purchaseItem`

Para entender como essas entidades são usadas, abra o arquivo: `C:\<installedpath>\src\demos\tutorialHybrid.ts` para examinar os retornos de chamada.

Observe que o código em `OnSessionStartCallback` copia o valor de `storeIsOpen` do armazenamento de conversa do BotBuilder como o valor da entidade `isOpen`, de modo que esteja disponível para o Conversation Learner. Observe o código a seguir:

![](../media/tutorial17_sessionstart.PNG)

Da mesma forma, o código em `OnSessionEndCallback` (se a sessão foi finalizada devido a uma atividade aprendida e não apenas um tempo limite) copia o valor da entidade `purchaseItem` para o armazenamento do BotBuilder `purchaseItem`. Observe o código a seguir:

![](../media/tutorial17_sessionend.PNG)

Agora vamos analisar as Ações.

## <a name="actions"></a>Ações

Observe que o modelo tem quatro ações.

As regras pretendidas para as ações são as seguintes:

- Se a entidade `isOpen` estiver configurada, o Bot perguntará "O que você gostaria de comprar?" e armazena isso no slot `puchaseItem`.
- Se `isOpen` não estiver definido, o Bot dirá "Desculpe, estamos fechados".
- As outras duas ações são do tipo `END_SESSION`.
- A ação END_SESSION indica ao ConversationLearner que a conversa foi concluída.

### <a name="overall-bot-logic"></a>Lógica geral de bots

Primeiro, você verá que se o sinalizador `usingConversationLearner` do estado de Bot foi definido, passaremos o controle para o Conversation Learner. Se não, passamos o controle para outra ação.  Neste exemplo, estamos mostrando uma correspondência de texto simples, mas pode ser qualquer outra tecnologia de bot, incluindo LUIS, QnA Maker e até mesmo outra instância do Conversation Learner.

Precisamos de uma maneira para o usuário abrir e fechar a loja, então, fazemos uma comparação com "loja aberta" e "loja fechada" e definimos o sinalizador "storeIsOpen".

Em seguida, precisaremos de uma maneira de disparar o controle de distribuição para o nosso Modelo do Conversation Learner. Quando combinamos com a cadeia de caracteres "loja", fazemos o seguinte:
- Defina o sinalizador `usingConversationLearner` na memória do Bot.
- Chame o método "StartSession" no modelo do Conversation Learner.  Isso disparará o "onSessionStartCallback", que inicializará o valor da entidade `isOpen`

Veja abaixo:

![](../media/tutorial17_useConversationLearner.PNG)

Também fazemos uma correspondência de texto para "histórico", que exibirá esse último item de compra.
Finalmente, se mais alguma coisa for digitada, exibimos os comandos do usuário disponíveis

## <a name="train-dialog"></a>Diálogo de Treinamento

Para este tutorial, o modelo já é pré-treinado.  Vamos testar o bot completo para ver o efeito dos retornos de chamada da sessão inicial e final na prática.

## <a name="testing-the-bot"></a>Testar o Bot

Ao contrário dos bots do modelo do Conversation Learner único, não será possível testar isso na interface do Conversation Learner, já que ele só pode mostrar o que é tratado pelo Modelo do Conversation Learner.

### <a name="install-the-bot-framework-emulator"></a>Instalar o emulador de estrutura do Bot

- Vá para [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Baixe e instale o emulador.

### <a name="configure-the-emulator"></a>Configurar o emulador

- Abra o emulador e assegure-se de que a URL esteja direcionando a mesma porta em que o bot está executando. Probabilidade: `http://localhost:3978/api/messages`

### <a name="test"></a>Teste 

#### <a name="scenario-1-store-is-closed"></a>Cenário 1: A loja está fechada
1. Insira 'loja'. Isso é tratado pela correspondência de texto e dará controle ao modelo do Conversation Learner.
2. Insira “olá”.  Como o valor `isOpen` não está definido, o bot dirá "Desculpe, estamos fechados" e encerrará a sessão.

#### <a name="scenario-2-store-is-open"></a>Cenário 2: A loja está aberta
3. Insira 'loja aberta'.  Isso definirá o `isOpen` como verdadeiro.
4. Insira 'loja'.
5. Insira “olá”.  Como o valor `isOpen` está definido como verdadeiro, o bot dirá "O que você gostaria de comprar?"
6. Insira 'cadeira'. 'cadeira' será salva na memória CL como a entidade `purchaseItem`. O retorno de chamada da sessão final é chamado, copiando esse valor para o armazenamento de conversa.
7. Insira 'histórico'.  O bot dirá 'Você comprou a cadeira', pois esse foi o último `purchaseItem`.

## <a name="conclusion"></a>Conclusão

Com o que foi aprendido até agora, você deverá ser capaz de combinar o Conversation Learner com qualquer outra tecnologia de criação de bot.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ramificação e desfazer](./17-branch-undo.md)
