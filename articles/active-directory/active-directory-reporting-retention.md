---
title: "Políticas de retenção de relatório do Azure Active Directory | Microsoft Docs"
description: "Políticas de retenção sobre dados de relatório no Active Directory do Azure"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 933d251903f4ca74902e984e7a1581a75345ee7f
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2017
---
# <a name="azure-active-directory-report-retention-policies"></a>Políticas de retenção de relatório do Azure Active Directory


Este tópico fornece respostas para as perguntas mais comuns em conjunto com a retenção de dados para os relatórios de atividade diferente no Azure Active Directory. 

**P: Como você pode iniciar a coleta de dados de atividade?**

**R:**

| Edição do Azure AD | Início da Coleta |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Quando você se inscreve para uma assinatura |
| AD do Azure Gratuito | Na primeira vez que você abrir a [folha do Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou usar as [APIs de relatório](https://aka.ms/aadreports)  |

---
**P: Quando os dados de atividade estarão disponíveis no Portal do Azure?**

**R:**

- **Imediatamente** - se você já estiver trabalhando com relatórios no portal clássico do Azure
- **Em até 2 horas** - se você ainda não tiver ativado os relatórios no portal clássico do Azure

---
**P: Como você pode iniciar a coleta de sinais de segurança?**  

**R:** Para sinais de segurança, o processo de coleta é iniciado quando você aceita usar o Centro de Proteção de Identidade. 


---
**P: Por quanto tempo os dados coletados são armazenados?**

**R:**

**Relatórios de atividades**    

| Relatório                 | AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Auditoria de Diretório        | 7 dias        | 30 dias             | 30 dias             |
| Atividade de Entrada       | N/D           | 30 dias             | 30 dias             |
| Uso do MFA do Azure        | 90 dias       | 90 dias             | 90 dias             |

**Sinais de Segurança**

| Relatório         | AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Usuários em risco  | 7 dias        | 30 dias             | 90 dias             |
| Entradas de risco | 7 dias        | 30 dias             | 90 dias             |

---
