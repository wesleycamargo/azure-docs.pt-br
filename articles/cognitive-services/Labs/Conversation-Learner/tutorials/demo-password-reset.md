---
title: Demonstração do Modelo do Aprendiz de Conversa, redefinição de senha - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como criar um modelo do Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bd0bcd79bb21dc3973b34086f6dad21b47a95c2f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240861"
---
# <a name="demo-password-reset"></a>Demonstração: redefinição de senha
Esta demonstração ilustra um bot de suporte técnico simples que pode ajudar na redefinição de senhas. 

Ela mostra como o Aprendiz de Conversa pode aprender fluxos de diálogo não triviais, sequências de vários turnos, incluindo uma classe fora do domínio. Esta demonstração não usa nenhum código ou entidades.

## <a name="video"></a>Vídeo

[![Demonstração da visualização de senha](https://aka.ms/cl-demo-password-preview)](https://aka.ms/blis-demo-password)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de redefinição de senha esteja em execução

    npm run demo-password

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista de modelos da interface do usuário da Web, clique em “Tutorial Demo Password Reset” (Tutorial de demonstração de redefinição de senha). 

### <a name="actions"></a>Ações

Criamos um conjunto de ações em que o usuário está procurando ajuda para a senha, incluindo soluções.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Diálogos de treinamento

Há vários diálogos de treinamento. Há também demonstrações de uma classe fora do domínio -- por exemplo, solicitações de usuários como 'instruções de direção' estão fora de domínio; o bot recebeu exemplos de algumas solicitações fora do domínio e pode responder com 'não posso ajudar com isso.'

![](../media/tutorial_pw_reset_entities.PNG)

Por exemplo, vamos tentar uma sessão de ensino.

1. Clique em “Train Dialogs” (Diálogos de treinamento) e, em seguida, em “New Train Dialog” (Novo diálogo de treinamento).
1. Insira “I lost my password” (Perdi minha senha).
2. Clique em “Score Action” (Pontuar Ação).
3. Clique para selecionar 'É para sua conta local ou conta Microsoft?'
4. Insira “Loca account” (Conta local).
5. Clique em “Score Actions” (Pontuar ações).
3. Clique para Selecionar “Which version of Windows do you have?” (Qual versão do Windows você tem?)
4. Insira “Windows 8’.
5. clique em “Score Actions” (Pontuar ações).
6. Selecione “SOLUTION: how to reset password on Windows 8” (SOLUÇÃO: como redefinir senha no Windows 8).
4. Clique em “Done Teaching” (Ensino concluído).

Vamos experimentar como o bot pode aprender uma aula fora do domínio.

1. Clique em “Train Dialogs” (Diálogos de treinamento) e, em seguida, em “New Train Dialog” (Novo diálogo de treinamento).
1. Insira “web search” (pesquisa na Web).
    - Este é um exemplo de classe fora do domínio. 
2. Clique em “Score Action” (Pontuar Ação).
3. Clique para Selecionar “'Sorry, I can't help with that” (Desculpe, não posso ajudar com isso).
    - Observe que a pontuação dessa opção está baixa no momento. Mas, depois de um pouco mais de ensino, a pontuação será maior.
4. Clique em “Done Teaching” (Ensino concluído).

Você já viu como criar uma demonstração básica de suporte técnico e como ela pode aprender a fornecer soluções, além de lidar com exemplos de consultas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Demonstração - pedido de pizza](./demo-pizza-order.md)
