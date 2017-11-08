---
title: "Concluir uma análise de acesso de membros de um grupo ou o acesso de usuários a um aplicativo com Azure AD | Microsoft Docs"
description: "Saiba como concluir uma revisão de acesso para membros de um grupo ou os usuários com acesso a um aplicativo no Azure Active Directory."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: b301ff06c01d51c02f7d7393801b35cd8965403c
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2017
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Concluir uma análise de acesso de membros de um grupo ou o acesso de usuários a um aplicativo no Azure AD

Os administradores podem usar o Azure Active Directory (Azure AD) para [criar uma análise de acesso](active-directory-azure-ad-controls-create-access-review.md) para membros do grupo ou os usuários atribuídos a um aplicativo. O Azure AD envia automaticamente, para os revisores, um email solicitando que analisem o acesso. Se um usuário não recebeu um email, é possível enviar para ele as instruções em [Examinar seu acesso](active-directory-azure-ad-controls-perform-access-review.md). Depois que o período de análise de acesso tiver acabado ou se um administrador interromper a análise de acesso, siga as etapas deste artigo para ver e aplicar os resultados.

## <a name="view-an-access-review-in-the-azure-portal"></a>Exibir uma análise de acesso no Portal do Azure

1. Vá para a [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), selecione **Programas** e escolha o programa que contém o controle de análise de acesso.

2. Selecione **Gerenciar** e selecione o controle de análise de acesso. Se houver muitos controles no programa, você poderá filtrar os controles de um tipo específico e classificar pelo status. Você também pode pesquisar pelo nome do controle da análise de acesso ou pelo nome de exibição do proprietário que o criou. 

## <a name="stop-a-review-that-hasnt-finished"></a>Parar uma análise que não foi concluída

Se a análise não tiver atingido a data de término agendada, um administrador poderá selecionar **Parar** para finalizar a análise mais cedo. Depois que a análise for interrompida, os usuários não poderão ser analisados. Não é possível reiniciar uma análise depois de ter sido interrompida.

## <a name="apply-the-changes"></a>Aplicar as alterações 

Depois que uma análise de acesso for concluída, seja porque atingiu a data de término ou um administrador a interrompeu manualmente, selecione **Aplicar**. O resultado da análise é implementado ao atualizar o grupo ou o aplicativo. Se o acesso do usuário tiver sido negado na análise, quando o administrador selecionar esta opção, o Azure AD removerá sua atribuição de associação ou aplicativo. 

Selecionar **Aplicar** não causará efeito em um grupo gerado em um diretório local ou para um grupo dinâmico. Se você quiser alterar um grupo que se origina localmente, baixe os resultados e aplique essas alterações para a representação do grupo neste diretório.

## <a name="download-the-results-of-the-review"></a>Baixar os resultados da análise

Para recuperar os resultados da análise, selecione **Aprovações** e, em seguida, selecione **Baixar**. O arquivo CSV resultante pode ser exibido no Excel ou em outros programas que abrem os arquivos CSV.

## <a name="optional-delete-a-review"></a>Opcional: excluir uma análise
Caso não esteja mais interessado na análise, você poderá excluí-la. Selecione **Excluir** para remover a análise do Azure AD.

> [!IMPORTANT]
> Não há nenhum aviso antes da exclusão, portanto, certifique-se de que deseja excluir a análise.
> 
> 

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar o acesso do usuário com revisões de acesso ao Azure AD](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Gerenciar o acesso de convidado com revisões de acesso do Azure AD](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Gerenciar os programas e os controles para análises de acesso do Azure AD](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Como criar uma revisão de acesso para os membros de um grupo ou para o acesso a um aplicativo](active-directory-azure-ad-controls-create-access-review.md)
- [Criar uma revisão de acesso de usuários em uma função administrativa do Azure AD](active-directory-privileged-identity-management-how-to-start-security-review.md)
