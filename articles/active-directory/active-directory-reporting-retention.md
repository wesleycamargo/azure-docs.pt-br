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
ms.sourcegitcommit: c4c8242a5bd85159c139b1ee0fd2e51b984cf98c
ms.openlocfilehash: baf4ea9e53a45fbbe486d2597987ffcf9493612a


---
# <a name="azure-active-directory-report-retention-policies"></a>Políticas de retenção de relatório do Azure Active Directory
*Esta documentação é parte do [Guia de Relatórios do Azure Active Directory](active-directory-reporting-guide.md).*



Este artigo fornece uma visão geral de retenção de dados para diferentes relatórios de Atividade presentes no Azure Active Directory. 

Para relatórios de Atividade, para os clientes Premium e Premium 2, começamos coletar os dados de atividade assim que você se inscreve em uma licença Premium. Para clientes gratuitos, podemos começar a coletar os dados de atividades assim que você fizer logon no portal ou usar nossas APIs de relatórios pela primeira vez. 

Se você já estiver vendo relatórios no Portal Clássico do Azure, você verá os dados imediatamente no Portal do Azure (novo). Se você ainda não ativou os relatórios no portal antigo fazendo logon no Azure Active Directory no Portal do Azure e deseja ver esses relatórios de atividade, pode levar até duas horas para que você possa ver os dados. Vamos começar a coletar os dados assim que você fizer logon na folha do Azure Active Directory.

Para sinais de Segurança, podemos começar a coletar os dados assim que você optar por usar o Centro de Proteção de Identidade. 



**Relatórios de atividades**    

| Relatório | AD do Azure Gratuito | Azure AD Premium 1 | Azure AD Premium 2 |
| :--    | :--           | :--                | :--                |
| Auditoria de Diretório | 7 dias | 30 dias | 30 dias |
| Atividade de Entrada |    7 dias | 30 dias | 30 dias |

**Sinais de Segurança**

| Relatório | AD do Azure Gratuito | Azure AD Premium 1 | Azure AD Premium 2 |
| :--    | :--           | :--                | :--                |
| Entradas de risco | 7 dias | 30 dias | 90 dias |





<!--HONumber=Dec16_HO1-->


