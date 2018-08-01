---
title: Habilitar uma avaliação no Microsoft Azure Marketplace usando o Microsoft Azure Active Directory | Azure
description: Habilitar o tipo de listagem de avaliação usando o Microsoft Azure Active Directory no Microsoft Azure Marketplace e no AppSource para editores de serviços e aplicativos.
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
ms.openlocfilehash: c5b7b4967c1acef733d366e651d50706db42aace
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160460"
---
# <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Habilitar uma listagem de avaliação usando o Microsoft Azure Active Directory

O Azure AD (Azure Active Directory) é um serviço de identidade em nuvem que permite autenticação com uma conta corporativa ou de estudante da Microsoft usando estruturas padrão do setor. Azure AD dá suporte à autenticação OAuth e OpenID Connect. O [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com) usa o Azure AD para autenticar você e seus clientes.

Para obter mais informações sobre o Azure AD, consulte [Azure Active Directory](https://azure.microsoft.com/services/active-directory).

Após um cliente selecionar a listagem de avaliação no Marketplace, ele será redirecionado para o ambiente de avaliação. No ambiente avaliação, é possível configurar o cliente diretamente sem necessidade de etapas de entrada adicionais. O aplicativo ou a oferta recebe um token do Azure AD durante a autenticação. O token inclui informações valiosas do usuário que são usadas para criar uma conta de usuário no aplicativo ou oferta. É possível automatizar a configuração do cliente e aumentar a probabilidade de conversão.

Para obter mais informações sobre o token que é enviado do AD do Azure durante a autenticação, consulte [Tokens de exemplo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Use o Azure AD para habilitar a autenticação em um clique no aplicativo ou avaliação. O Azure AD oferece os seguintes benefícios: 
*   Simplifique a experiência do cliente do Marketplace para avaliação.
*   Mantenha a percepção de uma "experiência no produto", mesmo quando o usuário é redirecionado do Marketplace para o seu domínio ou ambiente de avaliação.
*   Reduza a probabilidade de abandono no redirecionamento porque não há etapas de entrada adicionais.
*   Reduza as barreiras de implantação para a grande população de usuários do Azure AD.

## <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Verifique a integração do Azure AD no Marketplace: aplicativos multilocatários
Use o Azure AD para dar suporte às seguintes opções para sua solução:
*   Registre seu aplicativo nas vitrines do Marketplace.
*   Habilite o recurso de suporte multilocatário no Microsoft Azure Active Directory para obter uma experiência de avaliação com um clique.

Para obter mais informações, consulte [Integrar aplicativos com o Microsoft Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

Se você não tiver experiência com uso do SSO (logon único) federado do Azure AD, siga estas etapas:
1.  Registre seu aplicativo no Marketplace. 
2.  Desenvolva o SSO com o Azure AD usando o OAuth 2.0 ou o OpenID Connect.
    *   Para obter mais informações sobre o OAuth 2.0, consulte [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
    *   Para obter mais informações sobre o Open ID Connect, consulte [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
3.  Habilite o recurso de suporte multilocação no Azure AD para fornecer uma experiência de avaliação com um clique.
    
    Para obter mais informações sobre a certificação do AppSource, consulte [Certificação do AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

## <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Verificar a integração do Azure AD no Marketplace: aplicativos de locatário único
Use o Azure AD para dar suporte a uma das opções a seguir para sua solução de locatário único: 
*   Adicione usuários ao diretório como usuários convidados usando o B2B do Azure AD (Microsoft Azure Active Directory B2B).
    
    Para obter mais informações sobre o B2B do Azure AD, consulte [O que é a colaboração B2B do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Configure manualmente avaliações para clientes usando a opção de publicação Contact Me.
*   Desenvolva um test drive por cliente.
*   Crie um aplicativo de demonstração multilocatário de exemplo usando SSO.

## <a name="next-steps"></a>Próximas etapas
*   Revise o [guia de publicação do Microsoft Azure Marketplace e AppSource](./marketplace-publishers-guide.md).
