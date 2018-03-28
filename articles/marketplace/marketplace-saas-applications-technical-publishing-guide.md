---
title: Guia de Publicação Técnica de Aplicativos SaaS do Azure Marketplace
description: Guia passo a passo e listas de verificação de publicação para publicação de aplicativos SaaS no Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/28/2018
ms.author: pabutler
ms.openlocfilehash: 64becc80192e69bd332d6657637c845acf93748b
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="saas-applications-technical-publishing-guide"></a>Guia de publicação técnica de aplicativos SaaS

Bem-vindo ao Guia de Publicação Técnica de Aplicativos SaaS do Azure Marketplace. Este guia foi desenvolvido para ajudar candidatos e editores existentes a listar aplicativos e serviços no Azure Marketplace utilizando a oferta de Aplicativos SaaS.  
Você deseja usar a oferta de Aplicativos SaaS quando a solução for implantada em sua própria assinatura do Azure e os clientes fizerem logon por meio de uma interface projetada e gerenciada por você para testar o aplicativo. Isso é feito utilizando o [Microsoft Azure AD (Azure Active Directory)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis) para aproveitar o ambiente de avaliação existente. Em outras palavras, é uma avaliação gratuita orientada pelo cliente e hospedada pelo parceiro. É fundamental expor a solução de forma que os compradores de nuvem tenham a oportunidade de experimentar a solução de maneira independente, sem custos ou valor e, nessa perspectiva, esse tipo de oferta fornece uma experiência de avaliação que corresponde à maneira como os clientes pesquisam soluções na nuvem.  

Para obter uma visão geral de todas as outras ofertas do Marketplace, consulte o [Guia do Editor do Marketplace](https://aka.ms/sellerguide).

## <a name="saas-application-technical-guidance"></a>Diretrizes técnicas para aplicativo SaaS
Os requisitos técnicos para aplicativos SaaS são simples. Os editores apenas precisam estar integrados ao Microsoft Azure AD para serem publicados.  A integração do Microsoft Azure AD com aplicativos está bem documentada e a Microsoft fornece vários SDKs e recursos para realizar isso.  

Para começar, é recomendável que você tenha uma assinatura dedicada à publicação do Azure Marketplace, o que permite isolar o trabalho de outras iniciativas. Além disso, se ainda não estiver instalado, é recomendável que ter as seguintes ferramentas como parte do ambiente de desenvolvimento: 
- [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)  
- [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-5.0.0)  
- [Ferramentas para Desenvolvedores do Azure (analise o que está disponível)](https://azure.microsoft.com/tools/)  
- [Visual Studio Code](https://code.visualstudio.com/)  

### <a name="resources"></a>Recursos
As listas a seguir fornecem links para os melhores recursos do Microsoft Azure AD para você começar: 

**Documentação**

- [Guia do desenvolvedor do Active Directory do Azure](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)

- [Integração com Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-how-to-integrate)

- [Integrar aplicativos com Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

- [Roteiro do Azure - Segurança e Identidade](https://azure.microsoft.com/roadmap/?category=security-identity)

**Vídeos**

- [ Autenticação do Azure Active Directory com Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

- [Resumo técnico de Identidade do Azure Active Directory - Parte 1 de 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

- [Resumo técnico de Identidade do Azure Active Directory - Parte 2 de 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

- [Compilar aplicativos com Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

- [Vídeos do Microsoft Azure focados no Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

**Treinamento**  
- [ Microsoft Azure para Séries de Conteúdo para Profissionais de TI: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

**Atualizações de Serviço do Azure Active Directory**  
- [Atualizações de serviço do Microsoft Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Para suporte, é possível utilizar os seguintes recursos:
- [Fóruns do MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
- [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="the-azure-active-directory-gallery"></a>Galeria do Azure Active Directory
Além de ter o aplicativo listado no Azure Marketplace/AppSource, você também pode ter o aplicativo listado na galeria de aplicativos do Microsoft Azure AD que faz parte da AppStore do Azure Marketplace. Clientes que utilizam o Microsoft Azure AD como um provedor de identidade podem localizar os diferentes conectores de aplicativo SaaS publicados aqui. Administradores de TI podem adicionar conectores da galeria de aplicativos e, em seguida, configurar e usar os conectores para SSO (logon único) e provisionamento. O Microsoft Azure AD fornece suporte a todos os principais protocolos de federação para SSO, incluindo SAML 2.0, OpenID Connect, OAuth e WS-Fed.  

Depois de testar sua integração de aplicativos com o Microsoft Azure AD, envie sua solicitação de acesso em nosso Portal de Rede de Aplicativos. Se você tiver uma conta do Office 365, utilize-a para entrar nesse portal. Se você não tiver uma conta do Office 365, poderá utilizar sua conta da Microsoft (como o Outlook ou Hotmail) para entrar.

## <a name="program-benefits"></a>Benefícios do programa
- Fornece a melhor experiência de logon único possível para clientes
- Configuração simples e mínima do aplicativo
- Os clientes podem pesquisar o aplicativo e localizá-lo na galeria
- Qualquer cliente pode utilizar essa integração, independentemente de qual SKU do Microsoft Azure AD que utilizam (Gratuito, Básico ou Premium)
- Tutorial de configuração passo a passo para nossos clientes mútuos
- Permite o provisionamento de usuário para o mesmo aplicativo se você estiver utilizando SCIM

## <a name="prerequisites"></a>pré-requisitos
Para listar um aplicativo na galeria do Microsoft Azure AD, o aplicativo deve primeiro implementar um dos protocolos de federação com suporte pelo Microsoft Azure AD. Leia os termos e as condições da galeria de aplicativos do Microsoft Azure AD localizados em [Informações Legais do Microsoft Azure](https://azure.microsoft.com/support/legal/).  

A seguir, informações adicionais sobre pré-requisitos, dependendo do protocolo que está sendo utilizado:

**OpenID Connect** - Crie o aplicativo de multilocatário no Microsoft Azure AD e implemente a estrutura de consentimento para o aplicativo. Envie a solicitação de logon para o ponto de extremidade comum para que qualquer cliente possa fornecer consentimento para o aplicativo. Controle o acesso do usuário cliente com base na ID de locatário e no nome UPN do usuário recebido no token.  
**SAML 2.0 ou WS-Fed** - Seu aplicativo deve ter a capacidade de fazer a integração SAML ou integração de SSO do WS-Fed no modo IdP ou SP.
