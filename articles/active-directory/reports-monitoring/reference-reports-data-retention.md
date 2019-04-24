---
title: Políticas de retenção de relatório do Azure Active Directory | Microsoft Docs
description: Políticas de retenção sobre dados de relatório no Active Directory do Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1423fddc13a6912e90b6a239921cf2a4d2662ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437049"
---
# <a name="azure-active-directory-report-retention-policies"></a>Políticas de retenção de relatório do Azure Active Directory

Neste artigo, você saberá mais sobre as políticas de retenção de dados dos diferentes relatórios de atividade no Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Quando o Azure AD inicia a coleção de dados?

| Edição do Azure AD | Início da Coleta |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Quando você se inscrever para uma assinatura |
| AD do Azure Gratuito <br /> AD Basic do Azure | Na primeira vez que você abrir a [folha do Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou usar as [APIs de relatório](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Quando os dados da atividade estarão disponíveis no portal do Azure?

- **Imediatamente** - se você já estiver trabalhando com relatórios no portal do Azure.
- **Dentro de 2 horas**: se você não tiver ativado os relatórios no portal do Azure.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Em quanto tempo devo ver os dados das atividades após obter uma licença Premium?

Se você já tiver dados de atividades com sua licença gratuita, poderá vê-los imediatamente ao atualizar. Se você não tiver nenhum dado, serão necessários um ou dois dias para os dados serem exibidos nos relatórios depois de atualizar para uma licença premium.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Eu poderei visualizar os dados do último mês, após obter uma licença Premium do Azure AD?

Se você mudou recentemente para uma versão Premium (incluindo uma versão de avaliação), pode inicialmente ver dados de até sete dias. Quando os dados forem acumulados, será possível visualizar os dados dos últimos 30 dias.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Quando o Azure AD inicia a coleção de dados do sinal de segurança?  

Para sinais de segurança, o processo de coleção é iniciado quando você aceita usar a **Central do Identity Protection**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Por quanto tempo o Azure AD armazena os dados?

**Relatórios de atividades**    

| Relatório                 | AD do Azure Gratuito | AD Basic do Azure | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| Logs de auditoria             | 7 dias        |  7 dias        | 30 dias             | 30 dias             |
| Entradas               | N/D           |  N/D           | 30 dias             | 30 dias             |
| Uso MFA do Azure        | 30 dias       |  30 dias       | 30 dias             | 30 dias             |

É possível manter os dados da atividade de entrada e de auditoria por mais tempo que o período de retenção padrão descrito acima, roteando-os para uma conta de armazenamento do Azure usando o Azure Monitor. Para obter mais informações, consulte [Arquivar logs do Azure AD em uma conta de armazenamento do Azure](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Sinais de Segurança**

| Relatório         | AD do Azure Gratuito | AD Basic do Azure | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| Usuários em risco  | 7 dias        | 7 dias         | 30 dias             | 90 dias             |
| Entradas de risco | 7 dias        | 7 dias         |  30 dias            | 90 dias             |

---
