---
title: Descobrir os recursos do Azure para gerenciar no PIM | Microsoft Docs
description: Saiba como descobrir recursos do Azure para gerenciar no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/30/2018
ms.author: rolyon
ms.openlocfilehash: d9a6ab49d619e487eee6fb13abe128cfc167b560
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306682"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Descobrir os recursos do Azure para gerenciar no PIM

Usando o Gerenciamento de Identidade Privilegiada (PIM) do Azure AD, você pode melhorar a proteção de seus recursos do Azure. Isso é útil para organizações que já usam o PIM para proteger as funções de diretório do Azure AD e para os proprietários de grupos e assinaturas de gerenciamento que desejam proteger os recursos de produção.

Quando você configura pela primeira vez os recursos do PIM para o Azure, é necessário descobrir e selecionar os recursos a serem protegidos com o PIM. Não há nenhum limite para o número de recursos que você pode gerenciar com o PIM. No entanto, é recomendável começar com seus recursos  mais importantes (produção).

## <a name="discover-resources"></a>Descobrir recursos

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **recursos do Azure**.

    Se esta for a primeira vez que você usa recursos do PIM para o Azure, você verá um painel Recursos do Discover.

    ![Descubra recursos - primeira vez](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Se outro administrador de recurso ou diretório em sua organização já estiver gerenciando recursos do Azure no PIM, você verá uma lista dos recursos que estão sendo gerenciados no momento.

    ![Descubra recursos de painel](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Clique em **Descobrir recursos** para iniciar a experiência de descoberta.

    ![Painel de descoberta](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. No painel Descoberta, use **Filtro de estado de recurso** e **Selecione o tipo de recurso** para filtrar os grupos de gerenciamento ou assinaturas para os quais você tem permissão de gravação. Ele provavelmente é mais fácil para começar **todos** os inicialmente.

    Você pode pesquisar por recursos de assinatura ou de grupo de gerenciamento e selecioná-los somente para gerenciá-los usando o PIM. Quando você gerencia um grupo de gerenciamento ou uma assinatura no PIM, você também pode gerenciar seus recursos filhos.

1. Adicione uma marca de seleção ao lado de todos os recursos não gerenciados que você deseja gerenciar.

    > [!NOTE]
    > Depois que um grupo de gerenciamento ou assinatura for definido como gerenciado, ele não poderá ser gerenciado. Isso impede que outro administrador de recursos remova as configurações do PIM.

    ![Descoberta - gerenciar recursos](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Clique em **Gerenciar recurso** para começar a gerenciar os recursos selecionados.

## <a name="next-steps"></a>Próximas etapas

- [Definir configurações de função de recurso do Azure no PIM](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções de recurso do Azure no PIM](pim-resource-roles-assign-roles.md)
