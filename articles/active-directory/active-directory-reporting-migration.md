---
title: "Encontrar relatórios de atividade de usuário do Azure Active Directory no portal do Azure | Microsoft Docs"
description: "Saiba onde os relatórios de atividade do usuário do Azure Active Directory estão no portal do Azure."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: dhanyahk
ms.openlocfilehash: 23c186e268e9a43982ec6c34d350900793fad8de
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Localizar relatórios de atividade no Portal do Azure

Neste artigo, descrevemos como localizar relatórios de atividade de usuário do Azure Active Directory no portal do Azure.

## <a name="whats-new"></a>Novidades

Os relatórios no Portal Clássico do Azure estavam separados em categorias:
* Relatórios de segurança
* Relatórios de atividades
* Relatórios de aplicativo integrados

### <a name="activity-and-integrated-app-reports"></a>Relatórios do aplicativo integrado e de atividade

Para relatórios baseados no contexto no Portal do Azure, os relatórios existentes são mesclados em uma única exibição. Uma única API subjacente fornece os dados para a exibição.

Para ver essa exibição, na folha **Azure Active Directory**, em **ATIVIDADE**, selecione **logs de auditoria**.

![Logs de auditoria](./media/active-directory-reporting-migration/482.png "Logs de auditoria")

Os relatórios a seguir são consolidados nessa exibição:

* Relatório de auditoria
* Atividade de redefinição de senha
* Atividade de registro de redefinição de senha
* Atividade dos grupos de autoatendimento
* Alterações de nome do grupo do Office365
* Atividade de provisionamento de conta
* Status de substituição de senha
* Erros de provisionamento de conta


O relatório Uso do Aplicativo foi aprimorado e está incluído na exibição **Entradas**. Para ver essa exibição, na folha **Azure Active Directory**, em **ATIVIDADE**, selecione **Entradas**.

![Exibição de entradas](./media/active-directory-reporting-migration/483.png "Exibição de entradas")

A exibição **Entradas** inclui todas as entradas de usuário. Use essas informações para obter informações de uso do aplicativo. Também é possível exibir informações de uso do aplicativo na visão geral **Aplicativos empresariais**, na seção **GERENCIAR**.

![Aplicativos empresariais](./media/active-directory-reporting-migration/484.png "Aplicativos empresariais")

## <a name="access-a-specific-report"></a>Acessar uma porta específica

Embora o Portal do Azure ofereça uma exibição única, também é possível examinar relatórios específicos.

### <a name="audit-logs"></a>Logs de auditoria

Em resposta aos comentários dos clientes, no Portal do Azure, você pode usar a filtragem avançada para acessar os dados desejados. É um filtro que você pode usar é *categoria de atividade*, que lista os tipos diferentes de logs de atividade no Azure AD. Para restringir os resultados para o que você está procurando, selecione uma categoria.

Por exemplo, se você estiver interessado apenas em atividades relacionadas à redefinição de senha de autoatendimento, escolha a categoria **Gerenciamento de Senhas de Autoatendimento**. As categorias que você vê têm base no recurso no qual você está trabalhando.  

![Opções de categoria na página Filtrar Logs de Auditoria](./media/active-directory-reporting-migration/06.png "Opções de categoria na página filtrar Logs de Auditoria")

As categorias de atividades incluem:

- Diretório principal
- Gerenciamento de senhas de auto-atendimento
- Gerenciamento de grupos de autoatendimento
- Provisionamento de conta de usuário

### <a name="application-usage"></a>Uso do aplicativo

Para exibir detalhes sobre o uso do aplicativo para todos os aplicativos ou para um único aplicativo, em **ATIVIDADE**, selecione **Entradas**. Para restringir os resultados, filtre o nome de usuário ou nome do aplicativo.

![Página Filtrar Eventos de Entrada](./media/active-directory-reporting-migration/07.png "Página Filtrar Eventos de Entrada")

### <a name="security-reports"></a>Relatórios de segurança

#### <a name="azure-ad-anomalous-activity-reports"></a>Relatórios de atividades anômalas do Azure AD

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


#### <a name="detected-risk-events"></a>Eventos de risco detectados

No Portal do Azure, você pode acessar os relatórios sobre os eventos de risco detectados na folha **Azure Active Directory** em **SEGURANÇA**. Os eventos de risco detectados são controlados nos seguintes relatórios:   

- Usuários em risco
- Entradas de risco

![Relatórios de segurança](./media/active-directory-reporting-migration/04.png "Relatórios de segurança")

Para saber mais sobre relatórios de segurança, veja:

- [Relatório de segurança de usuários em risco no portal do Azure Active Directory](active-directory-reporting-security-user-at-risk.md)
- [Relatório de entradas de risco no portal do Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md)


## <a name="activity-reports-in-the-azure-classic-portal-vs-the-azure-portal"></a>Relatórios de atividade no Portal Clássico do Azure versus Portal do Azure

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

## <a name="next-steps"></a>Próximas etapas

Para ter uma visão geral dos relatórios, consulte [Relatórios do Azure Active Directory](active-directory-reporting-azure-portal.md).
