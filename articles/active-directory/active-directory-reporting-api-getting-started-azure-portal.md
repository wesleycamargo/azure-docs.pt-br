---
title: Introdução à API de relatórios do Microsoft Azure AD | Microsoft Docs
description: Como começar a usar a API de relatório do Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: a818c9b0e2c9097f45d2fdd39676ef6807d06ca5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929079"
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



