---
title: Solucionar problemas de verificação em duas etapas - Azure AD | Microsoft Docs
description: Fornece instruções para os usuários sobre o que fazer se ele tiver um problema com autenticação multifator do Azure e a verificação em duas etapas.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: lizross
ms.reviewer: kexia
ms.openlocfilehash: ec79adcaf7cbef778b8c9e572ae932d5ceb47aa6
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090370"
---
# <a name="get-help-with-two-step-verification"></a>Obter ajuda com a verificação em duas etapas

A verificação em duas etapas é um recurso de segurança que sua organização decidiu usar para proteger suas contas. Verificação em duas etapas é mais segura do que apenas uma senha, porque se baseia em duas formas de autenticação: algo que você sabe e algo que você tem com você. Algo que você sabe é a sua senha, enquanto algo que você tem com você é o seu telefone ou um dispositivo. Usar a verificação em duas etapas pode ajudar a parar de hackers mal-intencionados de entrar como você, mesmo se ele obtiver sua senha.

Embora a Microsoft oferece verificação em duas etapas, é sua organização que decide se você usar o recurso. Você não poderá recusar se a organização exigir isso, assim como você não pode optar por não usar uma senha para proteger sua conta.

>[!Note]
>Se você estiver procurando para obter mais informações sobre como usar a verificação em duas etapas com sua conta pessoal da Microsoft, consulte o artigo [sobre a verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="why-do-i-need-to-use-another-verification-method"></a>Por que eu preciso usar outro método de verificação?

Há vários motivos por que talvez você precise usar um método de verificação alternativo com sua conta. Por exemplo: 

- **Você esqueceu seu telefone ou dispositivo.** Em alguns dias, você deixa seu telefone em casa, mas ainda precisa entrar no trabalho. Primeiro, você deve tentar entrar usando um método diferente que não precise de seu telefone.

- **Você perdeu seu telefone ou conseguiu um novo número de telefone.** Se você tiver perdido seu telefone ou conseguiu um novo número, você pode entrar usando um método diferente ou solicitar ao administrador para limpar as suas configurações. É altamente recomendável informar o administrador se o seu telefone foi perdido ou roubado, assim, as atualizações adequadas podem ser feitas para sua conta. Após suas configurações serem limpas,você será solicitado a [registrar-se para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) na próxima vez que você entrar.

- **Não obtendo o texto de autenticação ou chamada telefônica.** Há vários motivos por que talvez não obtenha o texto ou chamada telefônica. Se você já recebeu com êxito mensagens ou chamadas telefônicas antes, então, este é provavelmente um problema com o provedor de telefone, não sua conta. Se você geralmente tem atrasos na recepção devido a um sinal de celular ruim, recomendamos que você use o [aplicativo Microsoft Authenticator](microsoft-authenticator-app-how-to.md) em seu celular. Este aplicativo pode gerar códigos de segurança aleatórios para entrar, sem a necessidade de qualquer sinal de celular ou conexão de Internet.<br><br>Se você estiver tentando receber uma mensagem de texto, peça a um amigo para enviar para você como um teste para garantir que você pode obter um e se você tiver chegado várias mensagens, verifique se você estiver usando o código da mais atual.

- **As suas senhas de aplicativo não estão funcionando.** A senha de aplicativo substitui a senha normal, para aplicativos de área de trabalho mais antigos que não dão suporte à verificação em duas etapas. Primeiro, verifique se você digitou a senha corretamente. Se isso não resolver, tente entrar no [criar uma nova senha de aplicativo](multi-factor-authentication-end-user-app-passwords.md) ou entrar em contato com seu administrador para [exclua suas senhas de aplicativo existentes](../authentication/howto-mfa-userdevicesettings.md) para que você possa criar um novo.

## <a name="sign-in-using-another-verification-method"></a>Conecte-se usando outro método de verificação

1. Entre em sua conta normalmente e escolha o **entre de outra forma** no link a página **verificação em duas etapas**.

    ![Alterar o método de verificação de entrada](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Se você não vir o link **entre de outra forma**, isso significa que você não configurou quaisquer outros métodos de verificação. Você terá de entrar em contato com seu administrador para obter ajuda entrar em sua conta. Depois de entrar, certifique-se de adicionar mais métodos de verificação. Para obter mais informações sobre como adicionar métodos de verificação, consulte o artigo [gerenciar as configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md).<br><br>Se você vir o link, mas ainda não vir quaisquer outros métodos de verificação, você terá que entrar em contato com seu administrador para a Ajuda para entrar na sua conta.

2. Escolha seu método de verificação alternativo e continuar o processo de verificação em duas etapas.

3. Depois que você está em sua conta, você pode atualizar seus métodos de verificação (se necessário). Para obter mais informações sobre como adicionar ou mudar métodos, consulte o artigo [gerenciar as configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md).

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Não encontrei uma resposta para o meu problema

Se você tiver tentado essas etapas, mas ainda estiver encontrando problemas, entre em contato com o seu administrador para mais ajuda.

## <a name="related-topics"></a>Tópicos relacionados

* [Gerenciar as configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)

* [Perguntas frequentes sobre o aplicativo Microsoft Authenticator](microsoft-authenticator-app-faq.md)
