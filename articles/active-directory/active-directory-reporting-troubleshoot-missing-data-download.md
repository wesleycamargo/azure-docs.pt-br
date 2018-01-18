---
title: "Solução de problemas: dados ausentes no logs de atividades do Azure Active Directory baixados | Microsoft Docs"
description: "Fornece uma resolução para dados ausentes nos logs de atividade do Azure Active Directory baixados."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: d976e2476001ad4d23e55913681500f14e285014
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2018
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>Não consigo encontrar dados nos logs de atividade do Azure Active Directory que baixei


## <a name="symptoms"></a>Sintomas

Baixei os logs de atividade (auditoria ou entradas) e não vejo todos os registros para o momento que escolhi. Por quê? 

 ![Relatórios](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Causa

Quando você baixa os logs de atividade no portal do Azure, limitamos a escala para 120 mil registros, classificados pelos mais recentes. 

## <a name="resolution"></a>Resolução

Você pode aproveitar as [APIs de relatórios do Azure AD](active-directory-reporting-api-getting-started.md) para buscar até um milhões de registros em qualquer momento determinado. Nossa abordagem recomendada é executar um script de uma forma agendada que chame as APIs de geração de relatórios para buscar registros de maneira incremental durante um período de tempo (por exemplo, por dia ou por semana).

## <a name="next-steps"></a>Próximas etapas
Veja as [Perguntas frequentes sobre os relatórios do Azure Active Directory](active-directory-reporting-faq.md).

