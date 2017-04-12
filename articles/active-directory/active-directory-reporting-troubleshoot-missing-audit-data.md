---
title: "Solução de problemas: Dados ausentes no log de atividades do Azure Active Directory - visualização | Microsoft Docs"
description: "Lista os diversos relatórios disponíveis na visualização do Azure Active Directory"
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
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: c372fe5f3a419a6a27ef00d755d5d46325b956c6
ms.lasthandoff: 04/06/2017


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


