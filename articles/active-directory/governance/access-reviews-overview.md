---
title: O que são as Revisões de Acesso do Microsoft Azure Active Directory? | Microsoft Docs
description: Usando as Revisões de Acesso do Azure Active Directory, você pode controlar a associação de grupos e o acesso a aplicativos para atender às iniciativas de governança, gerenciamento de riscos e conformidade da sua organização.
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
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ab3570cb7e3e8b09425bf4c05f7f5725fd21d4f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845147"
---
# <a name="what-are-azure-ad-access-reviews"></a>O que são as Revisões de Acesso do Microsoft Azure Active Directory?

As Revisões de Acesso do Microsoft Azure Active Directory (Azure AD) permitem que as organizações gerenciem com eficiência as associações de grupo e o acesso a aplicativos empresariais e atribuições de função. O acesso do usuário pode ser examinado regularmente para garantir que somente as pessoas corretas tenham acesso contínuo.

Aqui está um vídeo que fornece uma visão geral das revisões de acesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Por que as revisão de acesso são importantes?

O Microsoft Azure Active Directory permite que você colabore internamente em sua organização e com os usuários de organizações externas, como parceiros. Os usuários podem ingressar em grupos, convidar pessoas, conectar-se aos aplicativos de nuvem e trabalhar remotamente com seus dispositivos pessoais ou de trabalho. A conveniência de aproveitar o poder do autoatendimento levou a uma necessidade de melhores recursos de gerenciamento de acesso.

- Conforme novos funcionários ingressam, como garantir que eles tenham o acesso correto para serem produtivos?
- Quando as pessoas mudam de equipes ou saem da empresa, como garantir que seu antigo acesso seja removido, especialmente quando envolve convidados?
- Direitos de acesso excessivos podem levar a conclusões e comprometimentos de auditoria pois indicam uma falta de controle de acesso.
- Você tem que se envolver proativamente com os proprietários do recurso para garantir que eles revisam regularmente quem tem acesso a seus recursos.

## <a name="when-to-use-access-reviews"></a>Quando usar as revisões de acesso?

- **Muitos usuários em funções com privilégios:** Ele é uma boa ideia para verificar quantos usuários têm acesso administrativo, quantos deles são administradores globais, e se há alguma convidou convidados ou parceiros não tiverem sido removidos após a que está sendo atribuído para fazer uma tarefa administrativa. Você pode renovar os usuários de atribuição de função nas [funções de diretório do Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), como os administradores globais, ou [funções de recursos do Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) como Administrador de acesso do usuário na experiência do [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md).
- **Quando a automação é inviável:** Você pode criar regras para associação dinâmica em grupos de segurança ou grupos do Office 365, mas e se os dados de RH não estiverem no Azure AD, ou se os usuários ainda precisarem de acesso após deixar o grupo para treinar sua substituição? Em seguida, você pode criar uma revisão nesse grupo para garantir que aqueles que ainda precisam de acesso devem ter acesso contínuo.
- **Quando um grupo é usado para uma nova finalidade:** Se você tiver um grupo que será sincronizado com o Azure AD, ou se planeja habilitar o aplicativo Salesforce para todos no grupo de equipe de Vendas, será útil solicitar ao proprietário do grupo para revisar a associação ao grupo antes que o grupo seja utilizado em um conteúdo de risco diferente.
- **Acesso de dados críticos de negócios:** para certos recursos, talvez seja necessário pedir que as pessoas fora do departamento de TI regularmente, saiam e forneçam uma justificativa sobre por que eles precisam de acesso para fins de auditoria.
- **Para manter a lista de exceções de uma política:** Em um mundo ideal, todos os usuários seguiriam as políticas de acesso para proteger o acesso aos recursos de sua organização. No entanto, às vezes, há casos comerciais em que é necessário fazer exceções. Como o administrador de TI, você pode gerenciar essa tarefa, evitar supervisão de exceções à política e fornecer os auditores a comprovação de que essas exceções são revisadas regularmente.
- **Solicite aos proprietários do grupo para confirmar se ainda precisam de convidados em seus grupos:** Acesso de funcionários pode ser automatizado com alguns locais IAM, mas não os convidados. Se um grupo oferece acesso de convidados para conteúdo confidencial da empresa, em seguida, é responsabilidade do proprietário do grupo se confirmar os convidados ainda terão uma necessidade comercial legítima de acesso.
- **Realizar revisões periodicamente:** É possível configurar revisões de acesso recorrentes de usuários em frequências definidas como semanal, mensal, trimestral ou anual e os revisores serão notificados no início de cada revisão. Os revisores podem aprovar ou negar acesso com uma interface amigável e com a ajuda de recomendações inteligentes.

