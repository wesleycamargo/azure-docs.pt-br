---
title: "Início rápido: Azure AD SSPR | Microsoft Docs"
description: "Implantar rapidamente a redefinição da senha de autoatendimento do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 7d97fad04a0aa549d0e3a182282f898302e8e41a
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2017
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Implantação rápida da redefinição da senha de autoatendimento do Azure AD

> [!IMPORTANT]
> **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md).

A redefinição da senha de autoatendimento (SSPR) oferece um meio simples para os administradores de TI a capacitar os usuários a redefinir ou desbloquear suas contas ou senhas. O sistema inclui relatórios detalhados para acompanhar quando os usuários utilizam o sistema junto com as notificações para alertá-lo para uso indevido ou um abuso.

Este guia pressupõe que você já tem um locatário do Azure AD de trabalho licenciado ou de avaliação. Se precisar de ajuda para configurar o Azure AD, confira o artigo [Introdução ao Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Habilitar SSPR para seu locatário do Azure AD

1. No seu locatário existente do Azure AD, selecione **"Redefinir senha"**

2. A partir da tela **"Propriedades"**, na opção "Redefinição da senha de autoatendimento habilitada" escolha uma das seguintes opções:
    * Ninguém - Não é possível usar a funcionalidade de SSPR.
    * Selecionado - Somente os membros de um grupo do Azure AD específico escolhido são capazes de usar a funcionalidade de SSPR. É recomendável definir um grupo de usuários e usar essa configuração ao implantá-la para uma prova de conceito.
    * Todos - Todos os usuários com contas no seu locatário do Azure AD são capazes de usar a funcionalidade de SSPR. É recomendável que isso seja definido quando você estiver pronto para implantar essa funcionalidade em todo o seu locatário depois de concluir uma prova de conceito.

3. Na tela **"Métodos de autenticação"** escolha
    * Número de métodos necessários para redefinir - oferecemos suporte a um mínimo de um ou um máximo de dois
    * Métodos disponíveis para os usuários - é necessário pelo menos um, mas é aconselhável ter uma opção extra disponível
        * **Email** envia um email com um código para o email de autenticação configurado do usuário
        * **Telefone celular** dá ao usuário a opção de receber uma chamada ou um texto com um código para seu número de celular configurado
        * **Telefone comercial** chama o usuário com um código para o número do telefone comercial configurado
        * **Perguntas de segurança** exige que você escolha
            * Número de perguntas obrigatórias para registrar - o requisito mínimo para um registro bem-sucedido, o que significa que um usuário pode optar por responder a mais para criar um pool de perguntas a serem usadas. Essa opção pode ser definida de 3 a 5 e deve ser maior ou igual ao número de perguntas obrigatórias para redefinir.
                * Perguntas personalizadas podem ser adicionadas clicando no botão "Personalizar" ao selecionar perguntas de segurança
            * Número de perguntas necessárias para a redefinição -pode ser definido de 3 a 5 perguntas a serem respondidas corretamente antes de permitir que uma senha de usuários seja redefinida ou desbloqueada.
            
    ![Autenticação][Authentication]

4. RECOMENDADO: **"Personalização"** permite que você altere o link "Entre em contato com seu administrador" para apontar para uma página ou endereço de email que você definir. É recomendável definir esse link para algo como um endereço de email ou site aos quais os usuários estão acostumados para uso para obter suporte.

5. OPCIONAL: a tela **"Registro"** fornece aos administradores as opções para:
    * Exigir que os usuários se registrem ao entrar
    * Número de dias antes que os usuários precisem reconfirmar suas informações de autenticação

6. OPCIONAL: a tela **"Notificação"** fornece aos administradores as opções de:
    * Notificar os usuários sobre as redefinições de senha
    * Notificar todos os administradores quando outros administradores redefinirem suas próprias senhas

**Neste ponto, você configurou a SSPR para seu locatário do Azure AD**. Os usuários agora podem usar as instruções encontradas nos artigos [Registrar para redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md) e [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md) para atualizar a senha sem a intervenção do administrador. Você pode parar por aqui se só utiliza a nuvem ou continuar a configurar a sincronização de senhas para um domínio do AD local.

> [!IMPORTANT]
> Teste a SSPR com um usuário e não como um administrador, uma vez que a Microsoft impõe requisitos de autenticação fortes para contas de administrador do Azure. Para obter mais informações sobre a política de senha do administrador, confira o nosso [artigo de política de senha](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-existing-identity-source"></a>Como configurar a sincronização com fonte de identidade existentes

Para habilitar a sincronização de identidades local ao Azure AD, você precisa instalar e configurar o [Azure AD Connect](./connect/active-directory-aadconnect.md) em um servidor na sua organização. Esse aplicativo lida com a sincronização de usuários e grupos da fonte de identidade existentes para seu locatário do Azure AD.

* [Como atualizar do DirSync ou sincronizar o Azure AD com o Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Introdução ao Azure AD Connect usando configurações expressas](./connect/active-directory-aadconnect-get-started-express.md)
* [Configurar o write-back de senha](active-directory-passwords-writeback.md#configuring-password-writeback) para gravar senhas do Azure AD de volta ao seu diretório local.

### <a name="on-premises-policy-change"></a>Alteração da política local

Se você estiver sincronizando usuários de um domínio do Active Directory local e deseja permitir que os usuários redefinam suas senhas imediatamente, faça a seguinte alteração em sua política de senha local:

**Configuração do computador** > **Políticas** > **Configurações do Windows** > **Configurações de segurança**  >  **Políticas de conta** > **Política de senha**

**Duração mínima da senha** - 0 dias

Essa configuração de segurança determina o período de tempo (em dias) que uma senha deve ser usada antes que o usuário possa alterá-la. Definindo-a como **0 dias** permite que os usuários usem SSPR se suas senhas são alteradas por suas respectivas equipes de suporte.

![Política][Policy]

## <a name="disabling-self-service-password-reset"></a>Desabilitação da redefinição da senha de autoatendimento

Desabilitar a redefinição de senha de autoatendimento é tão simples quanto abrir seu locatário do Azure AD e acessar **Redefinição de Senha > Propriedades** e escolher **Nenhuma** em **Redefinição da Senha de Autoatendimento Habilitada**

### <a name="learn-more"></a>Saiba mais
Os links a seguir fornecem informações adicionais sobre a redefinição de senha usando o Azure AD

* [Como concluir uma implementação de SSPR com sucesso?](active-directory-passwords-best-practices.md)
* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md).
* [Registro para redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md).
* [Você tem uma pergunta sobre licenciamento?](active-directory-passwords-licensing.md)
* [Quais dados são usados pela SSPR e quais dados você deve preencher para seus usuários?](active-directory-passwords-data.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política com SSPR?](active-directory-passwords-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](active-directory-passwords-writeback.md)
* [Como faço para informar sobre a atividade na SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que elas significam?](active-directory-passwords-how-it-works.md)
* [Acho que algo está errado. Como faço para solucionar o problema na SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a configurar a redefinição de senha por autoatendimento para seus usuários. Para continuar no Portal do Azure a fim de concluir estas etapas, siga o link abaixo até o portal.

> [!div class="nextstepaction"]
> [Habilitar a redefinição de senha por autoatendimento](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/active-directory-passwords-getting-started/sspr-authentication-methods.png "Métodos de autenticação do Azure AD e quantidade necessária"
[Policy]: ./media/active-directory-passwords-getting-started/password-policy.png "Política de grupo de senha local definida para 0 dias"
