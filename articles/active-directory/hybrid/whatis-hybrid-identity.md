---
title: Conectar o Active Directory com o Azure Active Directory. | Microsoft Docs
description: O Azure AD Connect integrará seus diretórios locais com o Azure Active Directory.  Isso permite que você forneça uma identidade comum para os aplicativos do Office 365, Azure e SaaS integrados ao AD do Azure.
keywords: introdução ao Azure AD Connect, visão geral do Azure AD Connect, o que é o Azure AD Connect, instalar o active directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fc18353ff89f8613847c977066e51620fb3e834f
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55494657"
---
# <a name="what-is-hybrid-identity"></a>O que é identidade híbrida? 

Hoje, as empresas e corporações estão se tornando cada vez mais uma mistura de aplicativos locais e na nuvem.  Os usuários precisam ter acesso local e na nuvem a esses aplicativos. Esse requisito se tornou um cenário desafiador. 

As soluções de identidade da Microsoft abrangem funcionalidades locais e baseadas em nuvem.  Essas soluções criam uma identidade de usuário comum para autenticação e autorização para todos os recursos, independentemente da localização. Chamamos isso de **identidade híbrida**.

Para obter identidade híbrida, pode ser usado um dos três métodos de autenticação, dependendo dos seus cenários.   Os três métodos são: 

- **[PHS (sincronização de hash de senha)](whatis-phs.md)**  
- **[PTA (autenticação de passagem)](how-to-connect-pta.md)**  
- **[Federação](whatis-fed.md)** 

Esses métodos de autenticação também fornecem funcionalidades de [logon único](how-to-connect-sso.md).  O logon único faz logon automaticamente dos seus usuários quando eles estão em seus dispositivos corporativos, conectados à sua rede corporativa.

Para saber mais, confira [Escolha o método de autenticação certo para sua solução de identidade híbrida do Azure Active Directory](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). 

## <a name="common-scenarios-and-recommendations"></a>Cenários e recomendações comuns 

Aqui estão alguns dos cenários comuns de gerenciamento de acesso e identidade híbrida com recomendações sobre qual opção de identidade híbrida pode ser apropriada para cada um. 

|Eu preciso de:|PHS e SSO<sup>1</sup>| PTA e SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Sincronizar automaticamente na nuvem as contas de novo usuário, de contato e de grupo criadas no meu Active Directory local.|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendadas](./media/whatis-hybrid-identity/ic195031.png) |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Configurar meu locatário para cenários híbridos do Office 365|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendadas](./media/whatis-hybrid-identity/ic195031.png) |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Habilitar os usuários para entrar e acessar serviços de nuvem usando suas senhas locais|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendadas](./media/whatis-hybrid-identity/ic195031.png) |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Implementar o logon único usando credenciais corporativas|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendadas](./media/whatis-hybrid-identity/ic195031.png) |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|  
|Certificar-me de que os hashes de senha não sejam armazenados na nuvem| |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Habilitar soluções de autenticação multifator de nuvem| |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Habilitar soluções de autenticação multifator locais| | |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Suporte à autenticação de cartão inteligente para meus usuários<sup>4</sup>| | |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Exibir notificações de expiração de senha no Portal do Office e na área de trabalho do Windows 10| | |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Sincronização de hash de senha com logon único. 
> 
> <sup>2</sup> Autenticação de passagem e Logon único.  
> 
> <sup>3</sup> Logon único federado com o AD FS.  
>  
> <sup>4</sup> O AD FS pode ser integrado com a Enterprise PKI para permitir a entrada usando certificados. Esses certificados podem ser certificados suaves implantados por meio dos canais de provisionamento confiáveis como certificados de cartão inteligente (incluindo cartões PIV/CAC), MDM, GPO ou Hello for Business (certificado confiável). Para obter mais informações sobre o suporte à autenticação de cartão inteligente, consulte [este blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/). 
> 

## <a name="next-steps"></a>Próximas etapas 

- [O que é o Azure AD Connect e o Connect Health?](whatis-azure-ad-connect.md) 
- [O que é PHS (sincronização de hash de senha)?](whatis-phs.md) 
- [O que é PTA (autenticação de passagem)?](how-to-connect-pta.md) 
- [O que é federação?](whatis-fed.md) 
- [O que é logon único?](how-to-connect-sso.md) 

