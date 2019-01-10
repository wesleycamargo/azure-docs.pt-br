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
ms.openlocfilehash: 16381a9529f516dcb9ffd39022a658a7dd17ff57
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796468"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Como usar cartões (parte 1 de 2)

Este tutorial mostra como adicionar e usar um cartão simples no seu bot.

> [!NOTE]
> Atualmente, o Conversation Learner espera que os arquivos de definição de cartão estejam localizados em um diretório chamado "cards", que está presente no diretório onde o bot é iniciado.

## <a name="video"></a>Vídeo

[![Versão prévia do tutorial de cartões](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

Os cartões são elementos de interface do usuário que permitem ao usuário selecionar uma opção na conversa. 

### <a name="open-the-demo"></a>Abrir a demonstração

Na interface do usuário da Web clique em "Importar Tutoriais" e selecione o modelo nomeado "Tutorial-15-Cards".

### <a name="the-card"></a>O cartão

A definição do cartão está no seguinte local: C:\<installedpath\>\src\cards\prompt.json.

O sistema espera encontrar suas definições de cartão neste diretório de “cartões”.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Observe o tipo de corpo "TextBlock e o espaço reservado "{{question}}" no campo de texto.
> Há dois botões de enviar e o texto que é enviado para cada um.

### <a name="actions"></a>Ações

Nós criamos três ações. Como você pode ver abaixo, a primeira ação é um cartão.

![](../media/tutorial13_actions.PNG)

Vamos ver como o tipo de ação do cartão foi criado:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> O cartão contém três parâmetros diferentes - pergunta de entrada, botão 1 e 2. Esses elementos são referências de modelo no cartão em que você entra na questão e as respostas respectivas. Você também pode referenciar e usar entidades ou uma mistura de texto e entidades.

O ícone de olho mostra a aparência do cartão.

### <a name="practicing-creating-card-actions"></a>Praticar criação de ações de cartão

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
2. Selecione "CARTÃO" para o "Tipo de Ação."
3. Selecione "prompt" da lista “Modelo”.
4. No campo "pergunta", digite "Ir para a esquerda ou direita"
5. No campo "button1", digite "esquerda"
6. No campo "button2", digite "direita"
7. Clique no botão “Cancelar”.

### <a name="train-dialog-using-an-adaptive-card"></a>Caixa de diálogo de treinamento usando um cartão adaptável

1. No painel esquerdo, clique em "Treinar Diálogos" e, em seguida, no botão "Novo Diálogo de Treinamento".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "oi"
3. Clique no botão "Ações de Pontuação".
4. Selecione a resposta, "prompt: pergunta: Ir para esquerda ou direita?”
    - O ícone de olho pode ser usado para o cartão de visualização
5. No painel de bate-papo, clique no botão "Esquerda" no prompt de renderizado.
6. Clique no botão "Ações de Pontuação".
7. Selecione a resposta, “Esquerda”
8. Clique no botão "Salvar".
9. Selecione a resposta, "prompt: pergunta: Ir para esquerda ou direita?”
10. No painel de chat clique no botão “Direita” no prompt renderizado.
11. Clique no botão "Ações de Pontuação".
12. Selecione a resposta, “Direita”

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Bots híbridos](./16-hybrid-bots.md)
