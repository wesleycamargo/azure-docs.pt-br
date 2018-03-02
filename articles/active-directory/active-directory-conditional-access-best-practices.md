---
title: "Práticas recomendadas para o acesso condicional no Azure Active Directory | Microsoft Docs"
description: "Saiba sobre o que você deve saber e o que você deve evitar fazer ao configurar políticas de acesso condicional."
services: active-directory
keywords: "acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 16f9179b6cbaee00a2afbe2efe090cb3eb8b204a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Práticas recomendadas para o acesso condicional no Azure Active Directory

Com o [acesso condicional do Azure AD (Azure Active Directory)](active-directory-conditional-access-azure-portal.md), você pode controlar como os usuários autorizados acessam seus aplicativos de nuvem. Este artigo fornece informações sobre:

- O que você deve saber 
- O que deve é necessário evitar ao configurar as políticas de acesso condicional. 

Este artigo assume que você está familiarizado com os conceitos e a terminologia descrita em [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md)



## <a name="whats-required-to-make-a-policy-work"></a>O que é necessário para fazer uma política funcionar?

Ao criar uma nova política, não há usuários, grupos, aplicativos ou controles de acesso selecionados.

![Aplicativos na nuvem](./media/active-directory-conditional-access-best-practices/02.png)


Para que a política funcione, você deve configurar:


|O que           | Como                                  | Porque|
|:--            | :--                                  | :-- |
|**Aplicativos na nuvem** |Você precisa selecionar um ou mais aplicativos.  | A meta de uma política de acesso condicional é permitir que você controle como os usuários autorizados podem acessar aplicativos na nuvem.|
| **Usuários e grupos** | É necessário selecionar pelo menos um usuário ou grupo autorizado para acessar os aplicativos na nuvem selecionados. | Uma política de acesso condicional que não tenha usuários e grupos atribuídos nunca será disparada. |
| **Controles de acesso** | Você precisa selecionar pelo menos um controle de acesso. | Se as condições forem atendidas, o processador de política precisará saber o que fazer.|




## <a name="what-you-should-know"></a>O que você deve saber

### <a name="how-are-assignments-evaluated"></a>Como as atribuições são avaliadas?

Todas as atribuições são avaliadas com **AND** lógicos. Se você tiver mais de uma atribuição configurada, todas as atribuições deverão ser atendidas para disparar uma política.  

Se for necessário configurar uma condição de local que se aplique a todas as conexões feitas de fora da rede da organização:

- Incluindo **Todos os locais**
- Excluir **Todos os IPs confiáveis**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>O que fazer se você estiver bloqueado no portal de administração do Azure Active Directory?

Se você estiver bloqueado do Portal do Azure Active Directory devido a uma configuração incorreta em uma política de acesso condicional:

- Verifique se existem outros administradores em sua organização que ainda não estão bloqueados. Um administrador com acesso ao Portal do Azure pode desabilitar a política que está afetando sua entrada. 

- Se nenhum dos administradores da sua organização puder atualizar a política, será necessário enviar uma solicitação de suporte. O suporte da Microsoft pode analisar e atualizar as políticas de acesso condicional que estão impedindo o acesso.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>O que acontece se você tiver políticas configuradas no portal clássico do Azure e no portal do Azure?  

Ambas as políticas são aplicadas pelo Azure Active Directory e o usuário só obterá acesso quando todos os requisitos forem atendidos.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>O que acontece se você tiver políticas no Portal do Intune Silverlight e no Portal do Azure?

Ambas as políticas são aplicadas pelo Azure Active Directory e o usuário só obterá acesso quando todos os requisitos forem atendidos.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>O que acontece se eu tiver várias políticas configuradas para o mesmo usuário?  

Para cada entrada, o Azure Active Directory avalia todas as políticas e garante que todos os requisitos sejam atendidos antes que o acesso seja concedido ao usuário.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>O acesso condicional funciona com o Exchange ActiveSync?

Sim, você não pode usar o Exchange ActiveSync em uma política de acesso condicional.






## <a name="what-you-should-avoid-doing"></a>O que você deve evitar

A estrutura de acesso condicional fornece uma excelente flexibilidade de configuração. No entanto, uma grande flexibilidade também significa que é necessário examinar cuidadosamente cada política de configuração, antes de liberá-la, para evitar resultados indesejáveis. Nesse contexto, preste atenção especial às atribuições que afetam conjuntos completos, como **todos os usuários/grupos/aplicativos de nuvem**.

Em seu ambiente, evite as configurações a seguir:


**Para todos os usuários, todos os aplicativos de nuvem:**

- **Bloquear o acesso** - Essa configuração bloqueia toda a organização, o que certamente não é uma boa ideia.

- **Exigir dispositivo compatível** - Para usuários que ainda não registraram seus dispositivos, esta política bloqueia todo acesso, incluindo acesso ao portal do Intune. Se você for um administrador sem um dispositivo registrado, essa política impedirá você voltar ao Portal do Azure para alterar a política.

- **Exigir ingresso no domínio** - Esta política de bloqueio de acesso também tem o potencial para bloquear o acesso de todos os usuários em sua organização se você ainda não tiver um dispositivo ingressado no domínio.


**Para todos os usuários, todos os aplicativos de nuvem, todas as plataformas de dispositivo:**

- **Bloquear o acesso** - Essa configuração bloqueia toda a organização, o que certamente não é uma boa ideia.


## <a name="how-should-you-deploy-a-new-policy"></a>Como você deve implantar uma nova política?

Como uma primeira etapa, é necessário avaliar a política, utilizando uma [ferramenta E-Se](active-directory-conditional-access-whatif.md).

Quando estiver pronto para implantar uma nova política no ambiente, isso deverá ser feito em fases:

1. Aplique uma política a um conjunto de usuários pequeno e verifique se o comportamento é conforme esperado. 

2.  Ao expandir uma política para incluir mais usuários, continue excluindo todos os administradores da política. Isso garante que os administradores ainda terão acesso e poderão atualizar uma política, se for necessário fazer uma alteração.

3. Aplique uma política a todos os usuários, somente se isso realmente for necessário. 

Como melhor prática, crie uma conta de usuário que seja:

- Dedicado à administração de política 
- Excluído de todas as suas políticas


## <a name="policy-migration"></a>Migração de política

Considere migrar as políticas que você não tiver criado no Portal do Azure porque:

- Agora você pode abordar cenários que você não podia manipular antes.

- Você pode consolidar as políticas e, dessa forma, reduzir o número de políticas a serem gerenciadas.   

- Você pode gerenciar todas as políticas de acesso condicional em um local central.

- O Portal Clássico do Azure foi desativado.   


Para obter mais informações, consulte [Migrar políticas clássicas no portal do Azure](active-directory-conditional-access-migration.md).


## <a name="next-steps"></a>Próximas etapas

Se você quiser saber como configurar uma política de acesso condicional, veja [Introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).
