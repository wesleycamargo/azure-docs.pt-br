---

title: "Introdução à API de relatório do Azure AD | Microsoft Docs"
description: "Como começar a usar a API de relatório do Azure Active Directory"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: dhanyahk;markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 414e6b0f23616f8f3d059f250335b0e530729741
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Introdução à API de relatório do Azure Active Directory
*Este tópico faz parte do [Guia de Relatórios do Azure Active Directory](active-directory-reporting-guide.md).*

O Azure Active Directory fornece vários relatórios. Os dados desses relatórios podem ser muito úteis para seus aplicativos, como sistemas SIEM, auditoria e ferramentas de business intelligence. As APIs de relatório do Azure AD fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação.

Este artigo fornece as informações necessárias para começar com as APIs de relatório do Azure AD.
Na próxima seção, você encontrará mais detalhes sobre como usar as APIs de auditoria e de entrada. Para todas as outras APIs, consulte o artigo [Eventos e relatórios do Azure AD (versão prévia)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview).

Para dúvidas, problemas ou comentários, entre em contato com a [Ajuda de relatório do AAD](mailto:aadreportinghelp@microsoft.com).

## <a name="learning-map"></a>Mapa de aprendizagem
1. **Prepare-se** – Antes de testar os exemplos de API, você precisa atender aos [pré-requisitos para acessar a API de relatório do Azure AD](active-directory-reporting-api-prerequisites.md).
2. **Explore** – Tenha uma primeira impressão das APIs de relatórios:
   
   * [Usar os exemplos para a API de auditoria](active-directory-reporting-api-audit-samples.md) 
   * [Usar os exemplos para a API de relatório de atividade de entrada](active-directory-reporting-api-sign-in-activity-samples.md)
3. **Personalizar** – Criar sua própria solução: 
   
   * [Usar a referência de API de auditoria](active-directory-reporting-api-audit-reference.md) 
   * [Usar a referência da API de relatório de atividade de entrada](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>Próximas etapas
Se você estiver curioso para ver todos os pontos de extremidade de API do Graph do Azure AD navegando até [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta).


