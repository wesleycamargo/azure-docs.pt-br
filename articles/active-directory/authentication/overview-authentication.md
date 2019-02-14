---
title: Autenticação de usuário do Azure Active Directory
description: Como administrador do Azure AD, como proteger a autenticação de usuário, reduzindo o impacto no usuário final?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b054dd8c78968ffb65b92ab7ee4fe1b21501b0d3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192500"
---
# <a name="what-methods-are-available-for-authentication"></a>Quais métodos estão disponíveis para autenticação?

Ouvimos relatórios nos noticiários, senhas roubadas e identidades sendo comprometidas. Exigir um segundo fator além de uma senha imediatamente aumenta a segurança da sua organização. O Microsoft Azure Active Directory (Azure AD) inclui recursos, como a Autenticação Multifator do Microsoft Azure (Azure MFA) e a redefinição de senha de autoatendimento do Azure AD (SSPR), para ajudar os administradores a proteger as organizações e os usuários com métodos de autenticação adicionais.

Há muitos cenários que incluem conexão com um aplicativo, redefinição da senha, habilitação do Windows Hello e outros, nos quais os usuários poderão precisar fornecer verificação adicional sobre suas identidades.

Uma verificação adicional pode vir na forma de métodos de autenticação, como:

* Um código fornecido em uma mensagem de email ou de texto
* Uma chamada telefônica
* Uma notificação ou um código em seu telefone
* Respostas para perguntas de segurança

![Página de logon do exemplo login.microsoftonline.com no Chrome](media/overview-authentication/overview-login.png)

A redefinição de senha de autoatendimento do Azure MFA e o Azure AD oferece aos administradores controle sobre configuração, política, monitoramento e emissão de relatórios usando o Azure AD e o portal do Azure para proteger suas organizações.

## <a name="self-service-password-reset"></a>Redefinição de senha de autoatendimento

A redefinição de senha de autoatendimento fornece aos usuários a capacidade de redefinir sua senha, sem nenhuma intervenção do administrador, quando e onde eles precisam.

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

A redefinição de senha por autoatendimento inclui:

* **Alteração de senha:** Sei minha senha, mas desejo alterá-la para uma nova.
* **Redefinição de senha:** Não consigo entrar e desejo redefinir minha senha usando um ou mais métodos de autenticação aprovados.
* **Desbloqueio de conta:** Não consigo entrar porque minha conta está bloqueada e desejo desbloqueá-la usando um ou mais métodos de autenticação aprovados.

## <a name="multi-factor-authentication"></a>Autenticação Multifator

A Autenticação Multifator do Azure é uma solução de verificação em duas etapas da Microsoft. Usando métodos de autenticação aprovados pelo administrador, o Azure MFA ajuda a proteger o acesso a dados e a aplicativos, ao mesmo tempo que atende à demanda por um processo de logon simples.

## <a name="next-steps"></a>Próximas etapas

A próxima etapa é mergulhar e configurar a redefinição de senha de autoatendimento e a Autenticação Multifator do Azure.

Para se familiarizar com a redefinição de senha de autoatendimento, consulte o [artigo de início rápido para habilitar o SSPR](quickstart-sspr.md).

Saiba mais sobre a redefinição de senha self-service no artigo [Como funciona: Redefinição de senha self-service do Azure AD](concept-sspr-howitworks.md)

Saiba mais sobre a Autenticação Multifator do Azure no artigo [Como funciona: Autenticação Multifator do Azure](concept-mfa-howitworks.md)
