---
title: Gerenciar programas e controles para análises de acesso do AD do Azure | Microsoft Docs
description: Aprenda a criar programas adicionais para cada controle, gerenciamento de riscos e iniciativa de conformidade em sua organização para coletar e organizar o Azure Active Directory as revisões de acesso como controles.
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
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3056976ada6c0a0287eec608dbad230e8dbb2625
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895161"
---
# <a name="manage-programs-and-controls-for-azure-ad-access-reviews"></a>Gerenciar programas e controles para análises de acesso do AD do Azure

O Azure Active Directory (Azure AD) inclui as revisões de acesso de membros do grupo e acesso ao aplicativo. Esses exemplos de controles garantem supervisão para quem tem acesso a aplicativos e associações do grupo da sua organização. As organizações podem usar esses controles para lidar eficientemente com os requisitos de governança, gerenciamento de riscos e conformidade.

## <a name="create-and-manage-programs-and-their-controls"></a>Criar e gerenciar programas e seus controles
É possível simplificar a maneira de rastrear e coletar revisões de acesso para finalidades diferentes, organizando-as em programas. Cada análise de acesso pode ser vinculada a um programa. Em seguida, ao preparar relatórios para um auditor, você poderá concentrar-se nas revisões de acesso no escopo de uma iniciativa específica.  Programas e os resultados da revisão de acesso são visíveis aos usuários o Administrador Global, administrador de usuário, administrador de segurança ou função de leitor de segurança.

Para ver uma lista de programas, vá para a [página revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) e selecione **Programas**.

O **Programa Padrão** sempre está presente. Se você estiver usando um administrador global ou a função de administrador do usuário, você pode criar programas adicionais. Por exemplo, é possível optar por ter um programa para cada iniciativa de conformidade ou meta de negócios.

Se você não precisar mais de um programa e se ele não tiver nenhum controle vinculado a ele, você poderá excluí-lo.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)
- [Recuperar resultados da revisão de acesso para grupos ou aplicativos](retrieve-access-review.md)
