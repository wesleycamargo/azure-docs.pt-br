---
title: "Introdução à API de relatório do Azure AD | Microsoft Docs"
description: "Como começar a usar a API de relatório do Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9c858b8f2d5a4a348bc0b4443ddbe0000a5b62f4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Introdução à API de relatório do Azure Active Directory

O Azure Active Directory fornece vários relatórios. Os dados desses relatórios podem ser muito úteis para seus aplicativos, como sistemas SIEM, auditoria e ferramentas de business intelligence. As APIs de relatório do Azure AD fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação.

Este artigo fornece as informações necessárias para começar com as APIs de relatório do Azure AD.
Na próxima seção, você encontrará mais detalhes sobre como usar as APIs de auditoria e de entrada. 

Para ver perguntas frequentes, leia nossas [Perguntas Frequentes](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-faq). Para solucionar problemas, [registre um tíquete de suporte](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

## <a name="learning-map"></a>Mapa de aprendizagem
1. **Prepare-se** – Antes de testar os exemplos de API, você precisa atender aos [pré-requisitos para acessar a API de relatório do Azure AD](active-directory-reporting-api-prerequisites-azure-portal.md).
2. **Explore** – Tenha uma primeira impressão das APIs de relatórios:
   
   * [Usar os exemplos para a API de auditoria](active-directory-reporting-api-audit-samples.md) 
   * [Usar os exemplos para a API de relatório de atividade de entrada](active-directory-reporting-api-sign-in-activity-samples.md)
3. **Personalizar** – Criar sua própria solução: 
   
   * [Usar a referência de API de auditoria](active-directory-reporting-api-audit-reference.md) 
   * [Usar a referência da API de relatório de atividade de entrada](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>Próximas etapas
Se estiver curioso para ver todos os pontos de extremidade disponíveis da API do Graph do Azure AD, use este link [https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta](https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta).

