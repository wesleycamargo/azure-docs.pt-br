---
title: "Políticas de retenção de relatório do Azure Active Directory | Microsoft Docs"
description: "Políticas de retenção sobre dados de relatório no Active Directory do Azure"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 47594d1d435ee92d57150a01a99765bab0e87846
ms.openlocfilehash: 2ac62c2c04975167b243b49bf2c536900e77df73
ms.lasthandoff: 02/03/2017


---
# <a name="azure-active-directory-report-retention-policies"></a>Políticas de retenção de relatório do Azure Active Directory
*Esta documentação é parte do [Guia de Relatórios do Azure Active Directory](active-directory-reporting-guide.md).*


Este tópico fornece respostas para as perguntas mais comuns em conjunto com a retenção de dados para os relatórios de atividade diferente no Azure Active Directory. 

Como você pode iniciar a coleta de dados de atividade?

| Edição do Azure AD | Início da Coleta |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Quando você se inscreve para uma assinatura |
| AD do Azure Gratuito | Na primeira vez que você abrir a [folha do Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou usar as [APIs de relatório](https://aka.ms/aadreports)  |


Quando os dados de atividade estarão disponíveis no portal do Azure?

- **Imediatamente** - se você já estiver trabalhando com relatórios no portal clássico do Azure
- **Em até 2 horas** - se você ainda não tiver ativado os relatórios no portal clássico do Azure

Como você pode iniciar a coleta de sinais de segurança?  
Para sinais de segurança, o processo de coleta é iniciado quando você aceita usar o Centro de Proteção de Identidade. 

Por quanto tempo os dados coletados são armazenados?

**Relatórios de atividades**    

| Relatório | AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--    | :--           | :--                | :--                |
| Auditoria de Diretório | 7 dias | 30 dias | 30 dias |
| Atividade de Entrada |    7 dias | 30 dias | 30 dias |

**Sinais de Segurança**

| Relatório | AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--    | :--           | :--                | :--                |
| Entradas de risco | 7 dias | 30 dias | 90 dias |



