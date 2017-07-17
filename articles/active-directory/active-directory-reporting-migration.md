---
title: "Localizar relatórios de atividade no Portal do Azure | Microsoft Docs"
description: "Saiba como localizar relatórios de atividade do Azure Active Directory no Portal do Azure."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 7f6d82d211e9b3d25b0efe62ab8dd32f827b08ef
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017

---
# Localizar relatórios de atividade no Portal do Azure
<a id="find-activity-reports-in-the-azure-portal" class="xliff"></a>

Se você estiver mudando do Portal Clássico do Azure para o Portal do Azure, dê uma nova olhada nos logs de atividade do Azure Active Directory (Azure AD). Em uma [postagem de blog](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/) recente, explicamos como você pode ver os logs de atividade no contexto do recurso no qual você está trabalhando no Portal do Azure. Neste artigo, descrevemos como localizar relatórios que você usou no Portal Clássico do Azure no Portal do Azure.

## Novidades
<a id="whats-new" class="xliff"></a>

Os relatórios no Portal Clássico do Azure são separados em categorias:

1.  Relatórios de segurança
2.  Relatórios de atividades
3.  Relatórios de aplicativo integrados

### Relatórios do aplicativo integrado e de atividade
<a id="activity-and-integrated-app-reports" class="xliff"></a>

Para relatórios baseados no contexto no Portal do Azure, os relatórios existentes são mesclados em uma única exibição. Uma única API subjacente fornece os dados para a exibição.

Para ver essa exibição, na folha **Azure Active Directory**, em **ATIVIDADE**, selecione **logs de auditoria**.

![Logs de auditoria](./media/active-directory-reporting-migration/482.png "Logs de auditoria")

Os relatórios a seguir são consolidados nessa exibição:

-   Relatório de auditoria
-   Atividade de redefinição de senha
-   Atividade de registro de redefinição de senha
-   Atividade dos grupos de autoatendimento
-   Alterações de nome do grupo do Office365
-   Atividade de provisionamento de conta
-   Status de substituição de senha
-   Erros de provisionamento de conta


O relatório Uso do Aplicativo foi aprimorado e está incluído na exibição **Entradas**. Para ver essa exibição, na folha **Azure Active Directory**, em **ATIVIDADE**, selecione **Entradas**.

![Exibição de entradas](./media/active-directory-reporting-migration/483.png "Exibição de entradas")

A exibição **Entradas** inclui todas as entradas de usuário. Use essas informações para obter informações de uso do aplicativo. Também é possível exibir informações de uso do aplicativo na visão geral **Aplicativos empresariais**, na seção **GERENCIAR**.

![Aplicativos empresariais](./media/active-directory-reporting-migration/484.png "Aplicativos empresariais")

## Acessar uma porta específica
<a id="access-a-specific-report" class="xliff"></a>

Embora o Portal do Azure ofereça uma exibição única, também é possível examinar relatórios específicos.

### Logs de auditoria
<a id="audit-logs" class="xliff"></a>

Em resposta aos comentários dos clientes, no Portal do Azure, você pode usar a filtragem avançada para acessar os dados desejados. É um filtro que você pode usar é *categoria de atividade*, que lista os tipos diferentes de logs de atividade no Azure AD. Para restringir os resultados para o que você está procurando, selecione uma categoria.

Por exemplo, se você estiver interessado apenas em atividades relacionadas à redefinição de senha de autoatendimento, escolha a categoria **Gerenciamento de Senhas de Autoatendimento**. As categorias que você vê têm base no recurso no qual você está trabalhando.  

![Opções de categoria na página Filtrar Logs de Auditoria](./media/active-directory-reporting-migration/06.png "Opções de categoria na página filtrar Logs de Auditoria")

As categorias de atividades incluem:

- Diretório principal
- Gerenciamento de senhas de auto-atendimento
- Gerenciamento de grupos de autoatendimento
- Provisionamento de conta de usuário

### Uso do aplicativo
<a id="application-usage" class="xliff"></a>

Para exibir detalhes sobre o uso do aplicativo para todos os aplicativos ou para um único aplicativo, em **ATIVIDADE**, selecione **Entradas**. Para restringir os resultados, filtre o nome de usuário ou nome do aplicativo.

![Página Filtrar Eventos de Entrada](./media/active-directory-reporting-migration/07.png "Página Filtrar Eventos de Entrada")

### Relatórios de segurança
<a id="security-reports" class="xliff"></a>

