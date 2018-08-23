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
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 182537d6f07b624f2395f591681ed4596579bde0
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2018
ms.locfileid: "42145650"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Localizar relatórios de atividade no Portal do Azure

Neste artigo, descrevemos como localizar relatórios de atividade de usuário do Azure Active Directory no portal do Azure.

## <a name="activity-and-integrated-app-reports"></a>Relatórios do aplicativo integrado e de atividade

Para relatórios baseados no contexto no Portal do Azure, os relatórios existentes são mesclados em uma única exibição. Uma única API subjacente fornece os dados para a exibição.

Para ver essa exibição, na folha **Azure Active Directory**, em **ATIVIDADE**, selecione **logs de auditoria**.

![Logs de auditoria](./media/howto-find-activity-reports/482.png "Logs de auditoria")

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

![Exibição de entradas](./media/howto-find-activity-reports/483.png "Exibição de entradas")

A exibição **Entradas** inclui todas as entradas de usuário. Use essas informações para obter informações de uso do aplicativo. Também é possível exibir informações de uso do aplicativo na visão geral **Aplicativos empresariais**, na seção **GERENCIAR**.

![Aplicativos empresariais](./media/howto-find-activity-reports/484.png "Aplicativos empresariais")

## <a name="access-a-specific-report"></a>Acessar uma porta específica

Embora o Portal do Azure ofereça uma exibição única, também é possível examinar relatórios específicos.

### <a name="audit-logs"></a>Logs de auditoria

Em resposta aos comentários dos clientes, no Portal do Azure, você pode usar a filtragem avançada para acessar os dados desejados. É um filtro que você pode usar é *categoria de atividade*, que lista os tipos diferentes de logs de atividade no Azure AD. Para restringir os resultados para o que você está procurando, selecione uma categoria.

Por exemplo, se você estiver interessado apenas em atividades relacionadas à redefinição de senha de autoatendimento, escolha a categoria **Gerenciamento de Senhas de Autoatendimento**. As categorias que você vê têm base no recurso no qual você está trabalhando.  

![Opções de categoria na página Filtrar Logs de Auditoria](./media/howto-find-activity-reports/06.png "Opções de categoria na página filtrar Logs de Auditoria")

As categorias de atividades incluem:

- Diretório principal
- Gerenciamento de senhas de auto-atendimento
- Gerenciamento de grupos de autoatendimento
- Provisionamento de conta de usuário

### <a name="application-usage"></a>Uso do aplicativo

Para exibir detalhes sobre o uso do aplicativo para todos os aplicativos ou para um único aplicativo, em **ATIVIDADE**, selecione **Entradas**. Para restringir os resultados, filtre o nome de usuário ou nome do aplicativo.

![Página Filtrar Eventos de Entrada](./media/howto-find-activity-reports/07.png "Página Filtrar Eventos de Entrada")

### <a name="security-reports"></a>Relatórios de segurança

#### <a name="azure-ad-anomalous-activity-reports"></a>Relatórios de atividades anômalas do Azure AD

Os relatórios de segurança de atividade anômala do Azure AD são consolidados para fornecer uma visão central. Essa exibição mostra todos os eventos de risco relacionado à segurança que o Azure AD pode detectar e relatar.

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

Para saber mais, veja [Eventos de risco do Azure Active Directory](concept-risk-events.md).  


#### <a name="detected-risk-events"></a>Eventos de risco detectados

No Portal do Azure, você pode acessar os relatórios sobre os eventos de risco detectados na folha **Azure Active Directory** em **SEGURANÇA**. Os eventos de risco detectados são controlados nos seguintes relatórios:   

- Usuários em risco
- Entradas de risco

![Relatórios de segurança](./media/howto-find-activity-reports/04.png "Relatórios de segurança")

Para saber mais sobre relatórios de segurança, veja:

- [Relatório de segurança de usuários em risco no portal do Azure Active Directory](concept-user-at-risk.md)
- [Relatório de entradas de risco no portal do Azure Active Directory](concept-risky-sign-ins.md)


Para ver o relatório **Uso do Aplicativo**, na folha **Azure Active Directory**, em **GERENCIAR**, selecione **Aplicativos Empresariais** e selecione **Entradas**.


![Relatório de Inscrições de Aplicativos Corporativos](./media/howto-find-activity-reports/199.png)

## <a name="next-steps"></a>Próximas etapas

Para ter uma visão geral dos relatórios, consulte [Relatórios do Azure Active Directory](overview-reports.md).
