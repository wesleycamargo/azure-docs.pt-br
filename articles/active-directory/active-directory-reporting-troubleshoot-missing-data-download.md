---
title: 'Solução de problemas: dados ausentes no logs de atividades do Azure Active Directory baixados | Microsoft Docs'
description: Fornece uma resolução para dados ausentes nos logs de atividade do Azure Active Directory baixados.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d0638404ec6f5b6d13aa207ef54913c1bd3ecc1a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231871"
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Não consigo localizar nenhum dado nos logs de atividades do Azure Active Directory que eu baixei


## <a name="symptoms"></a>Sintomas

Baixei os logs de atividade (auditoria ou entradas) e não vejo todos os registros para o momento que escolhi. Por quê? 

 ![Relatórios](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Causa

Ao baixar logs de atividades no portal do Azure, limitamos a escala a 5000 registros, classificados primeiro pelos mais recentes. 

## <a name="resolution"></a>Resolução

Você pode aproveitar as [APIs de relatórios do Azure AD](active-directory-reporting-api-getting-started.md) para buscar até um milhões de registros em qualquer momento determinado. Nossa abordagem recomendada é executar um script de uma forma agendada que chame as APIs de geração de relatórios para buscar registros de maneira incremental durante um período de tempo (por exemplo, diariamente ou semanalmente).

## <a name="next-steps"></a>Próximas etapas
Veja as [Perguntas frequentes sobre os relatórios do Azure Active Directory](active-directory-reporting-faq.md).

