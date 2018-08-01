---
title: Examinar o acesso usando as revisões de acesso do Azure AD | Microsoft Docs
description: Saiba como revisar o acesso usando análises de acesso do Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 07/16/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 2e0a522f6125a9e34a37c50f547e725072ec65b4
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39204996"
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Examinar o acesso com as revisões de acesso do Azure AD

O Azure Active Directory (Azure AD) simplifica como as empresas gerenciam o acesso a aplicativos e membros de grupos no Azure AD e outros Serviços Online da Microsoft, com um recurso chamado revisões de acesso. Talvez você tenha recebido um email da Microsoft solicitando que você examine o acesso para membros de um grupo ou usuários com acesso a um aplicativo. 

## <a name="open-an-access-review"></a>Abrir uma análise de acesso

Para ver as revisões de acesso pendentes, clique no link de acesso à revisão no email. A partir de agosto de 2018, as notificações por email para funções do Azure AD tem terão design atualizado. A seguir, é mostrado um exemplo de email enviado quando um usuário é convidado para ser um revisor. 

![Email de acesso à revisão](./media/active-directory-azure-ad-controls-perform-access-review/new-ar-email.png)

Se você não tiver o email, será possível localizar as revisões de acesso fazendo o seguinte:

1. Entre no [Painel de acesso do Azure AD](https://myapps.microsoft.com).

2. Selecione o símbolo de usuário no canto superior direito da página, que exibe seu nome e a organização padrão. Se houver mais de uma organização listada, selecione a organização que solicitou uma análise de acesso.

3. Se houver um bloco rotulado **Revisões de acesso** do lado direito da página, selecione-o. Se o bloco não estiver visível, não haverá revisões de acesso para executar nessa organização e nenhuma ação será necessária no momento.

## <a name="fill-out-an-access-review"></a>Preencher uma análise de acesso

Ao selecionar uma análise de acesso na lista, você verá os nomes de usuários que precisam ser revisados. Você poderá ver apenas um nome – o seu – se a solicitação era examinar seu próprio acesso.

Para cada linha na lista, você pode decidir se deseja aprovar ou negar o acesso do usuário. Selecione a linha e escolha se deseja aprovar ou negar. (Se você não conhecer o usuário, poderá indicar isso também.)

O revisor pode exigir que você forneça uma justificativa de aprovação de acesso contínuo ou de associação a um grupo.

## <a name="next-steps"></a>Próximas etapas

O acesso negado de um usuário não é removido imediatamente. Ele pode ser removido quando a análise for concluída ou quando um administrador para a análise. Se desejar alterar a resposta e aprovar um usuário negado anteriormente ou negar um usuário aprovado anteriormente, selecione a linha, redefina a resposta e selecione uma nova resposta. Você pode fazer essa etapa até que a análise de acesso seja concluída.



