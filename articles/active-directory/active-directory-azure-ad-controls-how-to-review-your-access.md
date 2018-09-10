---
title: Examinar seu acesso | Microsoft Docs
description: Examinar seu acesso com o Azure Active Directory
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 07/16/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: f85f83ff103818b826ca2c61c41fbbef67e98c14
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622221"
---
# <a name="review-your-access"></a>Examinar seu acesso

O Azure Active Directory (Azure AD) simplifica como as empresas gerenciam o acesso a aplicativos e membros de grupos no Azure AD e outros Serviços Online da Microsoft, com um recurso chamado revisões de acesso. Talvez você tenha recebido um email da Microsoft solicitando que você examine o acesso para membros de um grupo ou usuários com acesso a um aplicativo. 

## <a name="open-an-access-review"></a>Abrir uma análise de acesso

Para ver as revisões de acesso pendentes, clique no link de acesso à revisão no email. A partir de agosto de 2018, as notificações por email para funções do Azure AD tem terão design atualizado. A seguir, é mostrado um exemplo de email enviado quando um usuário é convidado para ser um revisor.

![Email de acesso à revisão](./media/active-directory-azure-ad-controls-how-to-review-your-access/new-ar-email.png)

Se você não tiver o email, será possível localizar as revisões de acesso fazendo o seguinte:

1. Entre no [Painel de acesso do Azure AD](https://myapps.microsoft.com).

2. Selecione o símbolo de usuário no canto superior direito da página, que exibe seu nome e a organização padrão. Se houver mais de uma organização listada, selecione a organização que solicitou uma análise de acesso.

3. Se houver um bloco rotulado **Revisões de acesso** do lado direito da página, selecione-o. Se o bloco não estiver visível, não haverá revisões de acesso para executar nessa organização e nenhuma ação será necessária no momento.

## <a name="fill-out-an-access-review"></a>Preencher uma análise de acesso

Ao selecionar uma análise de acesso na lista, você poderá ver seu acesso. Selecione a linha e escolha se deseja aprovar ou negar a necessidade de acesso contínuo.

O revisor pode exigir que você forneça uma justificativa de aprovação de acesso contínuo.

## <a name="next-steps"></a>Próximas etapas

O acesso negado não é removido imediatamente. Caso queira alterar a resposta e aprovar, redefina a resposta e selecione uma nova resposta. Você pode fazer essa etapa até que a análise de acesso seja concluída.






