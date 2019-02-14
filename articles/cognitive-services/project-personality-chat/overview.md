---
title: O que é o Chat de Personalidade do Projeto?
titlesuffix: Azure Cognitive Services
description: Este artigo é uma visão geral do Azure Project Personality Chat, uma API baseada em nuvem para aprimorar a capacidade de conversa do seu bot.
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: c7f7a8c65717acd5a19e92b7e0437dc4b8628909
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867079"
---
# <a name="what-is-project-personality-chat"></a>O que é o Chat de Personalidade do Projeto?

O Project Personality Chat aprimora as capacidades de conversa do seu bot, pois lida com a conversa em consonância com uma personalidade distinta escolhida. O Personality Chat usa classificadores de intenção para identificar as intenções comuns da conversa e gera respostas consistentes com uma personalidade. Com base nas pontuações de intenção e confiança, o bot escolhe a melhor resposta em uma base editorial coletada ou gera uma em tempo real usando DNNs (redes neurais profundas). É possível escolher entre três personas padrão. O modelo de persona devolve respostas que são mais semelhantes à personalidade escolhida.

Um conjunto editorial personalizável para consultas comuns de conversas está disponível. Ele pode ser facilmente integrado usando o SDK do Microsoft Bot Framework. Esse SDK economiza o tempo e o custo de escrevê-los do zero.

## <a name="getting-started-with-project-personality-chat"></a>Introdução ao Project Personality Chat

Você pode visitar a página de laboratórios do Project Personality Chat e conversar com a demonstração disponível, além de solicitar acesso antecipado quando o serviço for disponibilizado.
Hoje, também pode integrar a biblioteca somente editorial personalizável nos seus bots por meio do SDK do Microsoft Bot Framework. <br>
[Exemplos: Integrar o Personality Chat em um bot](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[Experimente a biblioteca do Personality Chat](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>Geração de respostas usando redes neurais

O Personality Chat usa modelos de aprendizado profundo para aprender padrões de conversa geral e gerar respostas. O modelo de geração de resposta é uma RNN (rede neural recorrente). Esse modelo é treinado com milhões de conversas, em que entende e aprende padrões de interações humanas. Com os padrões de estrutura de sentença aprendidos, novas consultas são formadas e respostas são geradas. Ao gerar essa nova resposta, o modelo pesquisa em um "vocabulário de gravação" de palavras e escolhe um número x de primeiras palavras adequadas para a resposta. Usando a pesquisa de transmissão, ele continua pesquisando um número x de segundas palavras adequadas para cada uma das primeiras palavras geradas. Uma resposta é considerada completa quando o modelo escolhe a palavra EOS ("fim da sentença"). Quando tem todas as respostas, ele escolhe um número x de respostas adequadas com base na pontuação de probabilidade para a resposta completa.

O modelo aprende a gerar sequências contextualmente apropriadas que tenham a "estrutura" certa. Por exemplo, uma pergunta como “Você quer conversar agora?” diz bastante sobre a estrutura de uma resposta plausível: provavelmente começará com alguma paráfrase de “sim” ou “não”, e o pronome provavelmente será “eu”. Existe uma probabilidade maior de uma resposta “não” incluir uma explicação educada e assim por diante.

O sistema tenta aprender um modelo de linguagem para a estrutura básica de conversa. Essa estrutura deve permitir que as respostas sejam influenciadas, em parte, por restrições externas, como tópicos, personalidade e assim por diante.  Como essas restrições são adquiridas de padrões amplos, são adequadas para aplicação em conversa (mas não se limitam a isso).

![Modelo de sequência a sequência para geração de resposta](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>Modelagem de personalidade

 Com qualquer modelo de conversa controlado por dados, há o desafio de fornecer uma "personalidade" coerente de forma consistente. A PERSONA é definida como o personagem encontrado durante interações de conversa. Uma persona pode ser composta por elementos de identidade, comportamento linguístico e estilo de interação. Na versão atual do Personality Chat, o enfoque está no comportamento linguístico e no estilo de interação.

Esse modelo representa cada orador individual como um vetor ou inserção. Ele codifica informações do orador que influenciam o conteúdo e o estilo de suas respostas. Em seguida, o modelo aprende as representações do orador com base no conteúdo da conversa fornecido por diferentes oradores. Os oradores com respostas semelhantes tendem a ter incorporações semelhantes, ocupando posições próximas no espaço de vetor. Dessa forma, os dados de treinamento dos oradores próximos no espaço de vetor ajudam a aumentar a capacidade de generalização do modelo de orador. O modelo efetivamente agrupa oradores que têm representações semelhantes no espaço de vetor para formar um cluster de persona, com uma "ID de persona".

Para as personas padrão, atributos e respostas coletadas são usados a fim de encontrar o cluster de oradores com a correspondência mais próxima. Em seguida, esse cluster é escolhido como ID de persona para cada uma das personalidades padrão. A personalização contínua pode acontecer para qualquer tipo de personalidade, pegando um conjunto de respostas de bot/marca. Depois, são criadas conversas que emulam com precisão a persona do indivíduo, como o comportamento linguístico de resposta e outras características principais.

![Modelagem de persona usando clusters de oradores](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>Reconhecimento da intenção da conversa

O Personality Chat tem classificadores de intenção para assegurar uma resposta às intenções das conversas. Esses classificadores não interferirão nas tarefas ou consultas de informações. Um classificador de bate-papo de alto nível determina se a consulta tem intenção de conversa. Ele decide usando classificadores de base lexical e semântica e combinando suas pontuações. Essas intenções são treinadas usando dados de conversa (como amostras de intenção positiva) e consultas de pesquisa/tarefa (para amostras de intenção negativa).

Outros classificadores de intenção secundária são usados para identificar as subclasses da conversa a fim de restringir os tipos de conversa a que o serviço responde; por exemplo: saudações, cumprimentos, opiniões e assim por diante. Esses classificadores de aprendizado profundo, usando o CDSSM (modelo semântico de estrutura profunda convolucional), convertem todas as consultas em vetores. São treinados usando dezenas de milhares de consultas estruturadas para as intenções secundárias. Em seguida, o classificador corresponde uma consulta às classes de intenção identificadas existentes, encontrando a correspondência mais próxima no espaço de vetor.

Vários controles foram estabelecidos para ajudar a evitar respostas desfavoráveis, aproveitando o conhecimento de agentes inteligentes como Zo. Por padrão, o Projeto Chat de Personalidade é definido para responder apenas a intenções de usuário reconhecidas. Você talvez queira testar se o Projeto Chat de Personalidade é adequado para as suas circunstâncias. Seus comentários serão bem-vindos se você encontrar algo que precise de mais treinamento.
