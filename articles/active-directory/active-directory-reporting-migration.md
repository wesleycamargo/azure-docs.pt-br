---
title: "Como localizar relatórios de atividade no Portal do Azure | Microsoft Docs"
description: "Saiba como localizar relatórios de atividade no Portal do Azure"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/17/2017
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 800397efdf2e3e3cb95c77bc90f45bb8852e1e42
ms.openlocfilehash: d05cf7dc62518ea5519f319a2502e1d261b148f3
ms.lasthandoff: 02/17/2017


---
# <a name="how-to-find-activity-reports-in-the-azure-portal"></a>Como localizar relatórios de atividade no Portal do Azure

Com a migração do Portal Clássico do Azure para o Portal do Azure, estamos fornecendo você com uma nova aparência para os Logs de atividade do Azure Active Directory. Lançamos uma [postagem de blog](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/) alguns meses atrás que explica como fornecemos logs de atividade no contexto do recurso em que você está trabalhando. Este artigo descreve como convertemos os relatórios existentes no Portal Clássico do Azure no novo mundo do Portal do Azure.

## <a name="what-is-new"></a>O que há de novo?

Os relatórios no Portal Clássico do Azure são divididos em várias categorias:

1.    Relatórios de segurança
2.    Relatórios de Atividades
3.    Relatórios do Aplicativo Integrado

### <a name="activity-and-integrated-app-reports"></a>Relatórios do aplicativo integrado e de atividade

Mudando para o relatório com base em contexto no Portal do Azure, mesclamos os relatórios existentes em uma única exibição com uma única API subjacente que fornece os dados para o modo de exibição. Você pode encontrar esse modo de exibição em "*Atividades*" > Logs de Auditoria no Portal do Azure (navegação à esquerda). A seguir estão os relatórios que foram consolidados neste modo de exibição.

-    Relatório de auditoria

-     Atividade de redefinição de senha

-     Atividade de registro de redefinição de senha

-     Atividade de grupos de autoatendimento

-     Alterações de nome do grupo do Office365

-     Atividade de provisionamento de conta

-     Status de substituição de senha
-     Erros de provisionamento de conta


O relatório de Uso do Aplicativo foi aprimorado e incluído em uma exibição chamada de "*Atividades*"-> Entradas (navegação à esquerda) e inclui todas as entradas de usuários, as quais podem ser derivadas para obtenção das informações de uso do aplicativo. Você também pode encontrar informações de uso do aplicativo por meio da tela de visão geral "Aplicativos Corporativos".

## <a name="how-can-i-access-a-specific-report-in-this-single-view"></a>Como acessar um relatório específico neste modo de exibição único?

### <a name="audit-logs"></a>Logs de auditoria

Uma das principais reivindicações de muitos clientes foi a capacidade de ter várias opções de filtro para acessar logs de atividade no Azure AD. Em vez disso, fornecemos o mecanismo de filtragem avançado para filtrar os dados que você deseja. Um dos filtros fornecidos é chamado "*Categoria da Atividade*", que lista os diferentes tipos de logs de atividade que o Azure AD fornece. Escolhendo a categoria desejada, você pode restringir os resultados dos logs de atividade àqueles que você está interessado em ver. 

Por exemplo, se você estiver interessado em obter apenas atividades relacionadas à **Redefinição de senha de autoatendimento**, você poderá escolher a categoria **Gerenciamento de senhas de autoatendimento**. As categorias que você pode ver estão no contexto do recurso no qual você está trabalhando.  


![Logs de auditoria](./media/active-directory-reporting-migration/06.png "Logs de auditoria")

 
As diversas categorias que temos hoje incluem:

- Diretório principal

- Gerenciamento de senhas de auto-atendimento

- Gerenciamento de grupos de autoatendimento

- Provisionamento de conta de usuário

### <a name="application-usage"></a>Uso do aplicativo

Você pode exibir o uso do aplicativo para todos os aplicativos ou um único aplicativo por meio da exibição Atividades-> Entradas. Conforme mostrado abaixo, essa exibição está presente para todos os aplicativos ou um único aplicativo. Se você quiser restringir os resultados, poderá filtrar por Nome de Usuário ou Nome do Aplicativo.
 

![Logs de auditoria](./media/active-directory-reporting-migration/07.png "Logs de auditoria")


### <a name="security-reports"></a>Relatórios de segurança

Os relatórios de segurança foram consolidados para fornecer uma exibição de todos os sinais de riscos que o Azure Active Directory pode detectar e relatar.

Essa exibição consolidada fornece dados sobre:

- Usuários em risco
- Entradas de risco 


![Logs de auditoria](./media/active-directory-reporting-migration/04.png "Logs de auditoria")



## <a name="activity-reports-in-azure-classic-portal-versus-azure-portal"></a>Relatórios de atividade no Portal Clássico do Azure versus Portal do Azure

Esta seção lista os relatórios existentes no Portal Clássico do Azure e como você pode obter essas informações no Portal do Azure.

**Logs de auditoria:**

1. No painel de navegação à esquerda, clique em **Atividades** e em **Logs de Auditoria**
2. Como **Categoria da Atividade**, selecione **Diretório Principal** 

**Atividade de redefinição de senha:**

1. No painel de navegação à esquerda, clique em **Atividades** e em **Logs de Auditoria**
2. Como a **Categoria da Atividade**, selecione **Gerenciamento de Senhas de Autoatendimento** 

**Atividade de registro de redefinição de senha:**    

1. No painel de navegação à esquerda, clique em **Atividades** e em **Logs de Auditoria**
2. Como a **Categoria da Atividade**, selecione **Gerenciamento de Senhas de Autoatendimento**.

**Atividade de grupos de autoatendimento:**    

1. No painel de navegação à esquerda, clique em **Atividades** e em **Logs de Auditoria**
2. Como a **Categoria da Atividade**, selecione **Gerenciamento de Grupos de Autoatendimento**

**Alterações de nome do grupo do Office365:**

1. No painel de navegação à esquerda, clique em **Atividades** e em **Logs de Auditoria**
2. Como a **Categoria da Atividade**, selecione **Gerenciamento de Senhas de Autoatendimento**
3. Como **Tipo de Recurso de Atividade**, selecione **Grupo** 
4. Como **Origem da Atividade**, selecione **Grupos do O365** 

**Atividade de provisionamento de conta:**    

1. No painel de navegação à esquerda, clique em **Atividades** e em **Logs de Auditoria**
2. Como **Categoria da Atividade**, selecione **Provisionamento da Conta de Usuário**

**Status de substituição de senha:**    

1. No painel de navegação à esquerda, clique em **Atividades** e em **Logs de Auditoria**
2. Como **Categoria da Atividade**, selecione **Substituição Automática de Senha de Aplicativo**

**Erros de provisionamento de conta:**

1. No painel de navegação à esquerda, clique em **Atividades** e em **Logs de Auditoria**
2. Como a **Categoria da Atividade**, selecione **Provisionamento da Conta de Usuário**

**Uso do Aplicativo:**

- No painel de navegação esquerdo, clique em **Aplicativos Corporativos** e, em seguida, clique em **Entradas**



