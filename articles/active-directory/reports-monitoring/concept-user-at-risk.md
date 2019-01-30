---
title: Usuários sinalizados para o relatório de risco na segurança no portal do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre os usuários sinalizados para o relatório de risco na segurança no portal do Azure Active Directory
services: active-directory
author: priyamohanram
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/17/2019
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: de9cf73ba7ad68d84e43a7774617959f9c3789ee
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810747"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Relatório de usuários sinalizados para risco no portal do Azure

O Azure Active Directory (Azure AD) detecta ações suspeitas relacionadas às suas contas de usuário. Para cada ação detectada, um registro chamado [evento de risco](concept-risk-events.md) é criado.

Você pode acessar os relatórios de segurança do [portal do Azure](https://portal.azure.com) selecionando a folha **Azure Active Directory** e, em seguida, navegando até a seção **Segurança**. 

Os eventos de risco detectados são usados para calcular:

- **Entradas arriscadas** - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. 

- **Usuários sinalizados para riscos** - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. 

Para saber como configurar as políticas que disparam esses eventos de risco, consulte [Como configurar a política de risco do usuário](../identity-protection/howto-user-risk-policy.md). 

![Entradas de risco](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Qual é a licença do Azure AD necessária para acessar o relatório de usuários em risco?  

Todas as edições do Azure Active Directory fornecem relatórios de usuários sinalizados como risco. No entanto, o nível de granularidade do relatório varia entre as edições: 

- Nas **edições do Azure Active Directory Gratuito e Básico**, você obtém uma lista de usuários sinalizados como risco. 

- Além disso, a edição do **Azure Active Directory Premium 1** permite que você examine alguns dos eventos de risco subjacentes que foram detectados para cada relatório. 

- A edição do **Azure Active Directory Premium 2** fornece as informações mais detalhadas sobre todos os eventos de risco subjacentes e também permite configurar políticas de segurança que atendem automaticamente aos níveis de risco configurados.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>O relatório de usuários em risco para as edições gratuita e básica do Azure AD

Os usuários sinalizados para relatório de risco nas edições gratuita e básica do Azure AD fornece uma lista de contas de usuário que podem ter sido comprometidas. 

![Entradas de risco](./media/concept-user-at-risk/03.png)

A seleção de um usuário fornece informações de entrada. Você pode examinar o histórico de entradas dos usuários em risco e redefinir a senha, se necessário.

Essa caixa de diálogo fornece uma opção para:

- Baixar o relatório
- Pesquisar usuários

    ![Entradas de risco](./media/concept-user-at-risk/16.png)

Para obter informações mais detalhadas, você precisa de uma licença premium.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Relatório de usuários em risco para as edições premium do Azure AD

O relatório de usuários sinalizados para risco nas edições premium do Azure AD fornece a você:

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

- [Como configurar a política de risco do usuário](../identity-protection/howto-user-risk-policy.md)
- [Como configurar a política de correção de risco](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

