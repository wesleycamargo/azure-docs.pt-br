---
title: 'Azure AD: registro SSPR | Microsoft Docs'
description: "Registrar dados de autenticação para redefinição de senha de autoatendimento do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.custom: end-user
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: bd0175558a94a146a365e8c1a32522f1e54c184e
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---
# <a name="register-for-self-service-password-reset"></a>Registro de redefinição de senha de autoatendimento

Como um usuário final, você poderá redefinir sua senha ou desbloquear sua conta sem precisar falar com uma pessoa usando a SSPR (redefinição de senha de autoatendimento). Antes de usar essa funcionalidade, você precisa registrar métodos de autenticação ou confirmar os métodos de autenticação predefinidos que administrador preencheu.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrar ou confirmar dados de autenticação com SSPR

1. Abra o navegador da web em seu dispositivo e vá para o [página de registro de redefinição de senha](http://aka.ms/ssprsetup)
2. Insira seu nome de usuário e senha fornecidos pelo administrador
3. Dependendo de como sua equipe de TI tiver configurado as coisas, uma ou mais das seguintes opções estão disponíveis para configurar e verificar. O administrador pode preencher algumas para você caso eles tenham permissão para usar as informações.
    * O telefone do escritório só é capaz de ser definida pelo administrador
    * O Telefone de Autenticação deve ser definido como outro número de telefone a que você teria acesso como um telefone celular que possa receber uma chamada ou um texto.
    * O email de autenticação deve ser definido como um endereço de email alternativo que você pode acessar sem a senha que será redefinida.
    * As perguntas de segurança fornecem uma lista de perguntas aprovadas pelo administrador para que você responda. Você não pode usar a mesma resposta para mais de uma pergunta.
4. Forneça e verifique as informações exigidas pelo seu administrador. Se houver mais de uma opção, sugerimos que você registre vários métodos para fornecer flexibilidade quando outro método não estiver disponível (exemplo: viajando e sem poder acessar seu telefone comercial)

    ![Registre os métodos de autenticação e clique em Concluir][Register]

5. Quando concluir a etapa 4, escolha **Concluir** e será capaz de usar a redefinição de senha de autoatendimento quando for necessário.

Se você inserir dados no telefone ou email de autenticação, eles não ficarão visíveis no diretório global. As únicas pessoas que podem ver esses dados são você e seus administradores. Só você pode ver as respostas para suas perguntas de segurança.

Os administradores podem exigir que você confirme os métodos de autenticação após um período de tempo para confirmar que os métodos apropriados estão registrados.

## <a name="next-steps"></a>Próximas etapas

* [Como alterar a senha usando a redefinição de senha de autoatendimento](active-directory-passwords-update-your-own-password.md)
* [Página de registro de redefinição de senha](http://aka.ms/ssprsetup)
* [Portal de redefinição de senha:](https://passwordreset.microsoftonline.com/)
* [Você não consegue acessar a sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Página de registro de redefinição de senha mostrando métodos registrados e botão Concluir"


