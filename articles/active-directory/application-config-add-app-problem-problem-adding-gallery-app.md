---
title: Problema ao adicionar um aplicativo da Galeria do Azure AD | Microsoft Docs
description: "Entenda os problemas comuns que as pessoas enfrentam durante a adição de aplicativos da Galeria do Azure AD e o que você pode fazer para resolvê-los"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: b429f5fc1be24e63b0e9e065f879f0b0e389403c
ms.contentlocale: pt-br
ms.lasthandoff: 04/11/2017

---

<a id="problem-adding-an-azure-ad-gallery-application" class="xliff"></a>

# Problema ao adicionar um aplicativo da Galeria do Azure AD

Este artigo ajuda você a entender os problemas comuns que as pessoas enfrentam durante a adição de aplicativos da Galeria do Azure AD e o que você pode fazer para resolvê-los.

<a id="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear" class="xliff"></a>

## Cliquei no botão "adicionar" e meu aplicativo demorou muito tempo para aparecer

Em algumas circunstâncias, pode demorar de 1 ou 2 minutos (às vezes mais) para um aplicativo aparecer após adicioná-lo ao diretório. Embora esse não seja o desempenho normal esperado, você pode ver que a adição do aplicativo está em andamento clicando no ícone **Notificações** (o sino) no canto superior direito do [Portal do Azure](https://portal.azure.com/) e procurando uma notificação **Em Andamento** ou **Concluído** rotulada **Criar aplicativo.**

Se seu aplicativo nunca for adicionado, ou você encontrar um erro ao clicar no botão **Adicionar**, você verá uma **Notificação** em um estado **Erro**. Se você quiser mais detalhes sobre o erro para saber mais ou compartilhar com um engenheiro de suporte, você poderá ver mais informações sobre o erro seguindo as etapas na seção [Como ver os detalhes de uma notificação do portal](#how-to-see-the-details-of-a-portal-notification).

<a id="i-clicked-the-add-button-and-my-application-didnt-appear" class="xliff"></a>

## Cliquei no botão "adicionar" e meu aplicativo não apareceu

Às vezes, devido a problemas temporários, problemas de rede ou um bug, a adição de um aplicativo falha. Você pode saber se isso aconteceu ao clicar no ícone **Notificações** (o sino) na parte superior direita do Portal do Azure e ver um ícone vermelho (!) ao lado de sua notificação **Criar aplicativo**. Isso indica que houve um erro ao criar o aplicativo.

Se você encontrar um erro ao clicar no botão **Adicionar**, você verá uma **Notificação** em um estado **Erro**. Se você quiser mais detalhes sobre o erro para saber mais ou compartilhar com um engenheiro de suporte, você poderá ver mais informações sobre o erro seguindo as etapas na seção [Como ver os detalhes de uma notificação do portal](#how-to-see-the-details-of-a-portal-notification).

<a id="i-dont-know-how-to-set-up-my-application-once-ive-added-it" class="xliff"></a>

 ## Não sei configurar meu aplicativo depois de adicioná-lo

Se você precisar de ajuda para aprender sobre aplicativos, o artigo [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) é um bom lugar para começar.

Além disso, a [Biblioteca de documentos de aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) ajuda você a saber mais sobre logon único com o Azure AD e como ele funciona.

<a id="how-to-see-the-details-of-a-portal-notification" class="xliff"></a>

## Como ver os detalhes de uma notificação no portal

Veja os detalhes de qualquer notificação do portal executando as etapas abaixo:

1.  Clique no ícone **Notificações** (o sino) na parte superior direita do Portal do Azure

2.  Selecione qualquer notificação em um estado **Erro** (aqueles com um (!) vermelho ao lado deles).

    >[!NOTE]
    >Não é possível clicar em notificações em um estado **Êxito** ou **Em Andamento**.
    >
    >

3.  Isso abre a folha **Detalhes da Notificação**.

4.  Use essas informações para saber mais detalhes sobre o problema.

5.  Se você ainda precisar de ajuda, também poderá compartilhar essas informações com um engenheiro de suporte ou com o grupo de produtos para obter ajuda com o problema.

6.  Clique no **ícone** **cópia** à direita da caixa de texto **Erro de cópia** para copiar todos os detalhes de notificação para compartilhar com um engenheiro de suporte ou de grupo de produtos

<a id="how-to-get-help-by-sending-notification-details-to-a-support-engineer" class="xliff"></a>

## Como obter ajuda enviando detalhes de notificação a um engenheiro de suporte

É muito importante que você compartilhe **todos os detalhes abaixo** com um engenheiro de suporte se você precisar de ajuda, para que ele possa ajudar rapidamente. Faça isso facilmente **tirando uma captura de tela** ou clicando no **ícone Erro de cópia**, localizado à direita da caixa de texto **Erro de cópia**.

<a id="notification-details-explained" class="xliff"></a>

## Detalhes da notificação explicados

Os dados abaixo explicam mais sobre o significado de cada item de notificação, e fornece exemplos de cada um deles.

<a id="essential-notification-items" class="xliff"></a>

### Itens essenciais de notificação

-   **Título** – O título descritivo da notificação

  * Exemplo – **Configurações do proxy do aplicativo**

-   **Descrição** – A descrição do que ocorreu como resultado da operação

    -   Exemplo – **A url interna inserida já está sendo usada por outro aplicativo**

-   **Id de notificação** – A id exclusiva da notificação

    -   Exemplo – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **Id de Solicitação do Cliente** – A id de solicitação específica feita pelo seu navegador

    -   Exemplo – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **UTC do Carimbo de Hora** – O carimbo de hora durante o qual a notificação ocorreu, em UTC

    -   Exemplo – **2017-03-23T19:50:43.7583681Z**

-   **Id de Transação Interna** – A ID interna que podemos usar para procurar o erro em nossos sistemas

    -   Exemplo – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** – O usuário que realizou a operação

    -   Exemplo – **tperkins@f128.info**

-   **Id do Locatário** – A ID exclusiva do locatário do qual o usuário que realizou a operação era membro

    -   Exemplo – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Id do objeto de usuário** – A ID exclusiva do usuário que realizou a operação

    -   Exemplo – **17f84be4-51f8-483a-b533-383791227a99**

<a id="detailed-notification-items" class="xliff"></a>

### Itens detalhados da notificação

-   **Nome de Exibição** – **(pode estar vazio)** um nome de exibição mais detalhado do erro

    -   Exemplo – **Configurações do proxy do aplicativo**

-   **Status** – O status específico da notificação

    -   Exemplo – **Falha**

-   **Id do Objeto** – **(pode estar vazia)** A identificação do objeto no qual a operação foi executada

    -   Exemplo – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Detalhes** – A descrição detalhada do que ocorreu como resultado da operação

    -   Exemplo – **Url interna 'http://bing.com/' é inválida, já que está sendo usada**

-   **Erro de cópia** – Clique no ícone **cópia** à direita da caixa de texto **Erro de cópia** para copiar todos os detalhes de notificação para compartilhar com um engenheiro de suporte ou de grupo de produtos

    -   Exemplo ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

<a id="next-steps" class="xliff"></a>

## Próximas etapas
[Gerenciamento de aplicativos com o Active Directory do Azure](active-directory-enable-sso-scenario.md)

