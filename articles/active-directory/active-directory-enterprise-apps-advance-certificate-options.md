---
title: "Opções avançadas de assinatura de certificado no token SAML para aplicativos pré-integrados no Azure Active Directory | Microsoft Docs"
description: "Saiba como usar as opções avançadas de assinatura de certificado no token SAML para aplicativos pré-integrados no Azure Active Directory"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: 70e495965287a0edcb31493b69311fe28e04f6dc
ms.contentlocale: pt-br
ms.lasthandoff: 09/19/2017

---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>As opções avançadas de assinatura de certificado no token SAML para aplicativos da galeria no Azure Active Directory
Hoje, o Azure Active Directory dá suporte a milhares de aplicativos pré-integrados na Galeria de Aplicativos do Azure AD, incluindo mais de 500 que dão suporte ao logon único usando o protocolo SAML 2.0. Quando um usuário é autenticado em um aplicativo por meio do Azure AD usando SAML, o Azure AD envia um token ao aplicativo (por um HTTP POST). Em seguida, o aplicativo é validado e usa o token para conectar o usuário em vez de solicitar um nome de usuário e a senha. Esses tokens SAML são assinados com o certificado exclusivo que é gerado no Azure AD. Esse token SAML é assinado com os algoritmos padrão específicos.

O Azure Active Directory usa algumas das configurações padrão para os aplicativos de galeria. Com base no requisito de aplicativo, os valores padrão são configurados.

Configurações avançadas de assinatura de certificado de suporte ao Azure Active Directory. Para selecionar essas opções, primeiro marque a caixa de seleção **Configurações avançadas de assinatura de certificado SAML** caixa de seleção da seguinte mostrado.

![Opções de assinatura de certificado][1]

Se essa caixa de seleção estiver marcada, você poderá configurar as **Opções de assinatura de certificado** e o **Algoritmo de assinatura de certificado**.

## <a name="certificate-signing-options"></a>Opções de assinatura de certificado

A seguir, os três tipos de certificado, as opções de assinatura de Certificado com suporte do Azure AD.

1. **Asserção SAML de assinatura** - essa é a opção padrão definida para a maioria dos aplicativos de galeria. Se essa opção for selecionada, o Azure AD, como um IDP, assinará as Asserções SAML e o certificado com o certificado X509 do aplicativo. Além disso, ele usa o algoritmo de assinatura, que é selecionado na lista suspensa abaixo.

2. **Assinar resposta SAML** - se essa opção for selecionada e o Azure AD como IDP assina a resposta SAML com o certificado X509 do aplicativo. Além disso, ele usa o algoritmo de assinatura, que é selecionado na lista suspensa abaixo.

3. **Assinar resposta e asserção SAML** - se essa opção for selecionada, o Azure AD, como IDP, assinará o token SAML inteiro com o certificado X509 do aplicativo. Além disso, ele usa o algoritmo de assinatura, que é selecionado na lista suspensa abaixo.

    ![Opções de assinatura de certificado][4]

## <a name="certificate-signing-algorithm"></a>Algoritmo de assinatura de certificado

O Azure Active Directory dá suporte a dois tipos de algoritmo de assinatura para assinar a resposta SAML.

1. SHA256 - é o algoritmo padrão usado pelo Azure Active Directory para assinar Resposta SAML. Este é o algoritmo mais recente e é considerado mais seguro do que SHA1. A maior parte do aplicativo dá suporte ao algoritmo SHA256. Se o aplicativo der suporte apenas a Sha1 como algoritmo de assinatura, então isso poderá ser alterado. Caso contrário, é recomendável usar o algoritmo SHA256 para assinar a Resposta SAML.

    ![Algoritmo de assinatura de certificado SHA256][3]

2. SHA1 - este é o algoritmo mais antigo e não é tratado como seguro. Se o aplicativo der suporte somente a esse algoritmo de assinatura, você poderá selecionar essa opção na lista suspensa. Com isso, o Azure AD assina a resposta SAML com o algoritmo Sha1.

    ![Algoritmo de assinatura de certificado SHA1][2]

## <a name="next-steps"></a>Próximas etapas
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](active-directory-saas-custom-apps.md)
* [Solução de problemas de logon único baseado em SAML](develop/active-directory-saml-debugging.md)

<!--Image references-->

[1]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png
[2]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png
[3]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png
[4]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png
