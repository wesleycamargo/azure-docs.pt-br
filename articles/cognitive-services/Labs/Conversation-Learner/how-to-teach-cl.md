---
title: Como ensinar com um Aprendiz de conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Como ensinar com um Aprendiz de conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8c55bb27ce5a413c5ceae22371ad61a5acf47281
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322882"
---
# <a name="how-to-teach-with-conversation-learner"></a>Como ensinar com um Aprendiz de conversa 

Este documento explica os sinais que Aprendiz de conversa compreende e descreve como ele aprende.  

O ensino pode ser decomposto em duas etapas distintas: Extração de entidade e a Seleção da ação.

## <a name="entity-extraction"></a>Extração de entidade

Nos bastidores, o Aprendiz de conversa usa o [LUIS](https://www.luis.ai) para extrair a entidade.  Se você estiver familiarizado com o LUIS, essa experiência aplica-se à extração de entidade no Aprendiz de conversa.

Os modelos de extração de entidade estão cientes do *conteúdo* e do *contexto* dentro de uma declaração do usuário.  Por exemplo, se a palavra "Rio de Janeiro" foi rotulada como cidade em uma declaração como "Como está o clima no Rio de Janeiro", a extração de entidade será capaz de reconhecer esse mesmo conteúdo ("Rio de Janeiro") como uma cidade em outra declaração como "População do Rio de Janeiro", mesmo que as declarações sejam muito diferentes.  Por outro lado, se "Vinícius" tiver sido reconhecido como um nome em "Agendar uma reunião com Vinícius", um novo nome nunca visto anteriormente poderá ser reconhecido em um contexto semelhante, como "Marcar uma reunião com Marcos".  O aprendizado de máquina infere quando se trata de conteúdo, contexto ou ambos, tendo como base exemplos de treinamento.

No momento, a extração de entidade só está ciente do conteúdo da declaração atual.  Ao contrário da seleção da ação (abaixo), a extração não está ciente do histórico do diálogo, como vezes anteriores do sistema, vezes anteriores de usuários ou entidades reconhecidas anteriormente.  Como resultado, o comportamento da extração de entidade é "compartilhado" em todas as declarações.  Por exemplo, se a declaração do usuário "Eu quero Guaraná" tiver "Guaraná" rotulado como tipo de entidade "Fruto" em uma declaração do usuário, o modelo de extração de entidade esperará que essa declaração ("Eu quero Guaraná") sempre tenha "Guaraná" rotulado como "Fruto".

Se extração de entidade não estiver se comportando conforme o esperado, veja as possíveis soluções:

- A primeira coisa a tentar é adicionar mais exemplos de treinamento, particularmente exemplos que revelam o contexto de entidade típico (palavras adjacentes) ou exceções
- Considere adicionar uma propriedade "Entidade Esperada" a uma ação, se apropriado.  Confira o tutorial sobre Entidades Esperadas para ver mais detalhes.
- Embora seja possível adicionar processamento manual a `EntityExtractionCallback` para extrair entidades usando código, essa é a abordagem menos recomendada, pois não se beneficiará de aprimoramentos no aprendizado de máquina à medida que seu sistema amadurece.

## <a name="action-selection"></a>Seleção da ação

A seleção da ação usa uma rede neural recorrente que recebe como entrada todo o histórico de conversas.  Assim, a seleção da ação é um processo com estado que está ciente das declarações anteriores do usuário, dos valores da entidade e das declarações do sistema.  

Alguns sinais são naturalmente preferidos pelo processo de aprendizado.  Em outras palavras, se o Aprendiz de conversa puder explicar uma decisão de seleção de ação usando sinais "mais preferenciais", ele assim o fará. Se não for possível, ele usará sinais "menos preferenciais".

Veja uma tabela mostrando todos os sinais no Aprendiz de conversa e quais são usados pela seleção da ação.  Observe que a ordem de palavras nas declarações do usuário é ignorada.

Sinal | Preferência (1=mais preferencial) | Observações
--- | --- | --- 
Ação do sistema na vez anterior | 1 | 
Entidades presentes no momento atual | 1 | 
Se esta é a primeira vez | 1 |
Correspondência exata de palavras na declaração do usuário atual | 2 | 
Palavras de significado semelhante na declaração do usuário atual | 3 | 
Ações do sistema antes da vez anterior | 4 |
Entidades presentes nas vezes anteriores ao momento atual | 4 | 
Declarações de usuários anteriores ao momento atual | 5 | 

> [!NOTE]
> Seleção de ação não considera o conteúdo das ações do sistema, ou seja, o texto, o conteúdo do cartão ou o nome ou comportamento da API, apenas a identidade da ação do sistema.  Como resultado, alterar o conteúdo de uma ação não alterará o comportamento do modelo de seleção da ação.
>
> Além disso, que o conteúdo/valores de entidades não são usados, somente sua presença/ausência.

Se seleção da ação não estiver se comportando conforme o esperado, veja as possíveis soluções:

- Acrescente mais diálogos de treinamento, particularmente diálogos que ilustrem os sinais para os quais a seleção de ações deve estar atenta.  Por exemplo, se a seleção da ação deve preferir um sinal em detrimento de outro, ofereça exemplos que mostrem que o sinal preferido está no mesmo estado e que os outros sinais variam.  Algumas sequências dependem de muitos diálogos de treinamento para aprender.
- Adicione entidades "Obrigatórias" e "Desqualificadoras" às definições da ação.  Isso limita quando as ações estão disponíveis e pode ser útil para expressar regras de negócio e alguns padrões de senso comum. 

## <a name="updates-to-models"></a>Atualizações para modelos

Sempre que você adiciona ou edita uma entidade, ação ou caixa de diálogo de treinamento na interface do usuário, isso gera uma solicitação para treinar novamente o modelo de extração de entidade e o modelo de seleção da ação.  Essa solicitação é colocada em uma fila e um novo treinamento é executado de forma assíncrona.  Quando um novo modelo está disponível, ele é usado a partir desse ponto para a extração de entidade e seleção da ação.  Esse processo de repetição do treinamento geralmente leva cerca de 5 segundos, mas pode ser mais demorado se o modelo for complexo ou se a carga no serviço de treinamento for alta.

Como o treinamento é feito de forma assíncrona, é possível que as edições feitas por você não sejam imediatamente refletidas.  Se a extração de entidade ou seleção da ação não estiverem se comportando conforme o esperado com base nas alterações feitas nos últimos 5 a 10 segundos, essa pode ser a causa.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Limites e valores padrão](./cl-values-and-boundaries.md)
