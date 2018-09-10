---
title: Gerenciar os programas e os controles para análises de acesso do Azure AD | Microsoft Docs
description: É possível criar outros programas para cada iniciativa de governança, gerenciamento de riscos e conformidade da sua organização para coletar e organizar as revisões de acesso do Azure Active Directory como controles.
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
ms.component: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: a1a08ac29605e66dc14ac5e32d21e00dad8cb655
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622279"
---
# <a name="manage-programs-and-their-controls"></a>Gerenciar programas e seus controles 

O Azure Active Directory (Azure AD) inclui as revisões de acesso de membros do grupo e acesso ao aplicativo. Esses exemplos de controles garantem supervisão para quem tem acesso a aplicativos e associações do grupo da sua organização. As organizações podem usar esses controles para lidar eficientemente com os requisitos de governança, gerenciamento de riscos e conformidade.

## <a name="create-and-manage-programs-and-their-controls"></a>Criar e gerenciar programas e seus controles
É possível simplificar a maneira de rastrear e coletar revisões de acesso para finalidades diferentes, organizando-as em programas. Cada análise de acesso pode ser vinculada a um programa. Em seguida, ao preparar relatórios para um auditor, você poderá concentrar-se nas revisões de acesso no escopo de uma iniciativa específica.  Programas e resultados de revisão de acesso são visíveis para usuários na função Administrador Global, Administrador de Conta de Usuário, Administrador de Segurança ou Leitor de Segurança.

Para ver uma lista de programas, vá para a [página revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) e selecione **Programas**.

O **Programa Padrão** sempre está presente. Se você estiver em uma função de administrador global ou um usuário de função de administrador de conta, será possível criar outros programas. Por exemplo, é possível optar por ter um programa para cada iniciativa de conformidade ou meta de negócios.

Se você não precisar mais de um programa e se ele não tiver nenhum controle vinculado a ele, você poderá excluí-lo.

## <a name="next-steps"></a>Próximas etapas

- [Como criar uma revisão de acesso para os membros de um grupo ou para o acesso a um aplicativo](active-directory-azure-ad-controls-create-access-review.md)
- [Recuperar os resultados de uma revisão de acesso](active-directory-azure-ad-controls-retrieve-access-review.md)
