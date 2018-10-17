---
title: Usuários sinalizados para o relatório de risco na segurança no portal do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre os usuários sinalizados para o relatório de risco na segurança no portal do Azure Active Directory
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/14/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fde063cb593ca1f610dc35cd044fe41e34ab9202
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578339"
---
# <a name="users-flagged-for-risk-security-report-in-the-azure-active-directory-portal"></a>Usuários sinalizados para o relatório de risco na segurança no portal do Azure Active Directory

Com os relatórios de segurança no Azure Active Directory (Azure AD), você pode obter informações sobre a probabilidade de contas de usuário comprometidas em seu ambiente. 

O Azure Active Directory detecta ações suspeitas relacionadas às suas contas de usuário. Para cada ação detectada, um registro chamado *evento de risco* é criado. Para saber mais, veja [Eventos de risco do Azure Active Directory](concept-risk-events.md). 

Os eventos de risco detectados são usados para calcular:

- **Entradas arriscadas** - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. Para saber mais, confira [Como configurar a política de risco de entrada](../identity-protection/howto-sign-in-risk-policy.md). 

- **Usuários sinalizados para riscos** - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. Para saber mais, confira [Como configurar a política de risco do usuário](../identity-protection/howto-user-risk-policy.md).  

Você pode encontrar os relatórios de segurança no Portal do Azure na folha **Azure Active Directory** na seção **Segurança**.  

![Entradas de risco](./media/concept-user-at-risk/10.png)



## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Qual licença do Azure AD você precisa para acessar a atividade de entrada?  

Todas as edições do Azure Active Directory fornecem relatórios de usuários sinalizados como risco.  
No entanto, o nível de granularidade do relatório varia entre as edições: 

- Nas **edições do Azure Active Directory Gratuita e Basic**, você obtém uma lista de usuários sinalizados como risco. 

- A edição do **Azure Active Directory Premium 1** estende esse modelo, também permitindo que você examine alguns dos eventos de risco subjacentes que foram detectados para cada relatório. 

- A edição do **Azure Active Directory Premium 2** fornece as informações mais detalhadas sobre todos os eventos de risco subjacentes e permite configurar políticas de segurança que atendem automaticamente aos níveis de risco configurados.



## <a name="azure-active-directory-free-and-basic-edition"></a>Edições gratuita e básica do Azure Active Directory

Os usuários sinalizados para relatório de risco nas edições gratuita e básica do Azure Active Directory fornece uma lista de contas de usuário que podem ter sido comprometidas. 


![Entradas de risco](./media/concept-user-at-risk/03.png)

A seleção de um usuário abre a respectiva folha de dados de usuário.
Você pode examinar o histórico de entradas dos usuários em risco e redefinir a senha, se necessário.

![Entradas de risco](./media/concept-user-at-risk/46.png)


Essa caixa de diálogo fornece uma opção para:

- Baixar o relatório

- Pesquisar usuários

![Entradas de risco](./media/concept-user-at-risk/16.png)


## <a name="azure-active-directory-premium-editions"></a>Edições premium do Azure Active Directory

Os usuários sinalizados para o relatório de risco nas edições premium do Azure Active Directory fornecem:

- Uma lista de contas de usuário que pode ter sido comprometida 

- Informações agregadas sobre os [tipos de eventos de risco](concept-risk-events.md) que foram detectados

- Uma opção para baixar o relatório

- Uma opção para configurar uma [política de correção de risco de usuário](../identity-protection/howto-user-risk-policy.md)  


![Entradas de risco](./media/concept-user-at-risk/71.png)

Ao selecionar um usuário, você obtém uma exibição detalhada do relatório deste usuário, que lhe habilita a:

- Abrir a exibição Todas as entradas

- Redefinir a senha do usuário

- Descartar todos os eventos

- Investigar os eventos de risco relatados para o usuário. 


![Entradas de risco](./media/concept-user-at-risk/324.png)


Para investigar um evento de risco, selecione um na lista para abrir a folha **Detalhes** para esse evento de risco. Na folha **Detalhes**, existe a opção de fechar manualmente um evento de risco ou reativar um evento de risco fechado manualmente. 


![Entradas de risco](./media/concept-user-at-risk/325.png)



## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o Azure Active Directory Identity Protection, veja [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

