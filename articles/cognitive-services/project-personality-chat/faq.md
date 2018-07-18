---
title: Perguntas frequentes sobre Chat de Personalidade – Serviços Cognitivos do Azure | Microsoft Docs
description: Perguntas frequentes sobre Chat de Personalidade
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.topic: faq
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: 5304b25672f9951e004876dfd19af75ea7d86502
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364766"
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>Por que isso não responde todas as perguntas que eu faço, como outros chat bots?

O Projeto Chat de Personalidade melhorará um bot com conversa casual comum que demonstra personalidade e cria uma experiência de usuário mais completa. Ele não é projetado para realizar conversas longas sobre tópicos que não são relevantes para a função principal do bot. Embora ele pudesse responder a todas as conversas, ele foi restringindo, na versão beta, a domínios de conversa casual comum.

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>Como posso personalizar a personalidade de acordo com a minha marca?

Selecione a persona mais próxima entre as personas padrão disponíveis. Atualmente, você pode pegar a biblioteca editorial e editar as respostas para que atendam melhor à sua marca. No futuro, você poderá fazer upload de um conjunto de enunciados de amostra de sua personalidade escolhida e encontrar a versão de ID da persona mais próxima. Também há maneiras para treinar novamente e personalizar o modelo.

## <a name="is-this-service-powering-existing-intelligent-agents-such-as-zo"></a>Esse serviço está habilitando agentes inteligentes atuais, como Zo?

Os serviços que habilitam Zo, Cortana e Projeto Chat de Personalidade compartilham de algumas técnicas semelhantes, mas são pilhas diferentes. Eles incorporaram lições aprendidas de experiências com Zo e Cortana.

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>Esse serviço pode levar a más experiências do cliente?

Para fornecer uma experiência mais avançada, o Chat de Personalidade pode gerar respostas além daquelas no conjunto de dados editorial, e tentará interpretar todas as entrada do usuário. Portanto, é possível que uma resposta não pareça estar correta no contexto. Vários controles foram estabelecidos para ajudar a evitar respostas desfavoráveis, aproveitando o conhecimento de agentes inteligentes como Zo. Por padrão, o Projeto Chat de Personalidade é definido para responder apenas a intenções de usuário reconhecidas. Você talvez queira testar se o Projeto Chat de Personalidade é adequado para as suas circunstâncias. Seus comentários serão bem-vindos se você encontrar algo que precise de mais treinamento. Se você usar esse serviço com seus clientes no futuro, é recomendável considerar registro em log anônimo para ajudá-lo a identificar problemas com interações com o usuário em tempo real.