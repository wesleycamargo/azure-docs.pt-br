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
ms.component: compliance
ms.date: 11/19/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: fe51419106f1164f9a9b5993261c61bad63333b5
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262951"
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

- **Número excessivo de usuários em funções privilegiadas:** é uma boa ideia verificar quantos usuários têm acesso administrativo, quantos deles são administradores globais e se há algum convidado ou parceiro que não foi removido depois de ter sido atribuído a uma tarefa administrativa. Você pode renovar os usuários de atribuição de função nas [funções de diretório do Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), como os administradores globais, ou [funções de recursos do Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) como Administrador de acesso do usuário na experiência do [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md).
- **Quando a automação for inviável:** você pode criar regras de associação dinâmicas em grupos de segurança ou grupos do Office 365, mas e se os dados de RH não estiverem no Azure AD ou se os usuários ainda precisam de acesso após deixar o grupo para treinar seu substituto? Em seguida, você pode criar uma revisão nesse grupo para garantir que aqueles que ainda precisam de acesso devem ter acesso contínuo.
- **Quando um grupo é usado para uma nova finalidade:** se você tiver um grupo que será sincronizado ao Azure Active Directory, ou se você planeja habilitar o aplicativo Salesforce para qualquer pessoa no grupo de equipe de vendas, seria útil pedir ao proprietário do grupo para examinar a associação do grupo antes de o grupo ser usado em um conteúdo de risco diferente.
- **Acesso de dados críticos de negócios:** para certos recursos, talvez seja necessário pedir que as pessoas fora do departamento de TI regularmente, saiam e forneçam uma justificativa sobre por que eles precisam de acesso para fins de auditoria.
- **Manter uma lista de exceção de política:** em um mundo ideal, todos os usuários seguiriam as políticas de acesso para proteger o acesso aos recursos de sua organização. No entanto, às vezes, há casos comerciais em que é necessário fazer exceções. Como o administrador de TI, você pode gerenciar essa tarefa, evitar supervisão de exceções à política e fornecer os auditores a comprovação de que essas exceções são revisadas regularmente.
- **Peça aos proprietários de grupo para confirmar se eles ainda precisam de convidados em seus grupos:** o acesso de funcionários pode ser automatizado com alguns IAM no local, mas não os convidados. Se um grupo oferece acesso de convidados para conteúdo confidencial da empresa, em seguida, é responsabilidade do proprietário do grupo se confirmar os convidados ainda terão uma necessidade comercial legítima de acesso.
- **Realizar revisões periodicamente:** você pode configurar revisões de acesso recorrente de usuários no conjunto de frequências como mensal, trimestral de semanalmente, ou anualmente e os revisores serão notificados no início de cada revisão. Os revisores podem aprovar ou negar acesso com uma interface amigável e com a ajuda de recomendações inteligentes.

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

Para obter mais informações, consulte [Como: Inscrever-se no Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) ou avaliação [Enterprise Mobility + Security E5](http://aka.ms/emse5trial).

## <a name="get-started-with-access-reviews"></a>Iniciar com revisões de acesso

Para saber mais sobre como criar e executar as revisões de acesso, assista a este breve demonstração:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se você estiver pronto para implantar as revisões de acesso em sua organização, siga estas etapas no vídeo para integrar, treinar seus administradores e criar sua primeira revisão de acesso!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="enable-access-reviews"></a>Habilitar as revisões de acesso

Para habilitar as revisões de acesso, siga estas etapas.

1. Como Administrador Global ou Administrador da Conta de Usuário, entre no [portal do Azure](https://portal.azure.com) onde você quer usar as revisões de acesso.

1. Clique em **Todos os serviços** e localize o acesso de revisões de serviço.

    ![Todos os serviços - Revisões de acesso](./media/access-reviews-overview/all-services-access-reviews.png)

1. Clique em **Revisões de acesso**.

    ![Revisões de acesso integrado](./media/access-reviews-overview/onboard-button.png)

1. Na lista de navegação, clique em **Carregar** para abrir a página **Revisões de acesso integrado**.

    ![Revisões de acesso integrado](./media/access-reviews-overview/onboard-access-reviews.png)

1. Clique em **Criar** para habilitar o acesso de revisões no diretório atual. Na próxima vez que você iniciar as revisões de acesso, as opções serão habilitadas.

    ![Revisões de Acesso habilitadas](./media/access-reviews-overview/access-reviews-enabled.png)

## <a name="next-steps"></a>Próximas etapas

- [Como criar uma revisão de acesso para os membros de um grupo ou para o acesso a um aplicativo](create-access-review.md)
- [Criar uma revisão de acesso de usuários em uma função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Realize uma revisão de acesso com as Revisões de Acesso do Microsoft Azure Active Directory](perform-access-review.md)
- [Concluir uma análise de acesso de membros de um grupo ou o acesso de usuários a um aplicativo no Azure AD](complete-access-review.md)
