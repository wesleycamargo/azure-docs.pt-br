---
title: Gerenciar suas informações de segurança - Active Directory do Azure | Microsoft Docs
description: Saiba como gerenciar suas informações de segurança, incluindo como trabalhar com suas configurações de verificação em duas etapas.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 4cd88490118343a9a00dbd919d7820f0334df92e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016139"
---
# <a name="manage-your-security-info-preview"></a>Gerenciar suas informações de segurança (visualização)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Você pode usar suas informações de segurança para entrar sua conta corporativa ou de estudante ou redefinir sua senha.

Quando você entrar, dependendo das configurações da sua organização, você poderá ver uma caixa de seleção que diz **não perguntar novamente durante X dias**. Essa caixa de seleção permite que você permaneça conectado ao seu dispositivo pelo número de dias que o administrador permite, sem necessidade de reverificação.

## <a name="change-your-info"></a>Alterar suas informações
Você pode atualizar ou adicionar informações de segurança ou alterar seu padrão, com base no que é permitido pelo seu administrador e a sua organização.

### <a name="to-change-your-info"></a>Para alterar suas informações

1. Entre sua conta corporativa ou de estudante.

2. Vá para myapps.microsoft.com, selecione seu nome no canto superior direito da página e selecione **Profile**.

3. No **Gerenciar conta** área, selecione **editar informações de segurança**.

    ![Tela de perfil com o link de informações de segurança de edição realçado](media/security-info/security-info-profile.png)

4. Use seu método padrão para aprovar o acesso e ver seus detalhes de informações de segurança atuais, caso seu administrador tenha configurado essa experiência para sua organização.

5. Na página **Manter sua conta segura**, você pode:

    - Selecione **adicionar informações de segurança** para adicionar métodos adicionais.

    - Selecione **alterar padrão** para alterar o método padrão.

    - Selecione o **Lápis** ícone ao lado de um método existente para atualizar suas informações.

    ![Tela de informações de segurança com as informações existentes, editável](media/security-info/security-info-edit.png)

6. Depois de fazer suas alterações, você pode sair da página e as alterações serão salvas.

Se você não vir essas opções, ou você não consegue acessar a página myapps.microsoft.com, é possível que sua organização usa as opções personalizadas ou uma página personalizada. Você precisará entrar em contato com seu administrador para obter mais ajuda.

## <a name="manage-your-security-info-for-a-lost-or-potentially-compromised-device"></a>Gerenciar suas informações de segurança para um dispositivo perdido ou potencialmente comprometido

Se você perder o dispositivo ou o dispositivo ficar comprometido, será necessário refazer o processo de verificação de todos os seus dispositivos confiáveis anteriormente.

### <a name="to-manage-your-security-info-for-lost-or-potentially-compromised-devices"></a>Para gerenciar suas informações de segurança para dispositivos perdidos ou potencialmente comprometidos

1. Entre sua conta corporativa ou de estudante.

2. Vá para myapps.microsoft.com, selecione seu nome no canto superior direito da página e selecione **Profile**.

3. No **Gerenciar conta** área, selecione **esquecer MFA em dispositivos lembrados**.
    
    A escolha desta opção significa que você terá que passar pelo processo de Autenticação Multifator (MFA) novamente depois de entrar.

    ![Tela de perfil com o link de esquecer realçado](media/security-info/security-info-forget.png)

## <a name="common-problems-and-solutions-with-your-security-info"></a>Problemas comuns e soluções com suas informações de segurança

Este artigo ajuda você a solucionar suas informações de segurança, incluindo problemas relacionados à verificação de duas etapas.

|Problema|Solução|
|-------|--------|
|Eu não tenho meu telefone comigo|É possível que você não tenha seu telefone com você o tempo todo, mas que ainda deseje fazer login em sua conta do trabalho ou da escola. Para corrigir esse problema, é possível fazer login usando um método de autenticação diferente que não exija seu telefone, como o número de telefone do escritório. Para adicionar métodos adicionais às suas informações de segurança, siga as etapas na seção [Alterar suas informações](#change-your-info).|
|Perdi meu telefone ou ele foi roubado|Infelizmente, perder seu telefone ou ele seja roubada pode acontecer. Nessa situação, é altamente recomendável que você informe sua organização para que sua equipe de TI pode redefinir suas senhas de aplicativo e limpe todos os dispositivos memorizados da sua lista de dispositivos confiáveis. Você também pode esquecer seus próprios dispositivos confiáveis seguindo as etapas na seção [Gerenciar informações de segurança para um dispositivo perdido ou potencialmente comprometido](#manage-your-security-info-for-a-lost-or-potentially-compromised-device).|
|Eu tenho um novo número de telefone|Há duas maneiras de corrigir esse problema. Você pode entrar usando um método de autenticação alternativo que não exija seu número de telefone, como e-mail, ou, se isso não for uma opção, você pode entrar em contato com a equipe de TI da sua organização e fazer com que ela limpe suas configurações. Para adicionar métodos adicionais às suas informações de segurança, siga as etapas na seção [Alterar suas informações](#change-your-info).|
|Meu método padrão está errado|Você pode atualizar seu método de padrão em suas opções de segurança. Para detalhes específicos, você pode ir para a seção [Alterar sua informação](#change-your-info).|
|Eu não estou recebendo um texto ou liguei no meu dispositivo móvel|Se você recebeu mensagens de texto ou telefonemas com sucesso no seu dispositivo móvel no passado, esse problema é mais provável com o provedor do telefone, não com sua conta. Certifique-se de ter um bom sinal de celular e de receber mensagens de texto e chamadas telefônicas. Você pode pedir a um amigo para ligar ou enviar uma mensagem de texto como teste.<br><br>Se você conseguir receber mensagens de texto e telefone, mas ainda não recebeu a notificação, tente usar um método diferente. Você pode adicionar métodos adicionais às suas informações de segurança seguindo as etapas na seção [Alterar suas informações](#change-your-info). Se você não tiver outro método para adicionar, poderá entrar em contato com o suporte da sua empresa e solicitar que ele limpe suas configurações para que você possa configurar seus métodos na próxima vez que fizer login.<br><br>Se você costuma ter problemas por causa da má recepção da célula, recomendamos usar o aplicativo Microsoft Authenticator no seu dispositivo móvel. O aplicativo pode gerar códigos de segurança aleatórios que você usa para entrar, e esses códigos não exigem qualquer conexão de internet ou um sinal de celular. Para obter mais informações sobre o aplicativo Microsoft Authenticator, consulte o artigo [Introdução ao Microsoft Authenticator app](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to).|
|Nenhuma das opções nesta tabela resolveu meu problema|Se você já tentou essas etapas de solução de problemas, mas ainda está com problemas; Entre em contato com o suporte da sua empresa, eles devem ser capazes de ajudá-lo.|

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as informações de segurança na [visão geral de informações de segurança (visualização)](user-help-security-info-overview.md).

- Saiba mais sobre a verificação em duas etapas no artigo [Visão geral da verificação em duas etapas](user-help-two-step-verification-overview.md). 

- Siga um desses artigos de instruções para aprender sobre como configurar seus dispositivos na área de informações de segurança:

    - [Configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md)

    - [Configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md)

    - [Configurar informações de segurança para usar mensagens de texto](security-info-setup-text-msg.md)

    - [Configurar informações de segurança para usar email](security-info-setup-email.md)

    - [Configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md)

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](user-help-reset-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.