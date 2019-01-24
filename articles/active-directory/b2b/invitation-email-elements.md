---
title: Os elementos do email de convite para colaboração B2B – Azure Active Directory | Microsoft Docs
description: Modelo de email de convite para colaboração B2B do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: ede9cb7638ccc2c46c265d42ad4c8cb652b8638c
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432295"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Os elementos do email de convite para colaboração B2B – Azure Active Directory

Emails de convite são um componente essencial para ingressar parceiros como usuários de colaboração B2B no Azure AD. Você pode usá-los para aumentar a confiança do destinatário. Você pode adicionar a legitimidade e a comprovação social ao email, para certificar-se de que o destinatário sinta-se confortável com a seleção do botão **Introdução** para aceitar o convite. Essa confiança é uma maneira importante de reduzir o atrito de compartilhamento. E o email fica ótimo dessa maneira!

![Email de convite de B2b do AD do Azure](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Explicação do email
Vamos examinar alguns elementos do email para que você conheça a melhor maneira de usar esses recursos.

### <a name="subject"></a>Assunto
O assunto do email segue o seguinte padrão: Você foi convidado para a organização &lt;tenantname&gt;

### <a name="from-address"></a>Do endereço
Usamos um padrão parecido com o do LinkedIn para o endereço De.  Você deve deixar claro quem é o emissor do convite e de qual empresa, e também esclarecer que o email é proveniente de um endereço de email da Microsoft. O formato é: &lt;Nome de exibição do emissor do convite&gt; de &lt;tenantname&gt; (via Microsoft) <invites@microsoft.com>

### <a name="reply-to"></a>Responder Para
O email para resposta é definido como o email do emissor do convite, quando houver um disponível, para que a resposta envie um email ao emissor do convite.

### <a name="branding"></a>Identidade visual
Os emails de convite de seu locatário usam a identidade visual da empresa configurada para o locatário. Se você quiser aproveitar esse recurso, [estes](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) são os detalhes da configuração. O logotipo da faixa é exibido no email. Siga as instruções de tamanho e qualidade da imagem [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) para obter os melhores resultados. Além disso, o nome da empresa também aparece no plano de ação.

### <a name="call-to-action"></a>Plano de ação
O plano de ação é composto por duas partes: explicação do motivo de o destinatário ter recebido o email, e o que o destinatário deve fazer a respeito.
- A seção "porquê" pode ser resolvida usando o seguinte padrão: Você foi convidado para acessar aplicativos na organização &lt;tentname&gt;

- E a seção "o que você deve fazer" é indicada pela presença do botão **Introdução**. Quando o destinatário tiver sido adicionado sem a necessidade de convites, esse botão não aparecerá.

### <a name="inviters-information"></a>Informações sobre o emissor do convite
O nome de exibição do emissor do convite está incluído no email. E, além disso, se você tiver configurado uma foto de perfil para sua conta do Azure AD, o email do convite também incluirá a imagem. Ambos servem para aumentar a confiança do destinatário do email.

Se você ainda não tiver configurado uma imagem de perfil, um ícone com as iniciais do emissor do convite no lugar da imagem, conforme mostra a imagem:

  ![exibição das iniciais do emissor do convite](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Corpo
O corpo contém a mensagem que o emissor do convite compõe ao [convidar um usuário convidado para o diretório, grupo ou aplicativo](add-users-administrator.md) ou [usando a API de convite](customize-invitation-api.md). Como é uma área de texto, ela não processa marcas HTML por motivos de segurança.

### <a name="footer-section"></a>Seção de rodapé
O rodapé contém a marca da empresa Microsoft e informará ao destinatário se o email foi enviado de um alias não monitorado. Casos especiais:

- O emissor do convite não tem um endereço de email no locatário que está convidando

  ![imagem do emissor do convite não tem um endereço de email no locatário que está convidando](media/invitation-email-elements/inviter-no-email.png)


- O destinatário não precisa resgatar o convite

  ![quando o destinatário não precisa resgatar o convite](media/invitation-email-elements/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do Azure AD](what-is-b2b.md)
- [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](add-users-administrator.md)
- [Como os operadores de informação adicionam usuários de colaboração B2B?](add-users-information-worker.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Adicionar usuários de colaboração B2B sem um convite](add-user-without-invite.md)
