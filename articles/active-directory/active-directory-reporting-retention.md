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
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 8a7d1bd1db8abdac5b741d79b10c9b363afecd24
ms.openlocfilehash: 9f8d9c845c391ec24aa3b265aa3078840fe3240a


---
# <a name="azure-active-directory-report-retention-policies"></a>Políticas de retenção de relatório do Azure Active Directory
*Esta documentação é parte do [Guia de Relatórios do Azure Active Directory](active-directory-reporting-guide.md).*


Este tópico fornece respostas para as perguntas mais comuns em conjunto com a retenção de dados para os relatórios de atividade diferente no Azure Active Directory. 

Como você pode iniciar a coleta de dados de atividade?

| Edição do Azure AD | Início da Coleta |
| :--              | :--   |
|Premium e Premium 2 | Quando você se inscreve em uma licença |
| Grátis | Na primeira vez que você abrir a [folha do Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou usar as [APIs de relatório](https://aka.ms/aadreports)  |


Quando os dados de atividade estarão disponíveis no portal do Azure?

- **Imediatamente** - se você já estiver trabalhando com relatórios no portal clássico do Azure
- **Em até 2 horas** - se você ainda não tiver ativado os relatórios no portal clássico do Azure

Como você pode iniciar a coleta de sinais de segurança?  
Para sinais de segurança, o processo de coleta é iniciado quando você aceita usar o Centro de Proteção de Identidade. 

Por quanto tempo os dados coletados são armazenados?

**Relatórios de atividades**    

| Relatório | AD do Azure Gratuito | Azure AD Premium 1 | Azure AD Premium 2 |
| :--    | :--           | :--                | :--                |
| Auditoria de Diretório | 7 dias | 30 dias | 30 dias |
| Atividade de Entrada |    7 dias | 30 dias | 30 dias |

**Sinais de Segurança**

| Relatório | AD do Azure Gratuito | Azure AD Premium 1 | Azure AD Premium 2 |
| :--    | :--           | :--                | :--                |
| Entradas de risco | 7 dias | 30 dias | 90 dias |





<!--HONumber=Dec16_HO5-->


