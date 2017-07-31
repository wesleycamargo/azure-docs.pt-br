---
title: "Relatório de segurança de usuários em risco no portal do Azure Active Directory | Microsoft Docs"
description: "Saiba mais sobre o relatório de segurança de usuários em risco no portal do Azure Active Directory"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 01ecb98c02b2a01007c7f76805d4db4b7aeee1f0
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017

---
# <a name="users-at-risk-security-report-in-the-azure-active-directory-portal"></a>Relatório de segurança de usuários em risco no portal do Azure Active Directory

Com os relatórios de segurança no Azure Active Directory (Azure AD) você pode obter informações sobre a probabilidade de contas de usuário comprometidas em seu ambiente. 

O Azure Active Directory detecta ações suspeitas relacionadas às suas contas de usuário. Para cada ação detectada, um registro chamado *evento de risco* é criado. Para obter mais detalhes, veja [Eventos de risco do Azure Active Directory](active-directory-identity-protection-risk-events.md). 

Os eventos de risco detectados são usados para calcular:

- **Entradas arriscadas** - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. Para obter mais detalhes, veja [Entradas arriscadas](active-directory-identityprotection.md#risky-sign-ins). 

- **Usuários sinalizados para riscos** - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. Para obter mais detalhes, veja [Usuários sinalizados para riscos](active-directory-identityprotection.md#users-flagged-for-risk).  

Você pode encontrar os relatórios de segurança no Portal do Azure na folha **Azure Active Directory** na seção **Segurança**.  

![Entradas de risco](./media/active-directory-reporting-security-user-at-risk/10.png)

## <a name="azure-active-directory-free-and-basic-edition"></a>Edições gratuita e básica do Azure Active Directory

O relatório de segurança de usuários em risco nas edições gratuita e básica do Azure Active Directory fornece uma lista de contas de usuário que podem ter sido comprometidas. 


![Entradas de risco](./media/active-directory-reporting-security-user-at-risk/03.png)

A seleção de um usuário abre a respectiva folha de dados de usuário.
Você pode examinar o histórico de entradas dos usuários em risco e redefinir a senha, se necessário.

![Entradas de risco](./media/active-directory-reporting-security-user-at-risk/46.png)

## <a name="azure-active-directory-premium-editions"></a>Edições premium do Azure Active Directory

O relatório de usuários em risco nas edições premium do Azure Active Directory oferece:

- Uma [lista de contas de usuário](active-directory-identityprotection.md#users-flagged-for-risk) que podem ter sido comprometidas 

- Informações agregadas sobre os [tipos de eventos de risco](active-directory-identity-protection-risk-events.md) que foram detectados

- Uma opção para baixar o relatório

- Uma opção para configurar uma [política de correção de risco de usuário](active-directory-identityprotection.md#user-risk-security-policy)  


![Entradas de risco](./media/active-directory-reporting-security-user-at-risk/71.png)

Ao selecionar um usuário, você obtém uma exibição detalhada do relatório deste usuário, que lhe habilita a:

- Abrir a exibição Todas as entradas

- Redefinir a senha do usuário

- Descartar todos os eventos

- Investigar os eventos de risco relatados para o usuário. 


![Entradas de risco](./media/active-directory-reporting-security-user-at-risk/324.png)


Para investigar um evento de risco, selecione um na lista.  
Isso abre a folha de **Detalhes** para este evento de risco. Na folha **Detalhes**, você tem a opção de [fechar manualmente um evento de risco](active-directory-identityprotection.md#closing-risk-events-manually) ou reativar um evento de risco fechado manualmente. 


![Entradas de risco](./media/active-directory-reporting-security-user-at-risk/325.png)



## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o Azure Active Directory Identity Protection, veja [Azure Active Directory Identity Protection](active-directory-identityprotection.md).


