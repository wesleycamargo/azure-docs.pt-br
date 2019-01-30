---
title: 'Solucionar problemas: Dados ausentes nos logs de atividade do Azure Active Directory baixados | Microsoft Docs'
description: Fornece uma resolução para dados ausentes nos logs de atividade do Azure Active Directory baixados.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ea7ef4eb75cc7ffdf951e8d8799e898bfc31948b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818193"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Não consigo localizar todos os dados nos logs de atividade do Azure Active Directory que eu baixei

## <a name="symptoms"></a>Sintomas

Baixei os logs de atividade (auditoria ou entradas) e não vejo todos os registros para o momento que escolhi. Por quê? 

 ![Relatórios](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Causa

Ao baixar logs de atividades no portal do Azure, limitamos a escala a 5000 registros, classificados primeiro pelos mais recentes. 

## <a name="resolution"></a>Resolução

Você pode aproveitar as [APIs de relatórios do Azure AD](concept-reporting-api.md) para buscar até um milhões de registros em qualquer momento determinado. Nossa abordagem recomendada é [executar um script com base em agendamento](tutorial-signin-logs-download-script.md) que chame as APIs de relatórios para buscar registros de maneira incremental durante um período de tempo (por exemplo, diariamente ou semanalmente). 

## <a name="next-steps"></a>Próximas etapas

* [Perguntas frequentes sobre relatórios do Azure Active Directory](reports-faq.md)

