---
title: Perform an access review in Privileged Identity Management for Azure resources | Microsoft Docs
description: Este documento descreve como realizar uma análise de acesso no PIM para recursos do Azure, de acordo com a função de recurso.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 47d981ce8034d725fe2b119392334a156852698d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33201582"
---
# <a name="perform-an-access-review-in-pim-according-to-resource-role"></a>Perform an access review in PIM, according to resource role
O Privileged Identity Management (PIM) para recursos do Azure simplifica o modo como as empresas gerenciam o acesso privilegiado aos recursos no Azure. 

If you are assigned to an administrative role, your organization's privileged role administrator might ask you to regularly confirm that you still need that role for your job. Você pode receber um email que inclui um link ou pode acessar diretamente o [portal do Azure](https://portal.azure.com). Siga as etapas neste artigo para executar a autorrevisão das suas funções atribuídas.

Se você for um administrador com privilégios de função interessado em revisões de acesso, obtenha mais detalhes em [Como iniciar uma revisão de acesso](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Adicionar o aplicativo Privileged Identity Management
Você pode usar o aplicativo PIM do Azure Active Directory (Azure AD) no [portal do Azure](https://portal.azure.com/) para executar a revisão. If you don't have the application in your portal, follow these steps to get started.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório em que você vai operar.
3. Select <bpt id="p1">**</bpt>All services<ept id="p1">**</ept>, and use the <bpt id="p2">**</bpt>Filter<ept id="p2">**</ept> box to search for <bpt id="p3">*</bpt>Azure AD Privileged Identity Management<ept id="p3">*</ept>.
4. Verifique **Fixar no painel** e, em seguida, selecione **Criar**. The PIM application opens.

## <a name="approve-or-deny-access"></a>Aprovar ou negar acesso
Ao aprovar ou negar o acesso, você está apenas dizendo ao revisor se ainda usa essa função ou não. Escolha **Aprovar** se você quiser manter a função ou **Negar** se não precisar mais do acesso. Your status changes only when the reviewer applies the results.

Siga estas etapas para localizar e concluir a análise de acesso:
1. Navegue até o aplicativo do Azure AD PIM.
2. Selecione a folha **Revisar acesso**.

   ![Captura de tela do aplicativo PIM, com folha Revisar acesso selecionada](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Selecione a análise que deseja concluir. 
4. Escolha **Aprovar** ou **Negar**. Na caixa de texto **Fornecer um motivo**, talvez seja necessário incluir um motivo para a sua decisão.

   ![Captura de tela Revisar detalhes](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
