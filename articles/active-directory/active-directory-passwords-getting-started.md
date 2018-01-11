---
title: "Início rápido: Azure AD SSPR | Microsoft Docs"
description: "Implantar rapidamente a redefinição da senha de autoatendimento do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/22/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 87e0d96c3bd0e9a488734487b5f39503c28f9b00
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2017
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Implantação rápida da redefinição da senha de autoatendimento do Azure AD

> [!IMPORTANT]
> **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, consulte [Preciso de ajuda, esqueci minha senha do Azure AD](active-directory-passwords-update-your-own-password.md).

A redefinição da senha de autoatendimento (SSPR) oferece um meio simples para os administradores de TI a capacitar os usuários a redefinir ou desbloquear suas contas ou senhas. O sistema inclui relatórios detalhados que acompanham quando os usuários acessam o sistema junto com as notificações para alertá-lo para uso indevido ou um abuso.

Este guia pressupõe que você já tem um locatário do Azure Active Directory (Azure AD) de trabalho licenciado ou de avaliação. Se precisar de ajuda para configurar o Azure AD, consulte [Introdução ao Azure AD](get-started-azure-ad.md).

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Habilitar SSPR para seu locatário do Azure AD

1. No seu locatário existente do Azure AD, selecione **Redefinir senha**.

2. Na página **Propriedades**, na opção **Redefinição da senha de autoatendimento habilitada**, escolha uma das seguintes opções:
   * **Ninguém**: Ninguém pode usar a funcionalidade de SSPR.
   * **Selecionado**: Somente os membros de um grupo específico do Azure AD escolhido podem usar a funcionalidade de SSPR. É recomendável definir um grupo de usuários e usar essa configuração ao implantar essa funcionalidade para uma prova de conceito.
   * **Todos**: Todos os usuários com contas no seu locatário do Azure AD podem usar a funcionalidade de SSPR. É recomendável usar essa configuração quando você estiver pronto para implantar essa funcionalidade em todo o seu locatário depois de concluir uma prova de conceito.

   > [!IMPORTANT]
   > Contas de Administrador do Azure sempre terão a capacidade de redefinir suas senhas, não importando o modo como essa opção é definida. 

3. Na página **Métodos de autenticação**, escolha o seguinte:
   * **Número de métodos necessários para redefinir**: Oferecemos suporte a um mínimo de um ou um máximo de dois.
   * **Métodos disponíveis para os usuários**: É necessário pelo menos um, mas é aconselhável ter uma opção extra disponível.
      * **Email**: Envia um email com um código para o endereço de email de autenticação configurado do usuário.
      * **Telefone celular**: Dá ao usuário a opção de receber uma chamada ou uma mensagem de texto com um código para o seu número de telefone celular configurado.
      * **Telefone comercial**: Faz uma chamada para o usuário com um código para o número de telefone comercial configurado.
      * **Perguntas de segurança**: Exige que você escolha:
         * **Número de perguntas obrigatórias para registrar**: O mínimo para um registro bem-sucedido. Um usuário pode optar por responder mais perguntas para criar um pool de perguntas para efetuar pull. Essa opção pode ser definida para três a cinco perguntas e deve ser maior ou igual ao número de perguntas obrigatórias para redefinir a senha. O usuário pode adicionar perguntas personalizadas se eles selecionarem o botão **Personalizado** quando eles selecionam as perguntas de segurança.
         * **Número de perguntas necessárias para a redefinição**: Podem ser definidas de três a cinco perguntas a serem respondidas corretamente antes de permitir que uma senha de usuário seja redefinida ou desbloqueada.
            
    ![Autenticação][Authentication]

4. Recomendado: Em **Personalização**, você pode alterar o link **Entre em contato com seu administrador** para apontar para um email ou página que você definir. É recomendável definir esse link para algo como um endereço de email ou site os quais os usuários já estão acostumados a usar para obter suporte.

