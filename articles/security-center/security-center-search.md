---
title: Pesquisa da Central de Segurança do Azure | Microsoft Docs
description: Saiba como a Central de segurança do Azure usa a pesquisa de logs do Azure Monitor para recuperar e analisar seus dados de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: 6cbf3d70bd835ce1b838b19c93507f7d9487a418
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332549"
---
# <a name="azure-security-center-search"></a>Pesquisa da Central de Segurança do Azure
Central de segurança do Azure usa [pesquisa de logs do Azure Monitor](../log-analytics/log-analytics-log-searches.md) para recuperar e analisar seus dados de segurança. Os logs do Azure Monitor inclui uma linguagem de consulta para recuperar e consolidar dados rapidamente. Central de segurança, você pode aproveitar a pesquisa de logs do Azure Monitor para construir consultas e analisar os dados coletados.

A pesquisa está disponível nas camadas Gratuita e Standard da Central de Segurança.  Os dados disponíveis em suas pesquisas de log dependem do nível de camada aplicado ao seu workspace.  Para saber mais, confira a [página de preços](../security-center/security-center-pricing.md) da Central de Segurança.


> [!NOTE]
> A Central de Segurança não salva dados de segurança de um workspace na Camada gratuita. Você pode enviar vários tipos de logs para um workspace na Camada gratuita e pesquisar nesses dados, mas os resultados da pesquisa não incluirão os dados da Central de Segurança. A Central de Segurança só salva dados em um workspace na camada Standard.
>
>

## <a name="access-search"></a>Pesquisa de acesso
1. No menu principal da Central de Segurança, selecione **Pesquisar**.

   ![Selecionar Pesquisa de log][1]

2. A Central de Segurança lista todos os workspaces em suas assinaturas do Azure. Selecione um workspace. (Se você tiver apenas um workspace, o seletor desse workspace não aparecerá).

   ![Selecionar um workspace][2]

3. A **Pesquisa de Logs** é aberta. Para consultar mais dados no workspace selecionado, insira este exemplo de consulta:

   SecurityEvent | where EventID == 4625 | summarize count() by TargetAccount

   O resultado mostra todas as contas com falha de logon (evento 4625).

   ![Resultados da Pesquisa][3]

Ver [linguagem de consulta Kusto](../log-analytics/log-analytics-search-reference.md) para obter mais informações sobre como consultar dados no espaço de trabalho selecionado.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a acessar a pesquisa na Central de Segurança. Central de segurança usa a pesquisa de logs do Azure Monitor. Para saber mais sobre a pesquisa de logs do Azure Monitor, consulte:

- [O que é o Azure Monitor logs?](../log-analytics/log-analytics-overview.md) – Visão geral sobre os logs do Azure Monitor
- [Entender pesquisas de log nos logs do Azure Monitor](../log-analytics/log-analytics-log-search-new.md) – descreve como as pesquisas de log são usadas nos logs do Azure Monitor e fornece conceitos que devem ser compreendidos antes de criar uma pesquisa de logs
- [Localizar dados usando pesquisas de log nos logs do Azure Monitor](../log-analytics/log-analytics-log-searches.md) – Tutorial sobre como usar a pesquisa de logs
- [Referência de pesquisa do Kusto](../log-analytics/log-analytics-search-reference.md) – descreve a linguagem de consulta nos logs do Azure Monitor

Para saber mais sobre a Central de Segurança, confira:

- [Visão geral da Central de Segurança](security-center-intro.md) – Descreve as principais funcionalidades da Central de Segurança

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
