---
title: Criar uma revisão de acesso de membros de um grupo ou usuários com acesso a um aplicativo no Azure AD | Microsoft Docs
description: Saiba como criar uma revisão de acesso para os membros de um grupo ou usuários para o acesso a um aplicativo.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 853d8f09a94e46db218553500a50dc4ef1ec3d23
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448264"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Criar uma revisão de acesso de membros do grupo ou acesso do aplicativo com o Azure AD

As atribuições de acesso se tornam "obsoletas" quando os usuários possuem um acesso que não precisam mais. Para reduzir os riscos associados às atribuições de acesso obsoletas, os administradores podem usar o Azure Active Directory (Azure AD) para criar uma análise de acesso para membros do grupo ou usuários atribuídos a um aplicativo. Criar revisões de acesso recorrentes pode economizar tempo. Se você precisa revisar regularmente os usuários que tenham acesso a um aplicativo ou são membros de um grupo, é possível definir a frequência dessas revisões. Para obter mais informações sobre esses cenários, consulte [Gerenciar acesso de usuário](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) e [Gerenciar acesso de convidado](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Criar uma análise de acesso

1. Como um administrador global ou administrador de conta de usuário, vá para a [página acessar análises](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) e selecione **Programas**.

2. Selecione o programa que mantém o controle de análise de acesso que deseja criar. O **Programa padrão** está sempre presente ou você pode criar um programa diferente. Por exemplo, é possível optar por ter um programa para cada iniciativa de conformidade ou meta de negócios.

3. Dentro do programa, selecione **Controles** e, em seguida, selecione **Adicionar** para adicionar um controle.

4. Nomeie a revisão de acesso. Opcionalmente, forneça uma descrição à revisão. O nome e a descrição são mostrados aos revisores.

5. Defina a data de início. Por padrão, uma revisão de acesso ocorre uma vez, inicia na mesma hora em que é criada e termina em um mês. Você pode alterar as datas de início e de término para iniciar uma análise de acesso em uma data futura e que dure quantos dias você desejar.

6. Para tornar a revisão de acesso recorrente, altere a frequência de Uma vez para Semanal, Mensal, Trimestral ou Anual e use o controle deslizante ou caixa de texto para definir quantos dias cada revisão da série recorrente será aberta para entrada dos revisores. Por exemplo, a duração máxima que você pode definir para uma revisão mensal é de 27 dias, para evitar avaliações sobrepostas. 

7.  A série de revisão de acesso recorrente pode terminar de 3 maneiras: ela é executada continuamente para iniciar revisões indefinidamente, até uma data específica ou após a conclusão de um número definido de ocorrências. Você, outro usuário administrador de conta, ou outro administrador global pode interromper a série após a criação, alterando a data em Configurações para que ela encerre nessa data.

8. As revisões de acesso podem ser de membros de um grupo ou usuários que foram atribuídos a um aplicativo. Você pode detalhar ainda mais o escopo da análise de acesso para examinar apenas os usuários convidados que são membros (ou atribuídos ao aplicativo), em vez de examinar todos os usuários que são membros ou que têm acesso ao aplicativo.

9. Selecione uma ou mais pessoas para examinar todos os usuários no escopo. Ou você pode selecionar para que os membros examinem seus próprios acessos. Se o recurso for um grupo, você pode pedir que a revisão seja realizada pelos proprietários de grupo. Você também pode exigir que os revisores forneçam um motivo ao aprovar o acesso.

10. Se você deseja aplicar manualmente os resultados quando a revisão for concluída, clique em **Iniciar**.  Caso contrário, a próxima seção explica como configurar a revisão para aplicação automática.

### <a name="configuring-an-access-review-with-auto-apply"></a>Configurar uma revisão de acesso com aplicação automática

1.  Expanda o menu para Após as configurações de conclusão e ative Aplicar automaticamente os resultados ao recurso. 

2.  Nos casos em que os usuários não foram revisados pelo revisor dentro do período de revisão, você pode fazer a revisão de acesso aceitar a recomendação do sistema (se habilitada) em negar / aprovar o acesso continuado do usuário, manter inalterado o acesso ou remover o acesso. Isso não afetará os usuários que foram revisados pelos revisores manualmente – se a decisão do revisor final for Negar, o acesso do usuário será removido.

3.  Para ativar a opção de receber recomendações, os revisores não devem responder, expanda Configurações avançadas e ative Exibir recomendações.
 
4.  Por fim, clique em **Iniciar**.

Com base nas seleções em Após configurações de conclusão, a aplicação automática será executada após a data de término da revisão ou quando a revisão for interrompida manualmente. O status da revisão será alterado de Concluído para estados intermediários, como Aplicando e, finalmente, para Estado Aplicado. É necessário esperar que os usuários negados, se houver algum, sejam removidos da associação do grupo ou da atribuição do aplicativo em alguns minutos.


## <a name="manage-the-access-review"></a>Gerenciar a análise de acesso

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise. Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma análise de acesso pendente. Você pode mostrar a eles as instruções de como [examinar o acesso](active-directory-azure-ad-controls-perform-access-review.md). Se sua análise for para convidados que examinam o próprio acesso, mostre a eles as instruções de como [examinar seu próprio acesso](active-directory-azure-ad-controls-perform-access-review.md).

Caso alguns revisores sejam convidados, os convidados serão notificados apenas por email se já tiverem aceitado seus convites.

Para gerenciar uma série de revisões de acesso, navegue até a revisão de acesso a partir de **Controles** e você localizará as próximas ocorrências em Revisões agendadas, edite a data de término ou adicione/remova os revisores adequadamente. 

É possível acompanhar o andamento conforme os revisores concluem suas revisões no painel do Azure AD na seção **Revisões de Acesso**. Nenhum direito de acesso será alterado no diretório até que a [análise seja concluída](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Próximas etapas

Quando uma análise de acesso é iniciada, o Azure AD enviará automaticamente aos revisores um email solicitando que examinem o acesso. Se um usuário não tiver recebido um email, você poderá enviar a ele as instruções de como [analisar o acesso](active-directory-azure-ad-controls-perform-access-review.md). 

Se essa for uma revisão única, depois que o período de revisão de acesso terminar ou o administrador interromper a revisão de acesso, execute as etapas em [Concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md) para ver e aplicar os resultados.  

Se essa for uma série de revisão, navegue até o **Histórico da revisão** na página da série de revisão de acesso para selecionar uma revisão de acesso completa.  As próximas revisões serão listadas em **Revisão agendada**, onde será possível editar a duração e adicionar ou remover revisores para comentários individuais.