5. Opcional: A página **Registro** fornece aos administradores as opções para:
   * Exigir que os usuários se registrem ao entrar.
   * Definir o número de dias antes que os usuários precisem reconfirmar suas informações de autenticação.

6. Opcional: A página **Notificações** fornece aos administradores as opções para:
   * Notificar os usuários sobre as redefinições de senha.
   * Notificar todos os administradores quando outros administradores redefinirem suas próprias senhas.

Neste ponto, você configurou a SSPR para o seu locatário do Azure AD. Os usuários agora podem usar as instruções encontradas nos artigos [Registrar para redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md) e [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md) para atualizar a senha sem a intervenção do administrador. Você pode parar aqui se usa apenas a nuvem. Ou você pode continuar para a próxima seção para configurar a sincronização de senhas para um domínio do Active Directory local.

> [!TIP]
> Teste a SSPR com um usuário e não como um administrador, pois a Microsoft impõe requisitos de autenticação fortes para contas de administrador do Azure. Para obter mais informações sobre a política de senha do administrador, confira o nosso artigo de [política de senha](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-an-existing-identity-source"></a>Como configurar a sincronização com uma fonte de identidade existente

Para habilitar a sincronização de identidades local ao Azure AD, você precisa instalar e configurar o [Azure AD Connect](./connect/active-directory-aadconnect.md) em um servidor na sua organização. Esse aplicativo lida com a sincronização de usuários e grupos da fonte de identidade existente para o seu locatário do Azure AD. Para obter mais informações, confira:

* [Como atualizar do DirSync ou sincronizar o Azure AD com o Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Introdução ao Azure AD Connect usando configurações expressas](./connect/active-directory-aadconnect-get-started-express.md)
* [Configurar o write-back de senha](active-directory-passwords-writeback.md#configure-password-writeback) para gravar senhas do Azure AD de volta ao seu diretório local

### <a name="on-premises-policy-change"></a>Alteração da política local

Se você sincronizar usuários a partir de um domínio do Active Directory local e deseja permitir que os usuários redefinam suas senhas imediatamente, faça a seguinte alteração em sua política de senha local:

1. Navegue até **Configuração do computador** > **Políticas** > **Configurações do Windows** > **Configurações de segurança**  >  **Políticas de conta** > **Política de senha**.

2. Definir a **Duração mínima da senha** para **0 dias**.

Essa configuração de segurança determina o período de tempo, em dias, que uma senha deve ser usada antes que o usuário possa alterá-la. Se você definir a configuração de uso mínimo como **0 dias**, os usuários podem usar a SSPR se suas senhas forem alteradas por suas respectivas equipes de suporte.

![Política][Policy]

## <a name="disable-self-service-password-reset"></a>Desabilitar a redefinição de senha por autoatendimento

É fácil desabilitar a redefinição de senha por autoatendimento. Abra o seu locatário do Azure AD e vá para **Redefinição de senha** > **Propriedades** e, em seguida, selecione **Nenhum** em **Redefinição de senha do serviço de autoatendimento habilitada**.

### <a name="learn-more"></a>Saiba mais
Os artigos a seguir fornecem informações adicionais sobre a redefinição de senha através do Azure Active Directory:

* [Como concluir uma implementação do SSPR com êxito?](active-directory-passwords-best-practices.md)
* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md)
* [Você tem uma pergunta sobre licenciamento?](active-directory-passwords-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](active-directory-passwords-data.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](active-directory-passwords-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](active-directory-passwords-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](active-directory-passwords-how-it-works.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a configurar a redefinição de senha por autoatendimento para seus usuários. Para concluir essas etapas, continue para o portal do Azure:

> [!div class="nextstepaction"]
> [Habilitar a redefinição de senha por autoatendimento](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/active-directory-passwords-getting-started/sspr-authentication-methods.png "Métodos de autenticação do Azure AD e quantidade necessária"
[Policy]: ./media/active-directory-passwords-getting-started/password-policy.png "Política de Grupo de senha local definida para 0 dias"

