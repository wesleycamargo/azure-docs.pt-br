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
ms.component: compliance-reports
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 8edce7d1a633148cf361deec0c3d09ef4c16ac9b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225171"
---
# <a name="azure-active-directory-report-retention-policies"></a>Políticas de retenção de relatório do Azure Active Directory


Este artigo fornece respostas para as perguntas mais comuns em conjunto com a retenção de dados para os diferentes relatórios de atividade no Active Directory do Azure. 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>P: Como você pode iniciar a coleta de dados de atividade?

**R:**

| Edição do Azure AD | Início da Coleta |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Quando você se inscrever para uma assinatura |
| AD do Azure Gratuito | Na primeira vez que você abrir a [folha do Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou usar as [APIs de relatório](https://aka.ms/aadreports)  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>P: Quando os dados de atividade estarão disponíveis no Portal do Azure?

**R:**

- **Imediatamente** - se você já estiver trabalhando com relatórios no portal do Azure.
- **Dentro de 2 horas**: se você não tiver ativado os relatórios no portal do Azure.

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>P: Como você pode iniciar a coleta de sinais de segurança?  

**R:** Para sinais de segurança, o processo de coleta é iniciado quando você aceita usar o Centro de Proteção de Identidade. 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>P: Por quanto tempo os dados coletados são armazenados?

**R:**

**Relatórios de atividades**    

| Relatório                 | AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Auditoria de Diretório        | 7 dias        | 30 dias             | 30 dias             |
| Atividade de Entrada       | N/D           | 30 dias             | 30 dias             |
| Uso do MFA do Azure        | 30 dias       | 30 dias             | 30 dias             |

**Sinais de Segurança**

| Relatório         | AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Usuários em risco  | 7 dias        | 30 dias             | 90 dias             |
| Entradas de risco | 7 dias        | 30 dias             | 90 dias             |

---
