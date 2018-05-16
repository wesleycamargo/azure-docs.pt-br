---
title: Como depurar o logon único baseado em SAML nos aplicativos no Azure Active Directory | Microsoft Docs
description: 'Saiba como depurar o logon único baseado em SAML em aplicativos no Active Directory do Azure  '
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: dastrock; smalser
ms.openlocfilehash: 1a33b5ab9e26ed497e3be2d430f66ef41402733d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Como depurar o logon único baseado em SAML em aplicativos no Active Directory do Azure

Ao depurar uma integração de aplicativos baseada em SAML, geralmente é útil usar uma ferramenta como [Fiddler](http://www.telerik.com/fiddler) para ver a solicitação SAML e a resposta SAML que contêm o token SAML que foi emitido para um aplicativo. 

![Fiddler][1]

Frequentemente, os problemas estão relacionados a um erro de configuração no Azure Active Directory ou no aplicativo. Dependendo de onde você vê o erro, é necessário revisar a solicitação ou a resposta SAML:

- Eu vejo um erro na página de entrada da minha empresa [Links para a seção]
- Eu vejo um erro na página do aplicativo depois de entrar [Links para a seção]

## <a name="i-see-an-error-in-my-company-sign-in-page"></a>Eu vejo um erro na página de entrada da minha empresa.

É possível localizar um mapeamento de um para um entre o código de erro e as etapas de resolução em [Problemas ao entrar em um aplicativo de galeria configurado para logon único federado](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML).

Se visualizar um erro na página de entrada da empresa, você precisará da mensagem de erro e da solicitação SAML para depurar o problema.

### <a name="how-can-i-get-the-error--message"></a>Como posso obter a mensagem de erro?

Para obter a mensagem de erro, observe o canto inferior direito da página. Você vê um erro que inclui:

- Uma CorrelationID
- Um carimbo de data/hora
- Uma mensagem

![Erro][2] 

 
A ID de correlação e o carimbo de data/hora formam um identificador exclusivo que você pode usar para localizar os logs de back-end associados à falha de entrada. Esses valores são importantes ao criar um caso de suporte com a Microsoft, pois ajudam os engenheiros a fornecer uma resolução mais rápida para o problema.

A mensagem é a causa raiz do problema de entrada. 


### <a name="how-can-i-review-the-saml-request"></a>Como posso revisar a solicitação SAML?

A solicitação SAML enviada pelo aplicativo ao Azure Active Directory geralmente é a última resposta HTTP 200 de [https://login.microsoftonline.com](https://login.microsoftonline.com).
 
Usando o Fiddler, você pode visualizar a solicitação SAML selecionando essa linha e, em seguida, selecionando a guia **Inspetores > WebForms** no painel direito. Clique com o botão direito do mouse no valor **SAMLRequest** e, em seguida, selecione **Enviar para TextWizard**. Selecione **De Base64** no menu **Transformar** para decodificar o token e ver seu conteúdo. 

Além disso, você também pode copiar o valor no SAMLrequest e usar outro decodificador Base64.

    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    Destination=https://login.microsoftonline.com/<Tenant-ID>/saml2
    AssertionConsumerServiceURL="https://contoso.applicationname.com/acs"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>

Com a solicitação SAML decodificada, revise o seguinte:

1. O **Destino** na solicitação SAML corresponde à URL do serviço de logon único (SSO) do SAML obtida do Azure Active Directory.
 
2. O **Emissor** na solicitação SAML é o mesmo **Identificador** que você configurou para o aplicativo no Azure Active Directory. O Microsoft Azure AD usa o Emissor para localizar um aplicativo no diretório.

3. **AssertionConsumerServiceURL** é onde o aplicativo espera receber o token SAML do Azure Active Directory. É possível configurar esse valor no Azure Active Directory, mas não é obrigatório se fizer parte da solicitação SAML.


## <a name="i-see-an-error-on-the-applications-page-after-signing-in"></a>Eu vejo um erro na página do aplicativo depois de entrar

Nesse cenário, o aplicativo não está aceitando a resposta emitida pelo Microsoft Azure AD. É importante que você verifique a resposta do Microsoft Azure AD que contém o token SAML com o fornecedor do aplicativo para saber o que está errado ou faltando. 

### <a name="how-can-i-get-and-review-the-saml-response"></a>Como é possível obter e revisar a resposta SAML?

A resposta do Microsoft Azure AD que contém o token SAML geralmente é aquela que ocorre após um redirecionamento HTTP 302 de https://login.microsoftonline.com e é enviada para a **URL de Resposta** do aplicativo. 

Você pode exibir o token SAML selecionando essa linha e, em seguida, selecionando a guia **Inspetores > WebForms** no painel à direita. Nela, clique com botão direito no valor **SAMLResponse** e selecione **Enviar para TextWizard**. Em seguida, selecione **From Base64** do menu **Transformação** para decodificar o token e ver o conteúdo usando outro decodificador Base64. 

Você também pode copiar o valor no **SAMLrequest** e usar outro decodificador Base64.

Visite as diretrizes de solução de problemas de [Erro na página do aplicativo depois de entrar ](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML) para obter mais informações sobre o que pode estar errado ou faltando na resposta SAML.

Para obter informações sobre como revisar a resposta SAML, visite o artigo [Protocolo SAML de Logon único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML#response).


## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](../active-directory-apps-index.md)
* [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Como personalizar declarações emitidas no token SAML para aplicativos pré-integrados](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
[2]: ../media/active-directory-saml-debugging/error.png
