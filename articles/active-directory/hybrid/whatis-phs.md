---
title: O que é sincronização de hash da senha com o Azure AD? | Microsoft Docs
description: Descreve a sincronização de hash de senha.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 12/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ae4244a9ce17cdcb5942100e97b67916d455d019
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470235"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>O que é sincronização de hash da senha com o Azure AD?
A sincronização de hash de senha é um dos métodos de entrada usados para atingir a identidade híbrida. O Azure AD Connect sincroniza um hash do hash da senha do usuário de uma instância do Active Directory local para uma instância do Azure AD baseada em nuvem.

A sincronização de hash de senha é uma extensão para o recurso de sincronização de diretório, implementado pelo Azure AD Connect Sync. Você pode usar esse recurso para entrar em serviços do Azure AD como o Office 365. Você entra no serviço usando a mesma senha que usa para entrar sua instância do Active Directory local.

![O que é o Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

A sincronização de hash de senha ajuda reduzindo o número de senhas que os usuários precisam manter para apenas uma. A sincronização de hash de senha pode:

* Aumentar a produtividade dos seus usuários.
* Reduzir os custos de assistência técnica.  

Opcionalmente, você poderá configurar a sincronização de hash de senha como um backup se optar por usar os [Serviços de Federação do Active Directory (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) como seu método de entrada.

Para usar a sincronização de hash de senha no seu ambiente, você precisa:

* Instalar o Azure AD Connect.  
* Configurar a sincronização de diretórios entre sua instância do Active Directory local e a instância do Azure Active Directory.
* Permitir a sincronização de hash de senha.



Para obter mais informações, confira [O que é a identidade híbrida?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Próximas etapas

- [O que é identidade híbrida?](whatis-phs.md)
- [O que é o Azure AD Connect e o Connect Health?](whatis-azure-ad-connect.md)
- [O que é PTA (autenticação de passagem)?](how-to-connect-pta.md)
- [O que é federação?](whatis-fed.md)
- [O que é logon único?](how-to-connect-sso.md)
- [Como a sincronização de hash de senha funciona](how-to-connect-password-hash-synchronization.md)