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
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 93532f4b0b2d527a4d5c79e2ee1b2810394b2f11
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442076"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Introdução à API de relatórios do Microsoft Azure Active Directory

O Microsoft Azure Active Directory fornece vários [relatórios](active-directory-reporting-azure-portal.md). Os dados desses relatórios podem ser muito úteis para seus aplicativos, como sistemas SIEM, auditoria e ferramentas de business intelligence. 

Ao usar a API de relatórios do Microsoft Azure Active Directory, você pode ganhar acesso programático aos dados através de um conjunto de APIs com base em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação.

Este artigo fornece um roteiro para acessar os dados de relatório usando a API relacionada.

Se houver problemas, consulte [Como obter suporte para o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>pré-requisitos

Para acessar a API de relatório, mesmo se estiver planejando acessar a API usando um script, será necessário:

1. Atribuir funções (Leitor de Segurança, Admin. de Segurança, Admin. Global)
2. Registrar um aplicativo
3. Conceder permissões
4. Reunir definições de configuração


 
Para obter instruções detalhadas, consulte, [pré-requisitos para acessar a API de relatório do Microsoft Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md).

## <a name="apis-with-graph-explorer"></a>APIs com Microsoft Azure Graph

Você pode usar o [MSGraph explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) para verificar sua entrada e dados da API de auditoria. Certifique-se de entrar em sua conta usando ambos os botões de entrar na interface do usuário do Graph Explorer e defina as permissões **Tasks. ReadWrite** e **Directory.ReadAll** para seu locatário, conforme mostrado.   

![Explorador do Graph](./media/active-directory-reporting-api-getting-started-azure-portal/graph-explorer.png)

![Modificar a interface do usuário de permissões](./media/active-directory-reporting-api-getting-started-azure-portal/modify-permissions.png)

## <a name="recommendation"></a>Recomendações 

Se você estiver planejando recuperar os dados de relatório sem a intervenção do usuário, considere usar a API de Relatórios do Microsoft Azure Active Directory com os certificados.

Para obter instruções detalhadas, consulte [obter dados usando a API de Relatórios do Microsoft Azure Active Directory com os certificados](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Explorar

Tenha uma primeira impressão das APIs de relatórios
   
   - [Usar os exemplos para a API de auditoria](active-directory-reporting-api-audit-samples.md) 
 
   - [Usar os exemplos para a API de relatório de atividade de entrada](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>Personalizar  

Crie sua própria solução 
   
   - [Usar a referência de API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [Usar a referência da API de relatório de atividade de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



