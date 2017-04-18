---
title: "Azure AD: registro de redefinição de senha de autoatendimento | Microsoft Docs"
description: "Registrar dados de autenticação para redefinição de senha de autoatendimento"
services: active-directory
keywords: "Gerenciamento de senha do Active Directory, gerenciamento de senha, redefinição de senha de autoatendimento do Azure AD, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/16/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 0f10ad4904b10a9554e949e77f0192edcb8f624f
ms.lasthandoff: 04/12/2017


---
# <a name="register-for-self-service-password-reset"></a>Registro de redefinição de senha de autoatendimento

Como um usuário final, se o administrador tiver habilitado essa opção, você poderá redefinir sua senha ou desbloquear sua conta sem precisar falar com uma pessoa usando a SSPR (redefinição de senha de autoatendimento). Antes de usar essa funcionalidade, você precisa registrar métodos de autenticação ou confirmar os métodos de autenticação predefinidos que administrador preencheu.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrar ou confirmar dados de autenticação com SSPR

1. Abra o navegador da web em seu dispositivo e vá para o [página de registro de redefinição de senha](http://aka.ms/ssprsetup)
2. Insira seu nome de usuário e senha fornecidos pelo administrador
3. Dependendo das opções que o administrador aprovou, você verá um ou mais itens a seguir para configurar ou verificar e ser usados se você precisar redefinir sua senha
    * Telefone do escritório - esta opção só é capaz de ser definida pelo administrador
    * Autenticação de telefone - esta opção deve ser definida como outro número de telefone que você pode acessar, como um telefone celular que possa receber uma chamada (o administrador pode preencher isso para você com o número do telefone celular se ele tiver permissão para usar essas informações)
    * Email de autenticação - esta opção deve ser definida como um endereço de email alternativo que você pode acessar sem a senha que será redefinida
    * Perguntas de segurança - esta opção fornece uma lista de perguntas aprovadas pelo administrador para que você responda. Você não pode usar a mesma resposta para mais de uma pergunta.
4. Forneça e verifique as informações exigidas pelo seu administrador. Se houver mais de uma opção, sugerimos que você registre vários métodos para fornecer flexibilidade quando outro método não estiver disponível (exemplo: viajando e sem poder acessar seu telefone comercial)

    ![Registre os métodos de autenticação e clique em Concluir][Register]

5. Quando concluir a etapa 4, escolha **Concluir** e será capaz de usar a redefinição de senha de autoatendimento quando for necessário.

Se você inserir dados no telefone ou email de autenticação, eles não ficarão visíveis no diretório global. As únicas pessoas que podem ver esses dados são você e seus administradores. Só você pode ver as respostas para suas perguntas de segurança.

Os administradores podem exigir que você confirme os métodos de autenticação após um período de tempo para confirmar que os métodos apropriados estão registrados.

## <a name="next-steps"></a>Próximas etapas

* [Como alterar a senha usando a redefinição de senha de autoatendimento](active-directory-passwords-update-your-own-password.md)
* [Página de registro de redefinição de senha](http://aka.ms/ssprsetup)
* [Portal de redefinição de senha:](https://passwordreset.microsoftonline.com/)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Página de registro de redefinição de senha mostrando métodos registrados e botão Concluir"

