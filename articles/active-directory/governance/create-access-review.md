---
title: Criar uma revisão de acesso de aplicativos - Azure Active Directory ou grupos | Microsoft Docs
description: Saiba como criar uma revisão de acesso de membros do grupo ou o acesso de aplicativo em revisões de acesso do Active Directory do Azure.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/01/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 804efa6e0a39e009e18bbb9dec5ad1638a163597
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495047"
---
# <a name="create-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Criar uma revisão de acesso de grupos ou revisões de acesso de aplicativos no Azure AD

Acesso a grupos e aplicativos para funcionários e visitantes muda ao longo do tempo. Para reduzir os riscos associados às atribuições de acesso obsoletas, os administradores podem usar o Azure Active Directory (Azure AD) para criar revisões de acesso para membros do grupo ou usuários atribuídos a um aplicativo. Se você precisar revisar rotineiramente o acesso, você também pode criar as revisões de acesso recorrentes. Para obter mais informações sobre esses cenários, consulte [Gerenciar acesso de usuário](manage-user-access-with-access-reviews.md) e [Gerenciar acesso de convidado](manage-guest-access-with-access-reviews.md).

Este artigo descreve como criar as revisões de acesso de um ou mais para membros do grupo ou o acesso ao aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- [Revisões de acesso habilitadas](access-reviews-overview.md)
- Administrador global ou administrador de usuário

## <a name="create-one-or-more-access-reviews"></a>Criar um ou mais revisões de acesso

