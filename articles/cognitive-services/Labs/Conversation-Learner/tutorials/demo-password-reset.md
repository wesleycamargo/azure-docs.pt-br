---
title: Aplicativo de demonstração de Aprendiz de Conversa, redefinição de senha - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como criar um aplicativo de demonstração para Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 24d61787a79ee1a1a9737c417aa966cc8fd75930
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364222"
---
# <a name="demo-password-reset"></a>Demonstração: redefinição de senha
Esta demonstração ilustra um bot de suporte técnico simples que pode ajudar na redefinição de senhas. 

Ela mostra como o Aprendiz de Conversa pode aprender fluxos de diálogo não triviais, sequências de vários turnos, incluindo uma classe fora do domínio. Esta demonstração não usa nenhum código ou entidades.

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de redefinição de senha esteja em execução

    npm run demo-password

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista de aplicativos da interface do usuário da Web, clique no Tutorial de demonstração de redefinição de senha. 

### <a name="actions"></a>Ações

Criamos um conjunto de ações em que o usuário está procurando ajuda para a senha, incluindo soluções.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Diálogos de treinamento

Há vários diálogos de treinamento. Há também demonstrações de uma classe fora do domínio -- por exemplo, solicitações de usuários como 'instruções de direção' estão fora de domínio; o bot recebeu exemplos de algumas solicitações fora do domínio e pode responder com 'não posso ajudar com isso.'

![](../media/tutorial_pw_reset_entities.PNG)

Por exemplo, vamos tentar uma sessão de ensino.

1. Clique em Diálogos de Treinamento e, em seguida, em Novo Diálogo de Treinamento.
1. Insira 'Perdi minha senha'.
2. Clique em Pontuar Ação.
3. Clique para selecionar 'É para sua conta local ou conta Microsoft?'
4. Insira 'Conta local'.
5. Clique em Pontuar Ações.
3. Clique para Selecionar 'Qual versão do Windows você tem?'
4. Insira 'Windows 8'.
5. clique em Pontuar Ações.
6. Selecione 'SOLUÇÃO: como redefinir senha no Windows 8.'
4. Clique em Ensino Concluído.

Vamos experimentar como o bot pode aprender uma aula fora do domínio.

1. Clique em Diálogos de Treinamento e, em seguida, em Novo Diálogo de Treinamento.
1. Insira 'pesquisa na Web'.
    - Este é um exemplo de classe fora do domínio. 
2. Clique em Pontuar Ação.
3. Clique para Selecionar 'Desculpe, não posso ajudar com isso.'
    - Observe que a pontuação dessa opção está baixa no momento. Mas, depois de um pouco mais de ensino, a pontuação será maior.
4. Clique em Ensino Concluído.

Você já viu como criar uma demonstração básica de suporte técnico e como ela pode aprender a fornecer soluções, além de lidar com exemplos de consultas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Demonstração - pedido de pizza](./demo-pizza-order.md)
