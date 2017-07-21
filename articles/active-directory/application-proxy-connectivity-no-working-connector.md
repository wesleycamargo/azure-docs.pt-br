---
title: Nenhum grupo do conector de trabalho encontrado para um aplicativo de Application Proxy | Microsoft Docs
description: "Solucionar problemas que podem ocorrer quando não há nenhum trabalho conector em um grupo de conector para seu aplicativo com o Application Proxy do Azure AD"
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
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 5b133afd82afecbfab250c2760404a4173086fa5
ms.contentlocale: pt-br
ms.lasthandoff: 04/17/2017

---

<a id="no-working-connector-group-found-for-an-application-proxy-application" class="xliff"></a>

# Nenhum grupo do conector de trabalho encontrado para um aplicativo de Application Proxy

Ajuda neste artigo você resolver os problemas mais comuns enfrentados quando não há um conector detectado para um aplicativo de Application Proxy integrado ao Azure Active Directory.

<a id="overview-of-steps" class="xliff"></a>

## Visão geral das etapas
Se não houver nenhum conector funcionando em um grupo de conector para o seu aplicativo, haverá algumas maneiras de resolver o problema:

-   Se você tiver não conectores no grupo, você poderá:

    -   Baixar um novo conector no servidor local certo e atribuí-lo a esse grupo

    -   Mover um conector ativo para o grupo

-   Se você não tiver conectores ativos no grupo, você poderá:

    -   Identificar o motivo pelo qual que o conector está inativo e resolver

    -   Mover um conector ativo para o grupo

Para saber qual é o problema, abra o menu "Application Proxy" no seu aplicativo e examine a mensagem de aviso do grupo de conector. Ela especifica que o grupo precisa de, pelo menos, um conector (você não tem nenhum no grupo) ou que não tem nenhum conector ativo (embora provavelmente tenha conectores inativos).

   ![Seleção de grupo do conector no Portal do Azure](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Para obter detalhes sobre cada uma dessas opções, consulte a seção correspondente abaixo. Cada um desses pressupõe que você está começando na página de gerenciamento de conector. Se você estiver procurando a mensagem de erro acima, você poderá ir para essa página clicando na mensagem de aviso. Caso contrário, isso pode ser encontrado visitando **Azure Active Directory**, clicando em **aplicativos empresariais**, em seguida, **Application Proxy.**

   ![Gerenciamento de grupo do conector no Portal do Azure](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

<a id="download-a-new-connector" class="xliff"></a>

## Baixar um novo conector

Para baixar um novo conector, use o botão "Baixar conector" na parte superior da página.

observe que o conector deve ser instalado em um computador com a linha direta de visão para o aplicativo de back-end e normalmente é colocado no mesmo servidor que o aplicativo. Após o download, o conector deve aparecer no menu. Clique no conector e use a lista suspensa "Grupo do Conector" para certificar-se de que ele pertence ao grupo direito. Salve a alteração.

   ![Baixe o conector do Portal do Azure](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
<a id="move-an-active-connector" class="xliff"></a>

## Mova um conector ativo

Se você tiver um conector ativo que deve pertencer ao grupo e tem metas claras para o aplicativo de back-end de destino, você poderá mover o conector para o grupo atribuído. Para fazer isso, clique no conector. No campo "Conector de grupo", use a lista suspensa para selecionar o grupo correto e clique em Salvar.

<a id="resolve-an-inactive-connector" class="xliff"></a>

## Resolver um conector inativo

Se os conectores apenas no grupo estão inativos, eles estão provavelmente em um computador que não tem todas as portas desbloqueadas.

consulte o documento de resolução de problemas das portas para obter detalhes sobre como investigar esse problema.

<a id="next-steps" class="xliff"></a>

## Próximas etapas
[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md)



