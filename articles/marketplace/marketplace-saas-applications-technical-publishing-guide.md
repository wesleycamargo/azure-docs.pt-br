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
ms.date: 05/09/2018
ms.author: pabutler
ms.openlocfilehash: 48b0b4177dad6262105bf30be2b8714f6ea1228f
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076691"
---
# <a name="saas-applications-technical-publishing-guide"></a>Guia de publicação técnica de aplicativos SaaS

Bem-vindo ao Guia de Publicação Técnica de Aplicativos SaaS do Azure Marketplace. Este guia foi desenvolvido para ajudar candidatos e editores existentes a listar aplicativos e serviços no Azure Marketplace utilizando a oferta de Aplicativos SaaS. 

Para entender melhor como publicar uma oferta de SaaS, este guia é dividido nas seguintes seções:
* Visão geral da oferta
* Requisitos de negócios
* Requisitos técnicos
* Processo de publicação
* Usar o Azure Active Directory para habilitar avaliações
* Certificar a integração do Azure AD para Marketplace

## <a name="offer-overview"></a>Visão geral da oferta  

Aplicativos SaaS estão disponíveis em ambas as vitrines do Azure A tabela a seguir descreve as opções disponíveis atualmente:

| Opção de vitrine | Listagem | Avaliação/Transação |  
| --- | --- | --- |  
| AppSource | Sim (Entrar em contato comigo) | Sim (PowerBI/Dynamics) |
| Azure Marketplace | Não  | Sim (Aplicativos SaaS) |   

**Listagem:** a opção de publicação de lista é uma oferta do tipo "Entrar em contato comigo" usada quanto a participação no nível Avaliação ou Transação não é viável. O benefício dessa abordagem é permitir que os editores com uma solução no mercado comecem imediatamente a receber clientes potenciais que possam ser convertidos em ofertas para impulsionar seu negócio.  
**Avaliação/Transação:** o cliente tem a opção de comprar diretamente ou solicitar uma avaliação para sua solução. Fornecer uma experiência de avaliação aumenta o nível de engajamento oferecido aos clientes e permite explorar a solução antes da compra. Com uma experiência de avaliação, você terá melhores oportunidades de promoção nas vitrines e pode esperar mais clientes potenciais mais valiosos com base no engajamento dos clientes. As Avaliações devem incluir suporte gratuito pelo menos durante o período de avaliação.  

| Oferta de aplicativos SaaS | Requisitos de negócios | Requisitos técnicos |  
| --- | --- | --- |  
| **Fale conosco** | sim | Não  |  
| **Power BI/Dynamics** | sim | Sim (Integração com o Azure AD) |  
| **Aplicativos SaaS**| sim | Sim (Integração com o Azure AD) |     

