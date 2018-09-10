---
title: Como usar chamadas à API com um modelo de Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar chamadas à API com um modelo de Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1d4013d736d8cfcb75874bc0c86d20b86ab4dd62
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215832"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Como adicionar chamadas à API a um modelo Aprendiz de Conversa

Este tutorial mostra como adicionar chamadas à API ao modelo. Chamadas de API são funções que você define e grava em seu bot, e que podem ser chamadas pelo Aprendiz de Conversa.

## <a name="video"></a>Vídeo

[![Versão prévia do Tutorial 12](http://aka.ms/cl-tutorial-12-preview)](http://aka.ms/blis-tutorial-12)

## <a name="requirements"></a>Requisitos
O tutorial exige que o bot "tutorialAPICalls.ts" esteja em execução.

    npm run tutorial-api-calls

## <a name="details"></a>Detalhes

- As chamadas de API podem ler e manipular entidades.
- As chamadas de API têm acesso ao objeto do gerenciador de memória.
- As chamadas de API também podem receber argumentos. Isso permite a reutilização da mesma chamada de API para finalidades diferentes.

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista Modelo da interface do usuário da Web, clique em Tutorial-12-APICalls. 

### <a name="entities"></a>Entidades

Nós definimos uma entidade no modelo chamado número.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Chamadas à API
O código para as chamadas de API é definido neste arquivo: c:\<installedpath\>\src\demos\tutorialAPICalls.ts.

![](../media/tutorial12_apicalls.PNG)

- O primeiro retorno de chamada à API é RandomGreeting. Retorna uma saudação aleatória definida na variável de saudação.
- O retorno de chamada à API de Multiplicar: multiplicará dois números fornecidos pelo usuário. Depois, retorna o resultado da multiplicação de dois números. Isso mostra que os retornos de chamada à API podem receber entradas. Observe que o gerenciador de memória é o primeiro argumento. 
- O retorno de chamada à API ClearEntities: limpa a entidade numérica para permitir que o usuário insira o próximo número. Isso ilustra como as chamadas à API podem manipular entidades.

### <a name="actions"></a>Ações

Criamos quatro ações. 

![](../media/tutorial12_actions.PNG)

- Além de "Qual número você deseja multiplicar por 12?" que é uma ação acessível para comunicações, há três chamadas à API diferentes que ilustram os padrões de chamada à API comuns.

- RandomGreeting: é uma ação de não espera. Para definir isso, em Criar Diálogo de Ação, selecionamos o Tipo de Ação de API_LOCAL, depois selecionamos RandomGreeting. 

![](../media/tutorial12_setupapicall.PNG)

O botão atualizar ao lado da API será usado se precisarmos parar o bot e fazer alterações nas APIs. Ao clicar em atualizar, as alterações mais recentes serão selecionadas.

Veja aqui como criamos a ação de Multiplicação: depois de selecionar API_Local e API, inserimos uma entidade ($number) para o primeiro valor de entrada (num1string) e um valor (12) para o segundo valor de entrada (num2string). Isso fornece um nível de indireção entre o bot e as chamadas à API, portanto, o mesmo retorno de chamada pode ser mapeado para algumas ações no sistema e diferem em como as ações são atribuídas.

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>Diálogo de Treinamento

Vamos examinar uma caixa de diálogo de ensino.

1. Clique em Caixas de Diálogo de Treinamento, em seguida, em Nova Caixa de Diálogo de Treinamento.
1. Digite ‘oi’.
2. Clique em Ação de Pontuação.
3. Clique para selecionar RandomGreeting. Isso executará a chamada à API de Saudação Aleatória.
3. Clique para selecionar "Que número você deseja multiplicar por 12?'
4. Insira "8". Depois, clique em Ações de Pontuação.
4. Selecione "Multiplicar $number 12". Anote o resultado da multiplicação.
5. Selecione "Limpar Entidades".
    - O valor da entidade `number` foi apagado.
3. Clique para selecionar "Que número você deseja multiplicar por 12?'
4. Clique em Concluir Teste.

![](../media/tutorial12_dialog.PNG)

Agora você viu como registrar retornos de chamada à API, os padrões comuns e como definir argumentos e associar valores e entidades neles.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Cartões parte 1](./13-cards-1.md)
