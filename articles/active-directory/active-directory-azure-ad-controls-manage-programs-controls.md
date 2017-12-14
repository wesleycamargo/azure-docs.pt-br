---
title: "Gerenciar os programas e os controles para análises de acesso do Azure AD | Microsoft Docs"
description: "É possível criar outros programas para cada iniciativa de governança, gerenciamento de riscos e conformidade da sua organização para coletar e organizar as revisões de acesso do Azure Active Directory como controles."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: f322bff427384e6432fcc0c288704f6bb605c900
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="manage-programs-and-their-controls"></a>Gerenciar programas e seus controles 

O Azure Active Directory (Azure AD) inclui as revisões de acesso de membros do grupo e acesso ao aplicativo. Esses exemplos de controles garantem supervisão para quem tem acesso a aplicativos e associações do grupo da sua organização. As organizações podem usar esses controles para lidar eficientemente com os requisitos de governança, gerenciamento de riscos e conformidade.

## <a name="create-and-manage-programs-and-their-controls"></a>Criar e gerenciar programas e seus controles
É possível simplificar a maneira de rastrear e coletar revisões de acesso para finalidades diferentes, organizando-as em programas. Cada análise de acesso pode ser vinculada a um programa. Então, quando você preparar relatórios para um auditor, somente as revisões de acesso no escopo para uma determinada iniciativa estarão visíveis.

Para ver uma lista de programas, vá para a [página revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) e selecione **Programas**.

O **Programa Padrão** sempre está presente. Se você estiver em uma função de administrador global, será possível criar outros programas. Por exemplo, é possível optar por ter um programa para cada iniciativa de conformidade ou meta de negócios.

Se você não precisar mais de um programa e se ele não tiver nenhum controle vinculado a ele, você poderá excluí-lo.

## <a name="next-steps"></a>Próximas etapas

[Como criar uma revisão de acesso para os membros de um grupo ou para o acesso a um aplicativo](active-directory-azure-ad-controls-create-access-review.md)