Para obter mais informações sobre as vitrines do Marketplace e uma descrição de cada opção de publicação, confira o [Guia do Editor do Marketplace](https://aka.ms/sellerguide) e as [Opções de Publicação](https://docs.microsoft.com/en-us/azure/marketplace/marketplace-publishers-guide#select-a-publishing-option).

## <a name="business-requirements"></a>Requisitos de negócios
Os requisitos de negócios da oferta SaaS podem ser concluídos em paralelo com os requisitos técnicos. A maioria dos requisitos de negócios e informações são coletadas durante a criação da oferta de SaaS no Portal do Cloud Partner. Os requisitos de negócios são os seguintes: 
* Concordar com as políticas de participação
* Integração com a Microsoft 
* Identificação do público-alvo da oferta
* Definição e determinação do gerenciamento de clientes potenciais a ser usado
* Definição da política de privacidade e dos termos de uso
* Definição dos contatos de suporte  

Mais informações podem ser encontradas no tópico [Pré-requisitos para publicação no Marketplace](https://docs.microsoft.com/en-us/azure/marketplace/marketplace-publishers-guide#prerequisites-for-marketplace-publishing)

## <a name="technical-requirements"></a>Requisitos técnicos

Os requisitos técnicos para aplicativos SaaS são simples. Os editores apenas precisam estar integrados ao Azure Active Directory (Azure AD) para serem publicados. A integração do Microsoft Azure AD com aplicativos está bem documentada e a Microsoft fornece vários SDKs e recursos para realizar isso.  

Para começar, é recomendável que você tenha uma assinatura dedicada à publicação do Azure Marketplace, o que permite isolar o trabalho de outras iniciativas. Depois disso, você pode começar a implantar o aplicativo SaaS nessa assinatura para iniciar o trabalho de desenvolvimento.  

As melhores documentações, exemplos e diretrizes do Azure Active Directory estão nestes sites: 

* [Guia do desenvolvedor do Active Directory do Azure](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)

* [Integração com Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrar aplicativos com Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

* [Roteiro do Azure - Segurança e Identidade](https://azure.microsoft.com/roadmap/?category=security-identity)

Para tutoriais em vídeo, examine o seguinte:

* [ Autenticação do Azure Active Directory com Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Resumo técnico de Identidade do Azure Active Directory - Parte 1 de 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Resumo técnico de Identidade do Azure Active Directory - Parte 2 de 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Compilar aplicativos com Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Vídeos do Microsoft Azure focados no Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Um treinamento gratuito do Azure Active Directory está disponível em  
* [ Microsoft Azure para Séries de Conteúdo para Profissionais de TI: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Além disso, o Azure Active Directory fornece um site para verificar se há atualizações de serviço   
* [Atualizações de serviço do Microsoft Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Para suporte, é possível utilizar os seguintes recursos:
* [Fóruns do MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="publishing-process"></a>Processo de publicação

O processo de publicação de SaaS tem etapas técnicas e de negócios.  A maior parte do trabalho de desenvolvimento e integração do Azure Active Directory pode ser feito em paralelo com o trabalho necessário para cumprir os requisitos de negócio da oferta. Grande parte dos requisitos de negócios fazem parte da configuração da oferta de aplicativo SaaS do Portal do Cloud Partner.  
O diagrama a seguir mostra que as principais etapas de publicação de Avaliação/Transação oferecem:  

![Etapas de publicação de SaaS](./media/marketplace-saas-applications-technical-publishing-guide/saaspublishingsteps.png)  

A tabela a seguir descreve cada uma das principais etapas de publicação:  

| Etapa de publicação | DESCRIÇÃO |   
| --- | --- |  
| **Criar o aplicativo SaaS** | Faça logon no Portal do Cloud Partner, clique em **Novo** e, em seguida, selecione a oferta **Aplicativos SaaS**. |  
| **Criar a integração com o Azure AD** | Siga os requisitos técnicos descritos na seção anterior para integrar a oferta SaaS com o Azure AD. |  
| **Definir as configurações de oferta**| Insira todas as informações iniciais da oferta SaaS. A ID de oferta e o Nome da Oferta que você gostaria de usar. |     
| **Definir as informações técnicas** | Insira as informações técnicas da oferta. Para aplicativos SaaS, o URI e o tipo do botão de aquisição da oferta da Solução (Gratuito, Trilha ou Entrar em contato comigo) são necessários. |  
| **Teste Drive (Opcional)** | Este é um tipo opcional de Avaliação, necessário principalmente para outros Tipos de Ofertas do Marketplace. Permite implantar a Avaliação nas Assinaturas do Editor vs. o cliente final. |  
| **Definir os materiais de vitrine da oferta**| Nesta seção, o editor vinculará e fará o upload dos logotipos, materiais de marketing, documentos legais e configurar o sistema de gerenciamento dos clientes potenciais. |
| **Definir os contatos de oferta** | Insira os contatos de engenharia e informações de contato de suporte para a oferta de SaaS. |  
| **Verificar a integração do Azure AD do aplicativo SaaS** | Antes de enviar seu aplicativo SaaS para publicação, verifique se o aplicativo está integrado ao Azure AD |  
| **Publicar a oferta**| Depois de concluir a oferta e os recursos técnicos, envie-a. Isso iniciará o processo de publicação, no qual o modelo de solução é testado, validado, certificado e aprovado para publicação. |

## <a name="using-azure-active-directory-to-enable-trials"></a>Usar o Azure Active Directory para habilitar avaliações  

A Microsoft autentica todos os usuários do Marketplace com o Azure AD, portanto, quando um usuário autenticado clicar na sua listagem de Avaliação no Marketplace e for redirecionado para seu ambiente de Avaliação, você poderá fornecer o usuário diretamente em uma Avaliação sem exigir uma etapa adicional de entrada. O token que seu aplicativo recebe do Azure AD durante a autenticação inclui informações valiosas do usuário que podem ser usadas para criar uma conta para ele no seu aplicativo, permitindo que você automatize a experiência de provisionamento e aumente a probabilidade de conversão. Para obter mais informações sobre o token, confira [Tokens de exemplo](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims).

Usar o Azure AD para habilitar a autenticação em 1 clique no aplicativo ou avaliação:  
* Simplifica a experiência do cliente do Marketplace para Avaliação.  
* Mantém a percepção de uma "experiência no produto", mesmo quando o usuário é redirecionado do Marketplace para o seu domínio ou ambiente de Avaliação.  
* Diminui a probabilidade de abandono no redirecionamento porque não há uma etapa de entrada adicional.  
* Reduz barreiras de implantação para a grande população de usuários do Microsoft Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certificar a integração do Azure AD para Marketplace  

É possível certificar a integração do Azure AD de maneiras diferentes, de acordo com o tipo do aplicativo, locatário único ou multilocatário, e se você for novato no SSO (logon único) federado do Azure AD ou já oferecer suporte para isso.  

**Para aplicativos multilocatários:**  

Se você já dá suporte ao Azure AD, faça o seguinte:
1.  Registre seu aplicativo no Portal do Azure
2.  Habilite o recurso de suporte multilocatário no Azure AD para obter uma experiência de avaliação com um clique. Encontre mais informações específicas [aqui](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications).  

Se você é novo no SSO Federado do Azure AD, faça o seguinte: 
1.  Registre seu aplicativo no Portal do Azure
2.  Desenvolva SSO com o Azure AD usando [OpenID Connect](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-openid-connect-code) ou [OAuth 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Habilite o recurso de suporte multilocatário no AAD para obter uma experiência de avaliação com um clique Encontre mais informações específicas [aqui](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Para o aplicativo de locatário único, use qualquer uma das seguintes opções:**  
* Adicionar usuários ao seu diretório como usuários convidados usando [B2B do Azure](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Provisionar manualmente avaliações para clientes por meio do "Entre em contato comigo"
* Desenvolver um test drive por cliente
* Compile um aplicativo de demonstração de amostra multilocatário com SSO