1. Entrar no portal do Azure e abra o [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. No menu à esquerda, clique em **revisões de acesso**.

1. Clique em **Nova análise de acesso** para criar uma nova revisão de acesso.

    ![Revisão do acesso - Controles](./media/create-access-review/access-reviews.png)

1. Nomeie a revisão de acesso. Opcionalmente, forneça uma descrição à revisão. O nome e a descrição são mostrados aos revisores.

    ![Criar uma revisão de acesso - nome da revisão e descrição](./media/create-access-review/name-description.png)

1. Defina a **Data de início**. Por padrão, uma revisão de acesso ocorre uma vez, inicia na mesma hora em que é criada e termina em um mês. Você pode alterar as datas de início e de término para iniciar uma análise de acesso em uma data futura e que dure quantos dias você desejar.

    ![Criar uma revisão de acesso - Data inicial e final](./media/create-access-review/start-end-dates.png)

1. Para fazer a revisão de acesso recorrente, altere o **frequência** definindo a partir **uma vez** para **semanal**, **mensal**,  **Trimestral** ou **anualmente**. Use o **duração** controle deslizante ou caixa de texto para definir quantos dias de cada análise da série de recorrentes será aberto para a entrada de revisores. Por exemplo, a duração máxima que você pode definir para uma revisão mensal é de 27 dias, para evitar revisões sobrepostas.

1. Use a configuração **Final** para especificar como terminar a série de revisão de acesso recorrente. A série pode terminar de três maneiras: ela é executada continuamente para iniciar revisões indefinidamente, até uma data específica ou após a conclusão de um número definido de ocorrências. Você, administrador de outro usuário ou outro administrador Global pode parar a série após a criação, alterando a data no **configurações**, de modo que ele termina nessa data.

1. No **usuários** seção, especifique os usuários que se aplica a revisão de acesso ao. As revisões de acesso podem ser de membros de um grupo ou usuários que foram atribuídos a um aplicativo. Você pode detalhar ainda mais o escopo da análise de acesso para examinar apenas os usuários convidados que são membros (ou atribuídos ao aplicativo), em vez de examinar todos os usuários que são membros ou que têm acesso ao aplicativo.

    ![Criar uma revisão de acesso - Usuários](./media/create-access-review/users.png)

1. No **grupo** , selecione um ou mais grupos que você gostaria de examinar a associação de.

    > [!NOTE]
    > Selecionar mais de um grupo, você criará várias revisões de acesso. Por exemplo, selecionar cinco grupos criará cinco revisões de acesso separados.
    
    ![Criar uma revisão de acesso - selecione o grupo](./media/create-access-review/select-group.png)

1. No **aplicativos** seção (se você selecionou **atribuídos a um aplicativo** na etapa 8), selecione os aplicativos que você gostaria de examinar o acesso ao.

    > [!NOTE]
    > Selecionar mais de um aplicativo, você criará várias revisões de acesso. Por exemplo, selecionar cinco aplicativos criará cinco revisões de acesso separados.
    
    ![Criar uma revisão de acesso – Selecionar aplicativo](./media/create-access-review/select-application.png)

1. Na seção **Revisores**, selecione uma ou mais pessoas para examinar todos os usuários no escopo. Ou você pode selecionar para que os membros examinem seus próprios acessos. Se o recurso for um grupo, você pode pedir que a revisão seja realizada pelos proprietários de grupo. Você também pode exigir que os revisores forneçam um motivo ao aprovar o acesso.

    ![Criar uma revisão de acesso - Revisores](./media/create-access-review/reviewers.png)

1. Na seção **Programas**, selecione o programa que você deseja usar. É possível simplificar a maneira de rastrear e coletar revisões de acesso para finalidades diferentes, organizando-as em programas. O **Programa padrão** está sempre presente ou você pode criar um programa diferente. Por exemplo, é possível optar por ter um programa para cada iniciativa de conformidade ou meta de negócios.

    ![Criar uma revisão de acesso - Programas](./media/create-access-review/programs.png)

### <a name="upon-completion-settings"></a>Após configurações de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a seção **Após configurações de conclusão**.

    ![Após configurações de conclusão](./media/create-access-review/upon-completion-settings.png)

1. Se você quiser remover automaticamente o acesso para usuários que foram negados, defina **Resultados de aplicação automática ao recurso** para **Habilitar**. Se você deseja aplicar manualmente os resultados quando a revisão for concluída, defina a opção para **Desabilitar**.

1. Use a lista **Se o revisor não responder** para especificar o que acontece para usuários que não foram examinados pelo revisor dentro do período de revisão. Essa configuração não afeta os usuários que foram revisados pelos revisores manualmente. Se a decisão do revisor final for negar o acesso do usuário será removido.

    - **Nenhuma alteração** - deixar o acesso do usuário inalterado
    - **Remover o acesso** - remover o acesso do usuário
    - **Aprovar o acesso** - aprovar o acesso do usuário
    - **Fazer recomendações** - levar a recomendação do sistema ao negar ou aprovar o acesso contínuo do usuário

### <a name="advanced-settings"></a>Configurações avançadas

1. Para especificar configurações adicionais, expanda a seção **Configurações avançadas**.

    ![Configurações avançadas](./media/create-access-review/advanced-settings.png)

1. Definir **Mostrar recomendações** à **Habilitar** para mostrar aos revisores as recomendações do sistema com base nas informações de acesso do usuário.

1. Definir **Requer motivo sob aprovação** para **Habilitar** para exigir que o revisor forneça um motivo para aprovação.

1. Definir **Notificações por email** para **Habilitar** para que o Azure Active Directory envie notificações por email para os revisores quando uma revisão de acesso começar e para os administradores quando uma revisão terminar.

1. Defina **Lembretes** para **Habilitar** para que o Azure Active Directory envie lembretes de análises de acesso em andamento para os revisores que não concluíram a sua análise.

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de especificar as configurações para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso será exibido na lista com um indicador de seu status.

![Lista de revisões de acesso](./media/create-access-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise. Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma análise de acesso pendente. Você pode mostrar as instruções de como a [revisar o acesso a grupos ou aplicativos](perform-access-review.md). Se sua revisão for para convidados revisem seus próprios acessos, exibir as instruções de como [revisar o acesso a grupos ou aplicativos por conta própria](review-your-access.md).

Caso alguns revisores sejam convidados, os convidados serão notificados apenas por email se já tiverem aceitado seus convites.

## <a name="manage-the-access-review"></a>Gerenciar a análise de acesso

Você pode acompanhar o progresso conforme os revisores concluem suas revisões na **visão geral** página de revisão de acesso. Nenhum direito de acesso será alterado no diretório até que a [análise seja concluída](complete-access-review.md).

![Progresso de revisões de acesso](./media/create-access-review/overview-progress.png)

Quando se trata de uma única revisão, em seguida, após o período de revisão de acesso ou o administrador tiver interrompido a revisão de acesso, siga as etapas em [concluir uma revisão de acesso de aplicativos ou grupos](complete-access-review.md) para ver e aplicar os resultados.  

Para gerenciar uma série de acesso de revisões, navegue até a revisão de acesso, e você será encontrar ocorrências futuras em revisões agendada e editar a data de término ou adicionar ou remover os revisores adequadamente.

Com base em suas seleções **nas configurações de preenchimento**, será de aplicação automática ser executada após a data de término da análise de ou quando você interromper a revisão manualmente. O status da revisão será alterado de **Completed** estados intermediários, como **aplicando** e, finalmente, para o estado **aplicada**. É necessário esperar que os usuários negados, se houver algum, sejam removidos da associação do grupo ou da atribuição do aplicativo em alguns minutos.

## <a name="create-reviews-via-apis"></a>Criar revisões via APIs

Você também pode criar as revisões de acesso usando as APIs. O que você faz para gerenciar as revisões de acesso dos grupos e usuários do aplicativo no portal do Azure também pode ser feito usando as APIs do Microsoft Graph. Para obter mais informações, consulte o [referência da API de revisões de acesso do Azure AD](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Para obter um exemplo de código, consulte [revisões de exemplo de como recuperar o acesso do Azure AD por meio do Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Próximos passos

- [Examinar o acesso a grupos ou aplicativos](perform-access-review.md)
- [Examinar o acesso a grupos ou aplicativos por conta própria](review-your-access.md)
- [Concluir uma revisão de acesso de aplicativos ou grupos](complete-access-review.md)
