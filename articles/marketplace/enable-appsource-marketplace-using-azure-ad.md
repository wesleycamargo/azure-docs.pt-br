---
title: Habilitar uma listagem do Microsoft AppSource e Azure Marketplace usando o Azure Active Directory | Azure
description: Habilitar o tipo de listagem usando o Microsoft Azure Active Directory no Microsoft Azure Marketplace e no AppSource para editores de serviços e aplicativos.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: d7fd09928c0a687755d216e7f10f7eac23677c63
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987337"
---
# <a name="enable-a-microsoft-appsource-and-azure-marketplace-listing-by-using-azure-active-directory"></a>Habilitar uma listagem do Microsoft AppSource e do Azure Marketplace usando o Azure Active Directory

O Azure AD (Microsoft Azure Active Directory) é um serviço de identidade de nuvem que permite a autenticação com uma conta Microsoft usando estruturas padrão do setor.  Para obter mais informações sobre o Azure AD, consulte [Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="benefits-of-using-azure-active-directory"></a>Benefícios ao usar o Azure Active Directory

Os clientes do Microsoft AppSource e do Azure Marketplace usam experiências no produto para pesquisar os catálogos de listagem, o que exige que entrem no produto.  Ao integrar o aplicativo ao Azure AD, você pode acelerar a participação e otimizar a experiência do cliente. Azure AD:

- Ativa SSO (logon único) para milhões de usuários corporativos.
- Permite a experiência de entrada do usuário em aplicativos publicados por diferentes parceiros.
- Fornece autenticação de plataforma cruzada e escalonável para os aplicativos móveis e na nuvem.

Conforme detalhado na seção abaixo, determinadas ofertas são necessárias para implementar o Azure AD para publicação no Marketplace.

## <a name="azure-active-directory-requirements"></a>Requisitos do Azure Active Directory

Há diferentes [opções de listagem e tipos de oferta](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) para o Microsoft AppSource e o Azure Marketplace.  Os requisitos do Azure AD para essas opções de listagem e tipos de oferta são mostrados abaixo:

| **Tipo de oferta**    | **É necessário SSO do AAD?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Entre em contato comigo | Avaliação | Test drive | Transação |
| Máquina Virtual | N/D | Não  | Não | Não  |
| Aplicativos Azure (modelo de solução)  | N/D | N/D | N/D | N/D |
| Aplicativos gerenciados  | N/D | N/D | N/D | Não  |
| SaaS  | Não  | sim | sim | SIM |
| Contêineres  | N/D | N/D | N/D | Não  |
| Serviços de Consultoria  | Não  | N/D | N/D | N/D |

Para obter mais informações sobre os requisitos técnicos de SaaS, consulte [Guia de publicação de oferta de aplicativo SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="integration-with-azure-active-directory"></a>Integração com Azure Active Directory

Para obter informações sobre como integrar o Azure AD para habilitar SSO, visite https://aka.ms/aaddev.

Para obter mais informações sobre SSO do Azure AD, consulte [O que é acesso de aplicativo e logon único com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)?

## <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Habilitar uma listagem de avaliação usando o Microsoft Azure Active Directory

Após um cliente selecionar a listagem de avaliação no Marketplace, ele será redirecionado para o ambiente de avaliação. No ambiente avaliação, é possível configurar o cliente diretamente sem necessidade de etapas de entrada adicionais. O aplicativo ou a oferta recebe um token do Azure AD durante a autenticação. O token inclui informações valiosas do usuário que são usadas para criar uma conta de usuário no aplicativo ou oferta. É possível automatizar a configuração do cliente e aumentar a probabilidade de conversão.

Para obter mais informações sobre o token que é enviado do AD do Azure durante a autenticação, consulte [Tokens de exemplo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Use o Azure AD para habilitar a autenticação em um clique no aplicativo ou avaliação. O Azure AD oferece os seguintes benefícios: 
*   Simplifique a experiência do cliente do Marketplace para avaliação.
*   Mantenha a percepção de uma "experiência no produto", mesmo quando o usuário é redirecionado do Marketplace para o seu domínio ou ambiente de avaliação.
*   Reduza a probabilidade de abandono no redirecionamento porque não há etapas de entrada adicionais.
*   Reduza as barreiras de implantação para a grande população de usuários do Azure AD.

### <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Verifique a integração do Azure AD no Marketplace: aplicativos multilocatários
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

### <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Verificar a integração do Azure AD no Marketplace: aplicativos de locatário único
Use o Azure AD para dar suporte a uma das opções a seguir para sua solução de locatário único: 
*   Adicione usuários ao diretório como usuários convidados usando o B2B do Azure AD (Microsoft Azure Active Directory B2B). Para obter mais informações sobre o B2B do Azure AD, consulte [O que é a colaboração B2B do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Configure manualmente avaliações para clientes usando a opção de publicação Contact Me.
*   Desenvolva um test drive por cliente.
*   Crie um aplicativo de demonstração de exemplo multilocatário que usa SSO.

## <a name="next-steps"></a>Próximas etapas

Caso ainda não tenha feito isso, 
- [Registre-se](https://azuremarketplace.microsoft.com/sell) no Marketplace.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em uma existente,
- [Entre no Portal do Cloud Partner](https://cloudpartner.azure.com/) para criar ou concluir a oferta.

