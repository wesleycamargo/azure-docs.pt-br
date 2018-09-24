---
title: O que é QnA Maker?
titleSuffix: Azure Cognitive Services
description: O QnA Maker permite ter um serviço perguntas e respostas a partir do seu conteúdo semi-estruturado como documentos de perguntas frequentes ou URLs e manuais de produtos.
services: cognitive-services
author: tulasim88
manager: cjgronlund
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: overview
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 08f3b6424d94195d2606e4cdb9be470e2f130909
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985803"
---
# <a name="what-is-qna-maker"></a>O que é QnA Maker?

O [QnA Maker](https://qnamaker.ai) permite ter um serviço perguntas e respostas a partir do seu conteúdo semi-estruturado como documentos de perguntas frequentes ou URLs e manuais de produtos. Você pode criar um modelo de perguntas e respostas que é flexível para consultas de usuário, fornecendo respostas que você vai treinar um bot para usar de maneira natural e coloquial.

Uma interface gráfica do usuário fácil de usar permite que você crie, gerencie, treine e coloque o seu serviço em funcionamento sem qualquer experiência do desenvolvedor.

![Visão geral](../media/qnamaker-overview-learnabout/overview.png)

## <a name="highlights"></a>Destaques

- Uma experiência completa **sem código** para [criar um bot de perguntas frequentes](https://aka.ms/qnamaker-docs-create-faqbot).
- **Sem limitação de rede**. Pague para hospedar o serviço e não pelo número de transações. Consulte a [página de preços](https://aka.ms/qnamaker-docs-pricing) para obter mais detalhes.
- **Escala de acordo com suas necessidades**. Escolha os SKUs apropriados dos componentes individuais que atendam ao seu cenário. Veja como [escolher a capacidade](https://aka.ms/qnamaker-docs-capacity) para o seu serviço do QnA Maker.
- **Total conformidade dos dados**. Os componentes de tempo de execução e dados são implantados na assinatura do Azure do usuário e dentro de seu limite de conformidade. Leia abaixo para obter mais detalhes.

## <a name="key-qna-maker-processes"></a>Principais processos do QnA Maker

Um QnA Maker fornece dois serviços essenciais para seus dados:

* **Extração**: Dados estruturados perguntas e respostas são extraídos de fontes de dados semi-estruturadas como perguntas frequentes e manuais de produtos. Essa extração é feita ao criar a base de dados de conhecimento. Crie sua base de dados de conhecimento [aqui](https://aka.ms/qnamaker-docs-createkb).

* **Correspondência**: Depois de sua base de dados de conhecimento ter sido [treinada e testada](https://aka.ms/qnamaker-docs-trainkb), você a [publica](https://aka.ms/qnamaker-docs-publishkb). Isso permite um ponto de extremidade para sua base de dados de conhecimento do QnA Maker, que você pode usar em seu aplicativo ou bot. Esse ponto de extremidade aceita uma pergunta de usuário e responde com a melhor correspondência de pergunta/resposta na base de dados de conhecimento, juntamente com uma pontuação de confiança para a correspondência.

## <a name="qna-maker-architecture"></a>Arquitetura do QnA Maker

A pilha do QnA Maker consiste das seguintes partes:

1. **Serviços de gerenciamento do QnA Maker (plano de controle)**: A experiência de gerenciamento para uma base de dados de conhecimento do QnA Maker, que inclui a criação inicial, atualização, treinamento e publicação. Essas atividades podem ser feitas por meio do [portal](https://qnamaker.ai) ou das [APIs de gerenciamento](https://aka.ms/qnamaker-v4-apis). Os serviços de gerenciamento se comunicam com o componente de tempo de execução abaixo.

2. **Tempo de execução do QnA Maker (plano de dados)**: Os dados e o tempo de execução são implantados na assinatura do Azure do usuário em uma região de sua escolha. O conteúdo de pergunta/resposta do cliente é armazenado no [Azure Search](https://azure.microsoft.com/services/search/), e o tempo de execução é implantado como um [Serviço de aplicativo](https://azure.microsoft.com/services/app-service/). Opcionalmente, você também pode optar por implantar um recurso do [Application Insights](https://azure.microsoft.com/services/application-insights/) para análise.

![Arquitetura](../media/qnamaker-overview-learnabout/architecture.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um serviço do QnA Maker](../how-to/set-up-qnamaker-service-azure.md)
