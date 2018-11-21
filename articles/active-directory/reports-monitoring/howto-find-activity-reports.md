---
title: Encontrar relatórios de atividade de usuário do Azure Active Directory no portal do Azure | Microsoft Docs
description: Saiba onde os relatórios de atividade do usuário do Azure Active Directory estão no portal do Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fab94088d1d54012a955b0663b078d03b13d6299
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624905"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Localizar relatórios de atividade no Portal do Azure

Neste artigo, você aprenderá a localizar relatórios de atividades de usuários do Azure AD (Azure Active Directory) no portal do Azure.

## <a name="audit-logs-report"></a>Relatório de logs de auditoria

O relatório de logs de auditoria combina vários relatórios sobre atividades de aplicativos em uma única exibição para relatórios baseados em contexto. Para acessar o relatório de logs de auditoria:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione o diretório no canto superior direito e, em seguida, selecione a folha **Azure Active Directory** do painel de navegação esquerdo.
3. Selecione **Logs de auditorias** da seção **Atividade** da folha do Azure Active Directory. 

    ![Logs de auditoria](./media/howto-find-activity-reports/482.png "Logs de auditoria")

O relatório de logs de auditoria consolida os seguintes relatórios:

* Relatório de auditoria
* Atividade de redefinição de senha
* Atividade de registro de redefinição de senha
* Atividade dos grupos de autoatendimento
* Alterações de nome do grupo do Office365
* Atividade de provisionamento de conta
* Status de substituição de senha
* Erros de provisionamento de conta

### <a name="filtering-on-audit-logs"></a>Filtragem em logs de auditoria

É possível usar a filtragem avançada no relatório de auditoria para acessar uma categoria específica de dados de auditoria, especificando-a no filtro **Categoria de atividade**. Por exemplo, para exibir todas as atividades relacionadas à redefinição de senha de autoatendimento, selecione a categoria **Gerenciamento de senha de autoatendimento**. 

    ![Category options on the Filter Audit Logs page](./media/howto-find-activity-reports/06.png "Category options on the Filter Audit Logs page")

As categorias de atividades incluem:

- Diretório principal
- Gerenciamento de senhas de auto-atendimento
- Gerenciamento de grupos de autoatendimento
- Provisionamento de conta de usuário


## <a name="sign-ins-report"></a>Relatório de entradas 

A exibição **Entradas** inclui todas as entradas do usuário, bem como o relatório **Uso do Aplicativo**. Também é possível exibir as informações de uso do aplicativo na seção **Gerenciar** da visão geral dos **Aplicativos empresariais**.

    ![Enterprise applications](./media/howto-find-activity-reports/484.png "Enterprise applications")

Para acessar o relatório de entradas:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione o diretório no canto superior direito e, em seguida, selecione a folha **Azure Active Directory** do painel de navegação esquerdo.
3. Selecione **Entradas** da seção **Atividade** da folha Azure Active Directory. 

    ![Exibição de entradas](./media/howto-find-activity-reports/483.png "Exibição de entradas")


### <a name="filtering-on-application-name"></a>Filtragem no nome do aplicativo

É possível usar o relatório de entradas para exibir detalhes sobre o uso do aplicativo, filtrando o nome de usuário ou o nome do aplicativo.

![Página Filtrar Eventos de Entrada](./media/howto-find-activity-reports/07.png "Página Filtrar Eventos de Entrada")

## <a name="security-reports"></a>Relatórios de segurança

### <a name="anomalous-activity-reports"></a>Relatórios de atividades anômalas

Relatórios de atividade anômalas fornecem informações sobre eventos de risco relacionados à segurança que o Azure AD pode detectar e relatar.

A tabela a seguir lista os relatórios de segurança de atividades anômalas do Azure AD e os tipos de evento de risco correspondentes no Portal do Azure. Para saber mais, veja [Eventos de risco do Azure Active Directory](concept-risk-events.md).  


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


### <a name="detected-risk-events"></a>Eventos de risco detectados

Você pode acessar relatórios sobre eventos de risco detectados na seção **Segurança** da folha **Azure Active Directory** no [portal do Azure](https://portal.azure.com). Os eventos de risco detectados são controlados nos seguintes relatórios:   

- [Usuários em risco](concept-user-at-risk.md)
- [Entradas de risco](concept-risky-sign-ins.md)

    ![Relatórios de segurança](./media/howto-find-activity-reports/04.png "Relatórios de segurança")

## <a name="next-steps"></a>Próximas etapas

* [Visão geral de logs de auditoria](concept-audit-logs.md)
* [Visão geral de entradas](concept-sign-ins.md)
* [Visão geral de eventos de risco](concept-risk-events.md)
