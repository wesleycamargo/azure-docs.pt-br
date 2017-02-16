---
title: "Os elementos do email de convite para colaboração B2B do Azure Active Directory | Microsoft Docs"
description: "Modelo de email de convite para colaboração B2B do Azure Active Directory"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/01/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 8fdd2993e6b3f0345948f35753ebb7a20ed174e3
ms.openlocfilehash: 5a31469014b7b0c7ea8a6d6a207646694c6c0b50


---


# <a name="the-elements-of-the-b2b-collaboration-invitation-email"></a>Os elementos do email de convite para colaboração B2B

Emails de convite são um componente essencial para ingressar parceiros como usuários de colaboração B2B no Azure AD. O objetivo principal é aumentar a confiança no destinatário e adicionar a legitimidade e a comprovação social ao email, para certificar-se de que o destinatário sinta-se confortável com a seleção do botão **Introdução** para aceitar o convite. Isso é um componente fundamental para a redução de conflitos de compartilhamento. E, obviamente, também queremos que o email tenha uma aparência incrível!

## <a name="explaining-the-email"></a>Explicação do email
Vamos examinar alguns elementos do email para que você conheça a melhor maneira de usar esses recursos.

### <a name="subject"></a>Subject
O assunto do email segue o seguinte padrão: você foi convidado para a organização &lt;nomedolocatário&gt;

### <a name="from-address"></a>Do endereço
Usamos um padrão parecido com o do LinkedIn para o endereço De. Nossa meta é deixar claro quem é o emissor do convite e de qual empresa, e também esclarecer que o email é proveniente de um endereço de email da Microsoft. O formato é: &lt;Nome de exibição do emissor do convite&gt; de &lt;nome do locatário&gt; (via Microsoft) <invites@microsoft.com&gt;

### <a name="reply-to"></a>Responder Para
O email para resposta é definido como o email do emissor do convite, quando houver um disponível, para que a resposta envie um email ao emissor do convite.

### <a name="branding"></a>Identidade visual
Os emails de convite de seu locatário usam a identidade visual da empresa configurada para o locatário. Se você quiser aproveitar isso, estes são os detalhes da configuração. O logotipo do banner aparecerá no email. Siga as instruções de tamanho e qualidade da imagem para obter os melhores resultados. Além disso, o nome da empresa também aparece no plano de ação.

### <a name="call-to-action"></a>Plano de ação
O plano de ação é composto por duas partes: explicação do motivo de o destinatário ter recebido o email, e o que o destinatário deve fazer a respeito.
- A seção "motivo" pode ter o seguinte padrão: Você foi convidado(a) para acessar aplicativos na organização &lt;nomedolocatário&gt;

- E a seção "o que você deve fazer" é indicada pela presença do botão **Introdução**. Quando o destinatário tiver sido adicionado sem a necessidade de convites, esse botão não aparecerá.

### <a name="inviters-information"></a>Informações sobre o emissor do convite
O nome de exibição do emissor do convite será incluído no email. E, além disso, se você tiver configurado uma foto de perfil para sua conta do Azure AD, o email do convite também incluirá a imagem. Isso serve para aumentar a confiança do destinatário do email.
Se o emissor do convite ainda não tiver configurado uma imagem de perfil, o Azure AD criará um ícone com as iniciais do emissor do convite no lugar da imagem, conforme mostra a imagem:

  ![exibição das iniciais do emissor do convite](media/active-directory-b2b-invitation-email/inviters-initials.png)

### <a name="body"></a>Corpo
O corpo conterá a mensagem digitada pelo emissor do convite no UX ou passada na API do convite. Ele é formado apenas por uma caixa de texto simples que não processará marcas de html por motivos de segurança.

### <a name="footer-section"></a>Seção de rodapé

O rodapé contém a marca da empresa Microsoft e informará ao destinatário se o email foi enviado de um alias não monitorado. Casos especiais:

- O emissor do convite não tem um endereço de email no locatário que está convidando

  ![imagem do emissor do convite não tem um endereço de email no locatário que está convidando](media/active-directory-b2b-invitation-email/inviter-no-email.png)


- O destinatário não precisa resgatar o convite

  ![quando o destinatário não precisa resgatar o convite](media/active-directory-b2b-invitation-email/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Licenciamento da colaboração B2B do Azure AD](active-directory-b2b-licensing.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)



<!--HONumber=Feb17_HO1-->


