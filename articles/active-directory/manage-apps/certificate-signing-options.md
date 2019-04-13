---
title: Opções avançadas de assinatura de certificado no token SAML para aplicativos pré-integrados no Azure Active Directory | Microsoft Docs
description: Saiba como usar as opções avançadas de assinatura de certificado no token SAML para aplicativos pré-integrados no Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: celested
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bede53ef2bc05750be21f831fc0cb790a001c6c5
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549330"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>As opções avançadas de assinatura de certificado no token SAML para aplicativos da galeria no Azure Active Directory

Atualmente, o Azure Active Directory (AD do Azure) oferece suporte a milhares de aplicativos pré-integrados na Galeria de aplicativo do Azure Active Directory. Mais de 500 aplicativos suportar o logon único usando o [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0 do protocolo, como o [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) aplicativo. Quando um cliente é autenticado em um aplicativo por meio do Azure AD usando SAML, o Azure AD envia um token ao aplicativo (por meio de um HTTP POST). O aplicativo, em seguida, valida e usa o token para entrar no cliente, em vez de solicitar um nome de usuário e senha. Esses tokens SAML são assinados com o certificado exclusivo que é gerado no Azure AD e por algorítimos padrão específicos.

O Azure AD usa algumas das configurações padrão para os aplicativos de galeria. Os valores padrão são configurados com base nos requisitos do aplicativo.

No Azure AD, você pode configurar opções de assinatura de certificado e o algoritmo de assinatura.

## <a name="certificate-signing-options"></a>Opções de assinatura de certificado

O Azure AD dá suporte a três opções de assinatura de certificado:

* **Assinar declaração SAML**. A opção padrão é definida para a maioria dos aplicativos de galeria. Se você selecionar essa opção, o Azure AD como provedor de identidade (IdP) assina a asserção SAML e o certificado com o [x. 509](https://wikipedia.org/wiki/X.509) certificado do aplicativo.

* **Assinar resposta SAML**. Se você selecionar essa opção, o Azure AD como IdP assina a resposta SAML com o certificado X.509 do aplicativo.

* **Assinar resposta SAML e declaração**. Se você selecionar essa opção, o Azure AD como IdP assina o token SAML inteiro com um certificado X.509 do aplicativo.

## <a name="certificate-signing-algorithms"></a>Algoritmo de assinatura de certificado

Azure AD dá suporte a dois algoritmos de assinatura ou algoritmos de hash seguro (SHAs) para assinar resposta SAML:

* **SHA-256**. O Azure AD dá suporte a dois algoritmos de assinatura para assinar resposta SAML. É o algoritmo mais recente e é mais seguro do que o SHA-1. A maioria dos aplicativos dá suporte ao algoritmo SHA-256. Se um aplicativo suporta somente SHA-1 como o algoritmo de assinatura, você pode alterá-lo. Caso contrário, é recomendável usar o algoritmo SHA256 para assinar a resposta SAML.

* **SHA-1**. Esse algoritmo é mais antigo, e ele é tratado como menos segura do que o SHA-256. Se o aplicativo der suporte somente a esse algoritmo de assinatura, você poderá selecionar essa opção na lista suspensa de **Algorítimo de Assinatura**. O Azure AD assina a resposta SAML com o algoritmo SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Alterar opções de assinatura e o algoritmo de assinatura de certificado

Para alterar as opções de assinatura de certificado de SAML do aplicativo e o algoritmo de assinatura de certificado, selecione o aplicativo em questão:

1. No [portal do Azure Active Directory](https://aad.portal.azure.com/), entre sua conta. O **Centro de administração do Azure Active Directory** página será exibida.
1. No painel esquerdo, selecione **Aplicativos Empresariais**. É exibida uma lista de aplicativos empresariais em sua conta.
1. Selecione um aplicativo. É exibida uma página de visão geral do aplicativo.

   ![Página de visão geral do aplicativo](./media/certificate-signing-options/application-overview-page.png)

Em seguida, altere o opções no token SAML para o aplicativo de assinatura de certificado:

1. No painel esquerdo da página de visão geral do aplicativo, selecione **logon único**.

2. Se o **definir o logon único com SAML - visualização** página for exibida, vá para a etapa 5.

3. Se o **selecionar um método de logon único** página não aparece, selecione **alterar modos de logon únicos** para exibir a página.

4. No **selecionar um método de logon único** , selecione **SAML** se disponível. (Se **SAML** não estiver disponível, o aplicativo não dá suporte a SAML, e você pode ignorar o restante deste procedimento e o artigo.)

5. No **definir o logon único com SAML - Preview** página, localize o **certificado de autenticação SAML** título e selecione o **editar** ícone (um lápis). O **certificado de autenticação SAML** página será exibida.

   ![Página de entrada do SAML](./media/certificate-signing-options/saml-signing-page.png)

6. No **opção de assinatura** lista suspensa, escolha **assinar resposta SAML**, **assinar declaração SAML**, ou **assinar resposta SAML e declaração**. Descrições dessas opções aparecem neste artigo na [opções de assinatura de certificado](#certificate-signing-options).

7. No **algoritmo de assinatura** lista suspensa, escolha **SHA-1** ou **SHA-256**. Descrições dessas opções aparecem neste artigo na [algoritmos de assinatura de certificado](#certificate-signing-algorithms) seção.

8. Se você estiver satisfeito com suas escolhas, selecione **salvar** aplicar de nova configurações de certificado de assinatura de SAML. Caso contrário, selecione a **X** para descartar as alterações.

## <a name="next-steps"></a>Próximos passos

* [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](configure-federated-single-sign-on-non-gallery-applications.md)
* [Solução de problemas de logon único baseado em SAML](../develop/howto-v1-debug-saml-sso-issues.md)