#### Relatórios de atividades anômalas do Azure AD
<a id="azure-ad-anomalous-activity-reports" class="xliff"></a>

Os relatórios de segurança de atividades anômalas do Azure AD do Portal Clássico do Azure foram consolidados para fornecer uma exibição única e central. Essa exibição mostra todos os eventos de risco relacionado à segurança que o Azure AD pode detectar e relatar.

A tabela a seguir lista os relatórios de segurança de atividades anômalas do Azure AD e os tipos de evento de risco correspondentes no Portal do Azure.

| Relatório de atividades anômalas do Azure AD |  Tipo de evento de risco do Identity Protection|
| :--- | :--- |
| Usuários com credenciais vazadas | Credenciais vazadas |
| Atividades de entrada irregulares | Viagem impossível a locais atípicos |
| Entradas de dispositivos possivelmente infectados | Entradas de dispositivos infectados|
| Entradas de fontes desconhecidas | Entradas de endereços IP anônimos |
| Entradas de endereços IP com atividade suspeita | Entradas de endereços IP com atividade suspeita |
| - | Entradas de locais desconhecidos |

Os seguintes relatórios de segurança de atividade anômala do Azure AD não são incluídos como eventos de risco no Portal do Azure:

* Entradas após várias falhas
* Entradas de várias geografias

Esses relatórios ainda estão disponíveis no Portal Clássico do Azure, mas serão desativados em algum momento no futuro.

Para saber mais, veja [Eventos de risco do Azure Active Directory](active-directory-identity-protection-risk-events.md).  


#### Eventos de risco detectados
<a id="detected-risk-events" class="xliff"></a>

No Portal do Azure, você pode acessar os relatórios sobre os eventos de risco detectados na folha **Azure Active Directory** em **SEGURANÇA**. Os eventos de risco detectados são controlados nos seguintes relatórios:   

- Usuários em risco
- Entradas de risco

![Relatórios de segurança](./media/active-directory-reporting-migration/04.png "Relatórios de segurança")

Para saber mais sobre relatórios de segurança, veja:

- [Relatório de segurança de usuários em risco no portal do Azure Active Directory](active-directory-reporting-security-user-at-risk.md)
- [Relatório de entradas de risco no portal do Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md)


## Relatórios de atividade no Portal Clássico do Azure versus Portal do Azure
<a id="activity-reports-in-the-azure-classic-portal-vs-the-azure-portal" class="xliff"></a>

A tabela nesta seção lista os relatórios existentes no Portal Clássico do Azure. Também descreve como você pode obter as mesmas informações no Portal do Azure.

Para ver todos os dados de auditoria, na folha **Azure Active Directory**, em **ATIVIDADE**, acesse **Logs de auditoria**.

![Logs de auditoria](./media/active-directory-reporting-migration/61.png "Logs de auditoria")

| portal clássico do Azure                 | Para encontrar no Portal do Azure                                                         |
| ---                                  | ---                                                                        |
| Logs de auditoria                           | Para **Categoria da Atividade**, selecione **Diretório Principal**.                       |
| Atividade de redefinição de senha              | Para **Categoria da Atividade**, selecione **Gerenciamento de Senhas de Autoatendimento**. |
| Atividade de registro de redefinição de senha | Para **Categoria da Atividade**, selecione **Gerenciamento de Senhas de Autoatendimento**.     |
| Atividade dos grupos de autoatendimento         | Para **Categoria da Atividade**, selecione **Gerenciamento de Grupos de Autoatendimento**.        |
| Atividade de provisionamento de conta        | Para **Categoria da Atividade**, selecione **Provisionamento do Usuário da Conta**.         |
| Status de substituição de senha             | Para **Categoria da Atividade**, selecione **Substituição Automática de Senha do Aplicativo**.      |
| Erros de provisionamento de conta          | Para **Categoria da Atividade**, selecione **Provisionamento do Usuário da Conta**.        |
| Alterações de nome do grupo do Office365         | Para **Categoria da Atividade**, selecione **Gerenciamento de Senhas de Autoatendimento**. Para **Tipo de Recurso de Atividade**, selecione **Grupo**. Para **Origem da Atividade**, selecione **Grupos do O365**.|

Para ver o relatório **Uso do Aplicativo**, na folha **Azure Active Directory**, em **GERENCIAR**, selecione **Aplicativos Empresariais** e selecione **Entradas**.


![Relatório de Entradas de aplicativos empresariais](./media/active-directory-reporting-migration/199.png "Relatório de Entradas de aplicativos empresariais")

