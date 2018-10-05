---
title: Perguntas frequentes – QnA Maker
titleSuffix: Azure Cognitive Services
description: Lista de perguntas frequentes sobre o serviço do QnA Maker
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: efefd595c43d7f46ff1ead91577d070cf8fb90e4
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47164609"
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Por que minhas URLs/arquivos não estão extraindo pares de pergunta-resposta?

É possível que o QnA Maker não possa extrair automaticamente algum conteúdo de QnA (perguntas e respostas) de URLs de perguntas frequentes válidas. Nesses casos, você pode colar o conteúdo de QnA em um arquivo .txt e verificar se a ferramenta pode ingeri-lo. Como alternativa, você pode adicionar o conteúdo de modo editorial à sua base de dados de conhecimento.

## <a name="how-large-a-knowledge-base-can-i-create"></a>Qual o tamanho da base de dados de conhecimento que posso criar?

O tamanho da base de dados de conhecimento depende do SKU da pesquisa do Azure que você escolhe ao criar o serviço QnA Maker. Leia [este texto](./Tutorials/choosing-capacity-qnamaker-deployment.md) para obter mais detalhes.

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>Por que eu não vejo algo na lista suspensa ao tentar criar uma nova base de dados de conhecimento?

Você ainda não criou serviços de QnA Maker no Azure. Leia [este texto](./How-To/set-up-qnamaker-service-azure.md) para saber como fazer isso.

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>Como compartilha uma base de conhecimento com outras pessoas?

O compartilhamento funciona no nível do serviço QnA Maker, ou seja, todas as bases de conhecimento nos serviços serão compartilhadas. Leia [este texto](./How-To/collaborate-knowledge-base.md) sobre como colaborar em uma base de dados de conhecimento.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Como alterar a mensagem padrão quando nenhuma correspondência boa for encontrada?

A mensagem padrão faz parte das configurações em seu serviço de Aplicativo.
- Vá até o recurso do seu serviço de Aplicativo no Portal do Azure

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Clique na opção **Configurações**

![qnamaker appservice settings](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Alterar o valor da configuração **DefaultAnswer**
- Reinicie o serviço Aplicativo

![qnamaker appservice restart](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Por que meu link do SharePoint não está sendo extraído?

A ferramenta analisa apenas as URLs públicas e não oferece suporte a fontes de dados autenticados no momento. Como alternativa, você pode baixar o arquivo e usar a opção de carregamento de arquivo para extrair perguntas e respostas.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>As atualizações que eu fiz em minha base de dados de conhecimento não são refletidas na publicação. Por que não?

Todas as operações de edição, seja em atualização de tabela, teste ou configurações, precisam ser salvas antes de serem publicadas. Clique no botão Salvar e treinar após cada operação de edição.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>Quando devo atualizar minhas chaves de ponto de extremidade?

Você deverá atualizar suas chaves de ponto de extremidade se suspeitar que elas foram comprometidas.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>A base de dados de conhecimento oferece suporte a dados avançados ou multimídia?

A base de dados de conhecimento dá suporte ao Markdown. No entanto, a extração automática das URLs limitou a capacidade de conversão de HTML para Markdown. Se quiser usar o Markdown completo, você poderá modificar seu conteúdo diretamente na tabela ou fazer o upload de uma base de dados de conhecimento com o conteúdo avançado.

Não há suporte para multimídia, como imagens e vídeos, no momento.

## <a name="does-qna-maker-support-non-english-languages"></a>O QnA Maker dá suporte para idiomas além do inglês?

Veja mais detalhes sobre os [idiomas com suporte](./Overview/languages-supported.md).

Se você tiver conteúdo de vários idiomas, crie um serviço separado para cada idioma.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Preciso usar o Bot Framework para usar o QnA Maker?

Não, você não precisa usar o Bot Framework com o QnA Maker. No entanto, o QnA Maker é oferecido como um dos vários modelos no Serviço de Bot do Azure. O Serviço de Bot permite o desenvolvimento rápido de bots inteligentes por meio da Estrutura de Bot da Microsoft, e é executado em um ambiente sem servidor.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>Como criar um bot com QnA Maker?

Siga as instruções [nessa](./Tutorials/create-qna-bot.md) documentação para criar o Bot com Bot do Azure.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Como posso inserir o serviço QnA Maker no meu site?

Execute as etapas para inserir o serviço QnA Maker como um controle de webchat em seu site:

1. Crie seu bot de perguntas frequentes seguindo as instruções [aqui](./Tutorials/create-qna-bot.md).
2. Habilite o chat da Web executando [estas](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) etapas


