---
title: Introdução à API de relatórios do Microsoft Azure AD | Microsoft Docs
description: Como começar a usar a API de relatório do Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7de7c87e5cf1747f4899f33d9e6b9cbf0bb430e1
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42145055"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Introdução à API de relatórios do Microsoft Azure Active Directory

O Microsoft Azure Active Directory fornece vários [relatórios](overview-reports.md). Os dados desses relatórios podem ser muito úteis para seus aplicativos, como sistemas SIEM, auditoria e ferramentas de business intelligence. 

Ao usar a API de relatórios do Microsoft Azure Active Directory, você pode ganhar acesso programático aos dados através de um conjunto de APIs com base em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação.

Este artigo fornece um roteiro para acessar os dados de relatório usando a API relacionada.

Se houver problemas, consulte [Como obter suporte para o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>Pré-requisitos

Para acessar a API de relatório, mesmo se estiver planejando acessar a API usando um script, será necessário:

1. Atribuir funções (Leitor de Segurança, Admin. de Segurança, Admin. Global)
2. Registrar um aplicativo
3. Conceder permissões
4. Reunir definições de configuração

Para obter instruções detalhadas, consulte, [pré-requisitos para acessar a API de relatório do Microsoft Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md).

## <a name="apis-with-graph-explorer"></a>APIs com Microsoft Azure Graph

Você pode usar o [MSGraph explorer](https://developer.microsoft.com/graph/graph-explorer) para verificar sua entrada e dados da API de auditoria. Certifique-se de entrar na conta usando ambos os botões entrar na interface do usuário do Explorador do Graph e configure as permissões **AuditLog.Read.All** e **Directory.Read.All** do locatário, conforme mostrado.   

![Explorador do Graph](./media/concept-reporting-api/graph-explorer.png)

![Modificar a interface do usuário de permissões](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Use certificados para acessar a API de relatórios do AD do Azure 

Use a API de relatórios do Azure AD com certificados se você planeja recuperar dados de relatórios sem a intervenção do usuário.

Para obter instruções detalhadas, consulte [Obter dados usando a API de relatórios do Azure AD com certificados](tutorial-access-api-with-certificates.md).


## <a name="next-steps"></a>Próximas etapas

 * [Referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
 * [Referência da API de relatório de atividade de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
 * [Solucionar problemas de erros no relatório de API do Azure AD](troubleshoot-graph-api.md)


