---
title: Lista de compatibilidade de federação do AD do Azure
description: Esta página contém provedores de identidade de terceiros que podem ser usados para implementar o logon único.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: billmath
ms.openlocfilehash: e7239ae88c6b4e56fa7c49f64c30fe602d8ec5fe
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32148838"
---
# <a name="azure-ad-federation-compatibility-list"></a>Lista de compatibilidade de federação do AD do Azure
O Azure Active Directory fornece logon único e segurança aprimorada de acesso ao aplicativo para o Office 365 e outros serviços do Microsoft Online para implementações híbridas e apenas de nuvem, sem a necessidade de qualquer solução de terceiros. O Office 365, como a maioria dos serviços online da Microsoft, é integrado ao Azure Active Directory para autorização, autenticação e serviços de diretório. O Azure Active Directory também fornece logon único a milhares de aplicativos SaaS e aplicativos Web locais. Consulte a [galeria de aplicativos](https://azuremarketplace.microsoft.com/marketplace/apps) do Azure Active Directory para aplicativos SaaS com suporte. 

## <a name="idp-validation"></a>Validação de IDP
Se sua organização usa uma solução de federação de terceiros, você pode configurar o logon único para os usuários do Active Directory local com os Serviços Online da Microsoft, como o Office 365, desde que a solução de federação de terceiros seja compatível com o Azure Active Directory.  Para dúvidas sobre a compatibilidade, entre em contato com seu provedor de identidade.  Se você quiser ver uma lista de provedores de identidade testados anteriormente quanto à compatibilidade com o Microsoft Azure Active Directory, pela Microsoft, clique em [aqui](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>A Microsoft não fornece mais testes de validação para provedores de identidade independentes quanto à compatibilidade com o Microsoft Azure Active Directory. Se você deseja testar a interoperabilidade do seu produto, consulte estas [diretrizes](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Próximas etapas

- [Integrar seus diretórios locais no Azure Active Directory](active-directory-aadconnect.md)
- [Azure AD Connect e federação](active-directory-aadconnectfed-whatis.md)
