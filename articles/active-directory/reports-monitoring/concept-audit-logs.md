---
title: Auditoria de relatórios de atividade no portal do Azure Active Directory | Microsoft Docs
description: Introdução à auditoria de relatórios de atividade no portal do Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f221b815b6800f635c07525fdbd332ac508786
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171529"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividades de auditoria no portal do Azure Active Directory 

Com os relatórios do Azure AD (Azure Active Directory), é possível obter as informações necessárias para determinar o desempenho do ambiente.

A arquitetura de relatórios consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – O [relatório de entradas](concept-sign-ins.md) fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - **Logs de auditoria** – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.
- **Segurança** 
    - **Entradas arriscadas** - Uma [entrada arriscada](concept-risky-sign-ins.md) é um indicador para uma tentativa de entrada que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. 
    - **Usuários sinalizados para risco** - Um [usuário arriscado](concept-user-at-risk.md) é um indicador de uma conta de usuário que pode ter sido comprometida.

Este artigo fornece uma visão geral do relatório de auditoria.
 
## <a name="who-can-access-the-data"></a>Quem pode acessar os dados?

* Usuários nas funções **Administrador de Segurança**, **Leitor de Segurança** ou **Administrador Global**
* Além disso, todos os usuários (não administradores) podem ver suas próprias atividades de auditoria

## <a name="audit-logs"></a>Logs de auditoria

Os logs de auditoria do Azure AD fornecem registros das atividades do sistema para fins de conformidade. Para acessar o relatório de auditoria, selecione **Logs de auditoria** na seção **Atividade** do **Azure Active Directory**. Observe que os logs de auditoria podem ter uma latência de, no máximo, uma hora. Por isso, os dados da atividade de auditoria podem demorar esse período para aparecer no portal após a conclusão da tarefa.

![Logs de auditoria](./media/concept-audit-logs/61.png "Logs de auditoria")

Um log de auditoria tem um modo de exibição de lista padrão que mostra:

- a data e a hora da ocorrência
- o iniciador/ator (*quem*) de uma atividade 
- a atividade (*o quê*) 
- o destino

![Logs de auditoria](./media/concept-audit-logs/18.png "Logs de auditoria")

Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.

![Logs de auditoria](./media/concept-audit-logs/19.png "Logs de auditoria")

Isso permite a você exibir campos adicionais ou remover campos que já estão exibidos.

![Logs de auditoria](./media/concept-audit-logs/21.png "Logs de auditoria")

Selecione um item na exibição de lista para obter informações mais detalhadas.

![Logs de auditoria](./media/concept-audit-logs/22.png "Logs de auditoria")


## <a name="filtering-audit-logs"></a>Filtragem de logs de auditoria

É possível filtrar os dados de auditoria nos seguintes campos:

- Intervalo de datas
- Iniciado por (ator)
- Categoria
- Tipo de recurso de atividade
- Atividade

![Logs de auditoria](./media/concept-audit-logs/23.png "Logs de auditoria")

O filtro **intervalo de datas** permite definir um período de tempo para os dados retornados.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 24 horas
- Personalizado

Quando você seleciona um período de tempo personalizado, pode configurar uma hora de início e uma hora de término.

O filtro **iniciado por** permite que você defina o nome do ator ou o nome UPN.

O filtro **categoria** permite que você selecione um dos seguintes filtros:

- Todos
- Categoria principal
- Diretório principal
- Gerenciamento de senhas de autoatendimento
- Gerenciamento de grupo de autoatendimento
- Provisionamento de conta - Substituição de senha automática
- Usuários convidados
- Serviço MIM
- Identity Protection
- B2C

O filtro **tipo de recurso de atividade** permite que você selecione um dos seguintes filtros:

- Todos 
- Agrupar
- Diretório
- Usuário
- Aplicativo
- Política
- Dispositivo
- Outros

Ao selecionar **Grupo** como **tipo de recurso de atividade**, você obtém uma categoria de filtro adicional que permite que você forneça também uma **Origem**:

- AD do Azure
- O365


O filtro **atividade** é baseado na seleção de tipo de recurso de categoria e atividade que você faz. Você pode selecionar uma atividade específica que deseja exibir ou escolher todas. 

Você pode obter a lista de todas as Atividades de Auditoria usando a API do Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, onde $tenantdomain = seu nome de domínio ou consulte o artigo [eventos do relatório de auditoria](reference-audit-activities.md).

## <a name="audit-logs-shortcuts"></a>Atalhos de logs de auditoria

Além do **Azure Active Directory**, o portal do Azure fornece dois pontos de entrada adicionais para dados de auditoria:

- Usuários e grupos
- Aplicativos empresariais

### <a name="users-and-groups-audit-logs"></a>Logs de auditoria de usuários e grupos

Com relatórios de auditoria baseados em grupos e usuários, você pode obter respostas a perguntas como:

- Que tipos de atualizações foram aplicadas os usuários?

- Quantos usuários foram alterados?

- Quantas senhas foram alteradas?

- O que um administrador fez em um diretório?

- Quais são os grupos que foram adicionados?

- Existem grupos com alterações de associação?

- Os proprietários do grupo foram alterados?

- Quais licenças foram atribuídas a um grupo ou a um usuário?

Se quiser examinar os dados de auditoria relacionados aos usuários e aos grupos, você poderá encontrar uma exibição filtrada em **Logs de auditoria** na seção **Atividade** de **Usuários e Grupos**. Esse ponto de entrada tem **Usuários e grupos** como **Tipo de Recurso de Atividade** pré-selecionado.

![Logs de auditoria](./media/concept-audit-logs/93.png "Logs de auditoria")

### <a name="enterprise-applications-audit-logs"></a>Logs de auditoria de aplicativos empresariais

Com relatórios de auditoria baseados em aplicativos, você pode obter respostas a perguntas como:

* Quais aplicativos foram adicionados ou atualizados?
* Quais aplicativos foram removidos?
* Uma entidade de serviço para um aplicativo foi alterada?
* Os nomes de aplicativos foram alterados?
* Quem deu permissão a um aplicativo?

Se você quiser revisar os dados de auditoria relacionados aos aplicativos, poderá encontrar uma exibição filtrada em **Logs de auditorias** na seção **Atividade** da folha **Aplicativos empresariais**. Este ponto de entrada tem **Aplicativos empresariais** pré-selecionados como o **Tipo de Recurso da Atividade**.

![Logs de auditoria](./media/concept-audit-logs/134.png "Logs de auditoria")

É possível filtrar essa exibição para **grupos** ou **usuários**.

![Logs de auditoria](./media/concept-audit-logs/25.png "Logs de auditoria")

## <a name="office-365-activity-logs"></a>Logs de atividade do Office 365

É possível exibir logs de atividades do Office 365 no [Centro de Administração do Office 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Embora os logs de atividades do Office 365 e do Azure AD compartilhem muitos dos recursos de diretório, somente o Centro de Administração do Office 365 oferece uma exibição completa dos logs de atividades do Office 365. 

Você também pode acessar os logs de atividade do Office 365 de modo programático usando as [APIs de gerenciamento do Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Próximas etapas

- [Referência das atividades de auditoria do Azure AD](reference-audit-activities.md)
- [Referência de retenção de relatórios do Azure AD](reference-reports-data-retention.md)
- [Referência de latências de log do Azure AD](reference-reports-latencies.md)
