---
title: Habilitar a avaliação usando o Azure AD | Azure
description: Habilitar o tipo de listagem de avaliação usando o Azure AD (Azure Active Directory) no Azure Marketplace e no AppSource para editores de aplicativos e serviços
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/04/2018
ms.author: ellacroi
ms.openlocfilehash: 4140ba98c0c65c22674c61dc7266818af904e777
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825741"
---
# <a name="enable-trial-using-azure-ad"></a>Habilitar a avaliação usando o Azure AD  
O Azure AD (Azure Active Directory) é um serviço de identidade de nuvem que habilita a autenticação com uma conta corporativa ou de estudante da Microsoft usando estruturas padrão do setor: OAuth e OpenID Connect.  
*   Para obter mais informações sobre o Azure AD, visite a página Azure Active Directory localizada em [azure.microsoft.com/services/active-directory](https://azure.microsoft.com/services/active-directory).  

Você e seus clientes são autenticados no marketplace usando o Azure AD. Depois que o cliente seleciona sua listagem de avaliação no marketplace, ele é redirecionado para o ambiente de avaliação.  Em seu ambiente avaliação, você pode configurar seu cliente diretamente sem precisar etapas de entrada adicionais. Seu aplicativo ou oferta recebe um token do Azure AD durante a autenticação que inclui informações de usuário valiosas para a criação da conta do usuário no aplicativo ou oferta. Você pode automatizar a configuração e aumentar a probabilidade de conversão.  
*   Para obter mais informações sobre o token enviado do Azure AD durante a autenticação, visite a seção Tokens de exemplo localizada em [docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)

Use o Azure AD para habilitar a autenticação em um clique no aplicativo ou avaliação.  
*   Simplifique a experiência do cliente do marketplace para avaliação.  
*   Mantenha a percepção de uma "experiência no produto", mesmo quando o usuário é redirecionado do Marketplace para o seu domínio ou ambiente de avaliação.  
*   Reduza a probabilidade de abandono no redirecionamento porque não há etapas de entrada adicionais.  
*   Reduza as barreiras de implantação para a grande população de usuários do Azure AD.  

## <a name="verify-your-azure-ad-integration-on-the-marketplace-multitenant-apps"></a>Verificar a integração do Azure AD ao marketplace: aplicativos multilocatários  
Dê suporte às seguintes opções para sua solução usando o Azure AD.  
*   Registre seu aplicativo nas vitrines do marketplace.  
*   Habilite o recurso de suporte multilocatário no Microsoft Azure Active Directory para obter uma experiência de avaliação com um clique.  
    *   Para obter mais informações sobre o registro do aplicativo, visite a página Integrando aplicativos com o Azure Active Directory em [docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Se você não tiver experiência com o uso do SSO (logon único) federado do Azure AD, siga essas etapas.  
1.  Registre seu aplicativo no marketplace. 
2.  Desenvolva o SSO com o Azure AD usando o OAuth 2.0 ou o OpenID Connect.  
    *   Para obter mais informações sobre o OAuth 2.0, visite a página do OAuth 2.0 em [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).  
    *   Para obter mais informações sobre o Open ID Connect, visite a página do OpenID Connect em [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  
3.  Habilite o recurso de suporte multilocação no Azure AD para fornecer uma experiência de avaliação com um clique.  
    *   Para obter mais informações sobre a Certificação do AppSource, visite a página de Certificação do AppSource em [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

## <a name="verify-your-azure-ad-integration-on-the-marketplace-single-tenant-apps"></a>Verificar a integração do Azure AD ao marketplace: aplicativos de locatário único  
Dê suporte a uma das opções a seguir para sua solução de locatário único.  
*   Adicione usuários ao diretório como usuários convidados usando o B2B do Azure AD.  
    *   Para obter mais informações sobre o B2B do Azure AD, visite a página O que é a colaboração do B2B do Azure AD em [docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Configure manualmente avaliações para clientes por meio do recurso Entrar em contato comigo.  
*   Desenvolva um test drive por cliente.  
*   Compile um aplicativo de demonstração de amostra multilocatário com SSO.  

## <a name="next-steps"></a>Próximas etapas
*   Visite a página [Guia do editor do Azure Marketplace e AppSource](./marketplace-publishers-guide.md).  
 
---  

