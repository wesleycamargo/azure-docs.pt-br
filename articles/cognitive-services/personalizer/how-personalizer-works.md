---
title: Como funciona o Personalizador – Personalizador
titleSuffix: Azure Cognitive Services
description: O Personalizador usa o aprendizado de máquina para descobrir qual ação deve ser usada em um contexto. Cada loop de aprendizado tem um modelo que é treinado exclusivamente nos dados que você enviou para ele por meio de chamadas de Classificação e Recompensa. Cada loop de aprendizado é completamente independente entre si.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 6b2237f27fba5eaf952932cd6592052649400b96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025490"
---
# <a name="how-personalizer-works"></a>Como funciona o Personalizador

O Personalizador usa o aprendizado de máquina para descobrir qual ação deve ser usada em um contexto. Cada loop de aprendizado tem um modelo que é treinado exclusivamente nos dados que você enviou para ele por meio de chamadas de **Classificação** e **Recompensa**. Cada loop de aprendizado é completamente independente entre si. Crie um loop de aprendizado para cada parte ou comportamento do aplicativo que você deseja personalizar.

Para cada loop, **chame a API de Classificação**, baseado no contexto atual, com:

* Lista de possíveis ações: itens de conteúdo dos quais a ação principal será selecionada.
* Lista de [recursos de contexto](concepts-features.md): dados contextualmente relevantes, como usuário, conteúdo e contexto.

A API de **Classificação** decide usar:

* _Explorar_: O modelo atual para decidir a melhor ação com base em dados passados.
* _Examinar_: Selecione uma ação diferente em vez da ação principal.

A API de **Recompensa**:

* Coleta dados para treinar o modelo gravando os recursos e as pontuações de recompensa de cada chamada de classificação.
* Usa esses dados para atualizar o modelo com base nas configurações especificadas na _Política de Aprendizado_.

## <a name="architecture"></a>Arquitetura

A seguinte imagem mostra o fluxo de arquitetura das chamadas de Classificação e Recompensa:

![texto Alt](./media/how-personalizer-works/personalization-how-it-works.png "Como funciona a personalização")

1. O Personalizador usa um modelo interno de IA para determinar a classificação da ação.
1. O serviço decide se o modelo atual será explorado ou se novas opções para o modelo serão examinadas.  
1. O resultado de classificação é enviado ao EventHub.
1. Quando Personalizador receber a recompensa, a recompensa será enviada ao EventHub. 
1. A classificação e a recompensa são correlacionadas.
1. O modelo de IA é atualizado de acordo com os resultados de correlação.
1. O mecanismo de inferência é atualizado com o novo modelo. 

## <a name="research-behind-personalizer"></a>A pesquisa por trás do Personalizador

O Personalizador se baseia em ciência e pesquisa de ponta na área de [Aprendizado de Reforço](concepts-reinforcement-learning.md), incluindo artigos, atividades de pesquisa e áreas contínuas de exploração do Microsoft Research.

## <a name="terminology"></a>Terminologia

* **Loop de aprendizado**: É possível criar um loop de aprendizado para todas as partes de seu aplicativo que possam se beneficiar da personalização. Caso você tenha mais de uma experiência para personalização, crie um loop para cada uma. 

* **Ações**: Ações são os itens de conteúdo, como produtos ou promoções, à sua escolha. O Personalizador escolhe a ação principal a ser mostradas aos usuários, conhecida como a _ação de Recompensa_, por meio da API de Classificação. Cada ação pode enviar recursos com a solicitação de Classificação.

* **Context**: Para fornecer uma classificação mais precisa, forneça informações sobre seu contexto, por exemplo:
    * Seu usuário.
    * O dispositivo que ele está usando. 
    * A hora atual.
    * Outros dados sobre a situação atual.
    * Dados históricos sobre o usuário ou o contexto.

    Seu aplicativo específico pode ter informações de contexto diferentes. 

* **[Recursos](concepts-features.md)**: Uma unidade de informação sobre um item de conteúdo ou um contexto de usuário.

* **Recompensa**: Uma medida de como o usuário respondeu à ação retornada pela API de Classificação, como uma pontuação entre 0 e 1. O valor de 0 a 1 é definido pela lógica de negócios, com base em como a opção ajudou a atingir as metas de negócios de personalização. 

* **Exploração**: O serviço Personalizador faz uma exploração quando, em vez de retornar a melhor ação, escolhe uma ação diferente para o usuário. O serviço Personalizador evita desvios e estagnação e pode se adaptar ao comportamento contínuo do usuário com a exploração. 

* **Duração do Teste**: O tempo que o serviço Personalizador aguarda uma recompensa, a partir do momento em que ocorreu a chamada de Classificação para o evento.

* **Eventos Inativos**: Um evento inativo é aquele em que você chamou a Classificação, mas não tem certeza se o usuário verá o resultado, devido às decisões do aplicativo cliente. Os eventos inativos permitem criar e armazenar resultados de personalização e decidir como descartá-los posteriormente sem afetar o modelo de machine learning.

* **Modelo**: Um modelo do Personalizador captura todos os dados aprendidos sobre o comportamento do usuário, obtendo dados de treinamento da combinação dos argumentos enviados para as chamadas de Classificação e Recompensa e com um comportamento de treinamento determinado pela Política de Aprendizado. 

## <a name="next-steps"></a>Próximas etapas

Entenda [as situações em que você pode usar o Personalizador](where-can-you-use-personalizer.md).