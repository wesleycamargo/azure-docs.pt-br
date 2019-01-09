---
title: Políticas de retenção de relatório do Azure Active Directory | Microsoft Docs
description: Políticas de retenção sobre dados de relatório no Active Directory do Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 6188b141ec1a514d999f290366cd9ffbafc1d96c
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753125"
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

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Quando o Azure AD inicia a coleção de dados do sinal de segurança?  

Para sinais de segurança, o processo de coleção é iniciado quando você aceita usar a **Central do Identity Protection**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Por quanto tempo o Azure AD armazena os dados?

**Relatórios de atividades**    

| Relatório                 | AD do Azure Gratuito | AD Basic do Azure | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| Auditoria de Diretório        | 7 dias        |  7 dias        | 30 dias             | 30 dias             |
| Atividade de Entrada       | N/D           |  N/D           | 30 dias             | 30 dias             |
| Uso do MFA do Azure        | 30 dias       |  30 dias       | 30 dias             | 30 dias             |

É possível manter os dados da atividade de entrada e de auditoria por mais tempo que o período de retenção padrão descrito acima, roteando-os para uma conta de armazenamento do Azure usando o Azure Monitor. Para obter mais informações, consulte [Arquivar logs do Azure AD em uma conta de armazenamento do Azure](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Sinais de Segurança**

| Relatório         | AD do Azure Gratuito | AD Basic do Azure | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| Usuários em risco  | 7 dias        | 7 dias         | 30 dias             | 90 dias             |
| Entradas de risco | 7 dias        | 7 dias         |  30 dias            | 90 dias             |

---