## <a name="where-do-you-create-reviews"></a>Onde você cria as revisões?

Dependendo do que você deseja revisar, você criará sua revisão de acesso nas Revisões de Acesso do Azure Active Directory, aplicativos corporativos do Azure Active Directory (visualização) ou PIM do Azure Active Directory.

| Direitos de acesso de usuários | Os revisores podem ser | Revisão criada em | Experiência do revisor |
| --- | --- | --- | --- |
| Membros do grupo de segurança</br>Membros do grupo do escritório | Revisores especificados</br>Proprietários do grupo</br>Revisão por conta própria | Revisões de acesso do Azure AD</br>Grupos do Azure AD | Painel de acesso |
| Atribuído a um aplicativo conectado | Revisores especificados</br>Revisão por conta própria | Revisões de acesso do Azure AD</br>Aplicativos corporativos do Azure Active Directory (visualização) | Painel de acesso |
| Função do diretório do Azure Active Directory | Revisores especificados</br>Revisão por conta própria | Azure AD PIM | Portal do Azure |
| Função de recurso do Azure | Revisores especificados</br>Revisão por conta própria | Azure AD PIM | Portal do Azure |

## <a name="prerequisites"></a>Pré-requisitos

Para usar as revisões de acesso, você deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Licença do Enterprise Mobility + Security (EMS) E5

Para obter mais informações, consulte [Como: Inscrever-se no Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) ou [Avaliação do Enterprise Mobility + Security E5](https://aka.ms/emse5trial).

## <a name="get-started-with-access-reviews"></a>Iniciar com revisões de acesso

Para saber mais sobre como criar e executar as revisões de acesso, assista a este breve demonstração:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se você estiver pronto para implantar as revisões de acesso em sua organização, siga estas etapas no vídeo para integrar, treinar seus administradores e criar sua primeira revisão de acesso!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="enable-access-reviews"></a>Habilitar as revisões de acesso

Para habilitar as revisões de acesso, siga estas etapas.

1. Como um Administrador Global ou administrador de usuário, entrar para o [portal do Azure](https://portal.azure.com) revisões de onde você deseja usar o acesso.

1. Clique em **Todos os serviços** e localize o acesso de revisões de serviço.

1. Clique em **Revisões de acesso**.

    ![Todos os serviços - Revisões de acesso](./media/access-reviews-overview/all-services-access-reviews.png)

1. Na lista de navegação, clique em **Carregar** para abrir a página **Revisões de acesso integrado**.

    ![Revisões de acesso integrado](./media/access-reviews-overview/onboard-button.png)

1. Clique em **Criar** para habilitar o acesso de revisões no diretório atual.

    ![Revisões de acesso integrado](./media/access-reviews-overview/onboard-access-reviews.png)

    Na próxima vez que você inicia o access revisa, as opções de revisão de acesso serão habilitadas.

    ![Revisões de Acesso habilitadas](./media/access-reviews-overview/access-reviews-enabled.png)

## <a name="next-steps"></a>Próximas etapas

- [Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)
- [Criar uma revisão de acesso de usuários em uma função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Examinar o acesso a grupos ou aplicativos](perform-access-review.md)
- [Concluir uma revisão de acesso de aplicativos ou grupos](complete-access-review.md)
