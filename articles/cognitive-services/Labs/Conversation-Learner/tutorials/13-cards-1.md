---
title: Como usar cartões com um modelo de Aprendiz de Conversa, parte 1 - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar cartões com o modelo de Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 988a2433f098f41bca4796299825293efd4de44b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171126"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Como usar cartões (parte 1 de 2)

Este tutorial mostra como adicionar e usar um cartão simples no seu bot.

> [!NOTE]
> Atualmente, o Aprendiz de Conversa espera que os arquivos de definição de cartão estejam localizados em um diretório chamado "cards", que está presente no diretório onde o bot é iniciado. Futuramente, isso será configurável.

## <a name="video"></a>Vídeo

[![Versão prévia do Tutorial 13](http://aka.ms/cl-tutorial-13-preview)](http://aka.ms/blis-tutorial-13)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

Os cartões são elementos de interface do usuário que permitem ao usuário selecionar uma opção na conversa. 

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista de modelos da interface do usuário da Web, clique em Tutorial-13-Cards-1. 

### <a name="the-card"></a>O cartão

A definição de cartão está no seguinte local: c:\<installedpath\>\src\cards\prompt.json.

O sistema espera encontrar suas definições de cartão neste diretório de cartões.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Observe o tipo de corpo `TextBlock` e o espaço reservado `{{question}}` no campo de texto.
> Há dois botões de enviar e o texto que é enviado para cada um.

### <a name="actions"></a>Ações

Nós criamos três ações. Como você pode ver abaixo, a primeira ação é um cartão.

![](../media/tutorial13_actions.PNG)

Vamos ver como o tipo de ação do cartão foi criado:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> A entrada de perguntas e os botões 1 e 2. Essas são as referências do modelo no cartão de onde você digita a pergunta e as respectivas respostas. Você também pode referenciar e usar entidades ou uma mistura de texto e entidades.

O ícone de olho mostra a aparência do cartão.

### <a name="train-dialog"></a>Caixa de diálogo de treinamento

Vamos examinar uma caixa de diálogo de ensino.

1. Clique em Caixas de Diálogo de Treinamento, em seguida, em Nova Caixa de Diálogo de Treinamento.
1. Digite ‘oi’.
2. Clique em Pontuar ação.
3. Clique para selecionar "O prompt vai para a esquerda ou direita".
    - Clicar em “esquerda” ou “direita” é equivalente ao usuário digitar “esquerda” “direita” respectivamente. 
4. Clique em Pontuar ações.
4. Clique para selecionar “esquerda”. Essa é uma ação sem espera.
6. Clique para selecionar "O prompt vai para a esquerda ou direita".
4. Clique em “direita”.
5. Clique em Pontuar ações.
3. Clique para selecionar “esquerda”
6. Clique para selecionar "O prompt vai para a esquerda ou direita".
4. Clique em Concluir o teste.

Agora você viu como funcionam os cartões. Eles são definidos no diretório de cartões como modelos de json. Os modelos serão exibidos na interface do usuário que você pode popular usando uma cadeia de caracteres ou uma entidade ou uma mistura de ambos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Cartões parte 2](./14-cards-2.md)
