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
ms.date: 07/05/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: aa7a69c933abfda3bf4d1ac1a298c4ba684efd7e
ms.contentlocale: pt-br
ms.lasthandoff: 04/06/2017

---
# Políticas de retenção de relatório do Azure Active Directory
<a id="azure-active-directory-report-retention-policies" class="xliff"></a>


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
| Atividade de Entrada       | 7 dias        | 30 dias             | 30 dias             |

**Sinais de Segurança**

| Relatório         | AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Usuários em risco  | 7 dias        | 30 dias             | 90 dias             |
| Entradas de risco | 7 dias        | 30 dias             | 90 dias             |

---
