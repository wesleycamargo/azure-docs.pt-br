---
title: Como usar o Aprendiz de Conversa com outras tecnologias de compilação de bots - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como a usar o Aprendiz de Conversa com outras tecnologias de compilação de bots.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: v-jaswel
ms.openlocfilehash: a03596ff8383a085314508d4a25d0ba89bcc3094
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39174475"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Como usar o Aprendiz de Conversa com outras tecnologias de compilação de bots

Este tutorial aborda como usar o Aprendiz de Conversa com outras tecnologias de compilação de bots e como a memória (ou estado) pode ser compartilhada entre essas tecnologias 

## <a name="video"></a>Vídeo

[![Versão prévia do Tutorial 15](http://aka.ms/cl-tutorial-15-preview)](http://aka.ms/blis-tutorial-15)

## <a name="requirements"></a>Requisitos
Este tutorial requer o uso do emulador de bot para criar diálogos de registro, e não a interface do usuário da Web do Diálogo de Registro.  

Este tutorial exige que o bot de tutorial híbrido esteja em execução:

    npm run tutorial-hybrid

## <a name="details"></a>Detalhes

Enquanto o Aprendiz de Conversa estiver no controle, todos os estados relativos à Sessão de Aprendiz de Conversa devem ser armazenados no gerenciador de memória do Aprendiz de Conversa. Isso é necessário, pois o aprendizado de máquina usa o estado para determinar como conduzir a conversa. O estado externo pode ser passado para o Aprendiz de Conversa no OnSessionStartCallback que é chamado quando a sessão é iniciada. O interno pode ser retornado pelo OnSessionEndCallback quando a sessão terminar.

É possível praticamente pensar no Aprendiz de Conversa como uma chamada de função que leva algum estado inicial e retorna valores.

Neste exemplo, você criará um bot híbrido usando dois sistemas diferentes:
1. Um modelo de Aprendiz de Conversa <br />
Usa o modelo de Aprendiz de Conversa para determinar a próxima ação do bot com base na sessão atual.
Esse bloco do bot leva uma parte do estado inicial `isOpen` (que indica se uma loja está aberta ou fechada) e retorna outra parte do estado `purchaseItem` (o nome de um item que o usuário compra.)

2. Correspondência de texto <br />
Simplesmente analisa o texto recebido para cadeia de caracteres específicas e responde.
Esse bloco do bot gerencia os outros mecanismos de armazenamento do Bots e é responsável por iniciar a sessão do CL. Especificamente, ele gerencia três variáveis: `usingConversationLearner`, `storeIsOpen` e `purchaseItem`.

Vamos começar dando uma olhada no modelo usado nesta demonstração.

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista de modelos da interface do usuário da Web, clique em Tutorial-15-Hybrid.

## <a name="entities"></a>Entidades

Abra a página de entidades e observe duas entidades: `isOpen` e `purchaseItem`

Para entender como essas entidades são usadas, abra o arquivo: `C:\<installedpath\>\src\demos\tutorialHybrid.ts` para examinar os retornos de chamada.

Observe que o código em `OnSessionStartCallback` copia o valor de `storeIsOpen` do armazenamento de conversa do BotBuilder como o valor da entidade `isOpen`, de modo que esteja disponível para o Aprendiz de Conversa. Observe o código a seguir:

![](../media/tutorial17_sessionstart.PNG)

Da mesma forma, o código em `OnSessionEndCallback` (se a sessão foi finalizada devido a uma atividade aprendida e não apenas um tempo limite) copia o valor da entidade `purchaseItem` para o armazenamento do BotBuilder `purchaseItem`. Observe o código a seguir:

![](../media/tutorial17_sessionend.PNG)

Agora vamos analisar as Ações.

## <a name="actions"></a>Ações

Observe que o modelo tem quatro ações.

As regras pretendidas para as ações são as seguintes:

- Se a entidade `isOpen` estiver configurada, o Bot perguntará "O que você gostaria de comprar?" e armazena isso no slot `puchaseItem`
- Se `isOpen` não estiver definido, o Bot dirá "Desculpe, estamos fechados"
- As outras duas ações são do tipo `END_SESSION`.
- A ação END_SESSION indica ao ConversationLearner que a conversa foi concluída.

### <a name="overall-bot-logic"></a>Lógica geral de bots

Primeiro, você verá que se o sinalizador `usingConversationLearner` do estado de Bot tiver sido definido, passamos o controle para o Aprendiz de Conversa. Se não, passamos o controle para outra ação.  Neste exemplo, estamos mostrando uma correspondência de texto simples, mas pode ser qualquer outra tecnologia de bot, incluindo LUIS, QnA Maker e até mesmo outra instância do Aprendiz de Conversa.

Precisamos de uma maneira para o usuário abrir e fechar a loja, então, fazemos uma comparação com "loja aberta" e "loja fechada" e definimos o sinalizador "storeIsOpen".

Em seguida, precisamos de uma maneira de disparar o controle de entrega para o nosso Modelo de Aprendiz de Conversa. Quando combinamos com a cadeia de caracteres "loja", fazemos o seguinte:
- Defina o sinalizador `usingConversationLearner` na memória do Bot.
- Chame o método "StartSession" no modelo de Aprendiz de Conversa.  Isso disparará o "onSessionStartCallback", que inicializará o valor da entidade `isOpen`

Veja abaixo:

![](../media/tutorial17_useConversationLearner.PNG)

Também fazemos uma correspondência de texto para "histórico", que exibirá esse último item de compra.
Finalmente, se mais alguma coisa for digitada, exibimos os comandos do usuário disponíveis

## <a name="train-dialog"></a>Diálogo de Treinamento

Para este tutorial, o modelo já é pré-treinado.  Vamos testar o bot completo para ver o efeito dos retornos de chamada da sessão inicial e final na prática.

## <a name="testing-the-bot"></a>Testar o Bot

Ao contrário dos bots de modelo de Aprendiz de Conversa único, não será possível testar isso na interface do Aprendiz de Conversa, já que ele só pode mostrar o que é tratado pelo Modelo de Aprendiz de Conversa.

### <a name="install-the-bot-framework-emulator"></a>Instalar o emulador de estrutura do Bot

- Vá para [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Baixe e instale o emulador.

### <a name="configure-the-emulator"></a>Configurar o emulador

- Abra o emulador e assegure-se de que a URL esteja direcionando a mesma porta em que o bot está executando. Probabilidade: `http://localhost:3978/api/messages`

### <a name="test"></a>Teste 

#### <a name="scenario-1-store-is-closed"></a>Cenário 1: a loja está fechada
1. Insira 'loja'. Isso é tratado pela correspondência de texto e dará controle ao modelo de Aprendiz de Conversa.
2. Insira “olá”.  Como o valor `isOpen` não está definido, o bot dirá "Desculpe, estamos fechados" e encerrará a sessão.

#### <a name="scenario-2-store-is-open"></a>Cenário 2: a loja está aberta
3. Insira 'loja aberta'.  Isso definirá o `isOpen` como verdadeiro.
4. Insira 'loja'.
5. Insira “olá”.  Como o valor `isOpen` está definido como verdadeiro, o bot dirá "O que você gostaria de comprar?"
6. Insira 'cadeira'. 'cadeira' será salva na memória CL como a entidade `purchaseItem`. O retorno de chamada da sessão final é chamado, copiando esse valor para o armazenamento de conversa.
7. Insira 'histórico'.  O bot dirá 'Você comprou a cadeira', pois esse foi o último `purchaseItem`.

## <a name="conclusion"></a>Conclusão

Com o que você aprendeu até agora, deverá ser capaz de combinar o Aprendiz de Conversa com qualquer outra tecnologia de Bot
