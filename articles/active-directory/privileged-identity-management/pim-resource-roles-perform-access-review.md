---
title: Realizar uma revisão de acesso das minhas funções de recurso do Azure no PIM | Microsoft Docs
description: Saiba como executar uma revisão de acesso de suas funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 3b9c998351432a591ece0a61d60256aea89a6f8f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180884"
---
# <a name="perform-an-access-review-of-my-azure-resource-roles-in-pim"></a>Realizar uma revisão de acesso das minhas funções de recurso do Azure no PIM
O Privileged Identity Management (PIM) para recursos do Azure simplifica o modo como as empresas gerenciam o acesso privilegiado aos recursos no Azure. 

Se você for atribuído a uma função administrativa, o administrador de função com privilégios de sua organização poderá solicitar que você confirme regularmente que ainda precisa da função para seu trabalho. Você pode receber um email que inclui um link ou pode acessar diretamente o [portal do Azure](https://portal.azure.com). Siga as etapas neste artigo para executar a autorrevisão das suas funções atribuídas.

Se você for um administrador com privilégios de função interessado em revisões de acesso, obtenha mais detalhes em [Como iniciar uma revisão de acesso](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Adicionar o aplicativo Privileged Identity Management
Você pode usar o aplicativo PIM do Azure Active Directory (Azure AD) no [portal do Azure](https://portal.azure.com/) para executar a revisão. Se você não tiver o aplicativo em seu portal, siga estas etapas para começar.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Selecione seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório em que você vai operar.
3. Selecione **Todos os serviços** e use a caixa **Filtrar** para pesquisar o *Azure AD Privileged Identity Management*.
4. Verifique **Fixar no painel** e, em seguida, selecione **Criar**. O aplicativo do PIM é aberto.

## <a name="approve-or-deny-access"></a>Aprovar ou negar acesso
Ao aprovar ou negar o acesso, você está apenas dizendo ao revisor se ainda usa essa função ou não. Escolha **Aprovar** se você quiser manter a função ou **Negar** se não precisar mais do acesso. Seu status será alterado somente quando o revisor aplicar os resultados.

Siga estas etapas para localizar e concluir a análise de acesso:
1. Navegue até o aplicativo do Azure AD PIM.
2. Selecione a folha **Revisar acesso**.

   ![Captura de tela do aplicativo PIM, com folha Revisar acesso selecionada](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Selecione a análise que deseja concluir. 
4. Escolha **Aprovar** ou **Negar**. Na caixa de texto **Fornecer um motivo**, talvez seja necessário incluir um motivo para a sua decisão.

   ![Captura de tela Revisar detalhes](media/azure-pim-resource-rbac/rbac-access-review-choice.png)

## <a name="next-steps"></a>Próximas etapas

- [Executar uma revisão de acesso de minhas funções de diretório do Azure AD no PIM](pim-how-to-perform-security-review.md)
