---
title: Como executar uma análise de acesso no Privileged Identity Management para Recursos do Azure | Microsoft Docs
description: Este documento descreve como executar e revisar acesso no PIM dos Recursos do Azure.
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
ms.openlocfilehash: f1bcf114b997c3056016b84cafc28253ea1af28e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="resource-dashboards"></a>Painéis de controle de recursos

O painel de Modo de exibição do administrador possui quatro componentes principais. Uma representação gráfica das ativações de função de recurso dos últimos sete dias. Esses dados estão no escopo das ativações de telas e recursos selecionados para as funções mais comuns (Proprietário, Colaborador, Administrador de Acesso do Usuário) e todas as funções combinadas.

No lado direito do grafo de ativações, há dois gráficos que exibem a distribuição de atribuições de função por tipo de atribuição, para os usuários e para os grupos. Selecionar uma fatia do gráfico altera o valor para uma porcentagem (ou vice-versa).

![](media/azure-pim-resource-rbac/rbac-overview-top.png)

Abaixo dos gráficos, você pode ver o número de usuários e grupos com novas atribuições de função dos últimos 30 dias (lado esquerdo) e uma lista de funções, classificadas por atribuições totais (decrescente).

![](media/azure-pim-resource-rbac/role-settings.png)
