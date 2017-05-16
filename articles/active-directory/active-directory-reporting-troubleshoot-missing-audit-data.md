---

title: "Solução de problemas: dados ausentes no log de atividades do Azure Active Directory | Microsoft Docs"
description: "Lista os diversos relatórios disponíveis no Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 18af51e95a283a5cd33688484a0d7477eb4b957d
ms.contentlocale: pt-br
ms.lasthandoff: 05/08/2017


---

# <a name="i-cant-find-some-actions-that-i-performed-in-the-azure-active-directory-activity-log"></a>Não consigo localizar algumas ações que executei no log de atividades do Azure Active Directory


## <a name="symptoms"></a>Sintomas

Eu executei algumas ações no portal do Azure e esperava ver os logs de auditoria para essas ações na folha `Activity logs > Audit Logs`, mas não é possível encontrá-los.

 ![Relatórios](./media/active-directory-reporting-troubleshoot-missing-audit-data/01.png)
 

## <a name="cause"></a>Causa

As ações não são exibidas imediatamente no log de Auditoria da Atividade. Ele pode levar de 15 minutos a uma hora para ver os logs de auditoria no portal desde o momento em que a operação é executada.

## <a name="resolution"></a>Resolução

Aguarde de 15 minutos a uma hora e verifique se as ações aparecem no log. Se você ainda não os vê, gere um tíquete de suporte conosco e verificaremos o problema.


## <a name="next-steps"></a>Próximas etapas
Veja as [Perguntas frequentes sobre os relatórios do Azure Active Directory](active-directory-reporting-faq.md).


