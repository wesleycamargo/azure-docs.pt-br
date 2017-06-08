---
title: "Início rápido: Azure AD SSPR | Microsoft Docs"
description: "Implantar rapidamente a redefinição da senha de autoatendimento do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: gahug
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/12/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 5a338029cea5f448fc4d84a1a9c01c867ecd5028
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017


---
# <a name="quick-start-azure-ad-self-service-password-reset"></a>Início rápido: redefinição da senha de autoatendimento do Azure AD

## <a name="rapidly-deploy-self-service-password-reset"></a>Implantar rapidamente a redefinição da senha de autoatendimento

A redefinição da senha de autoatendimento (SSPR) oferece um meio simples para os administradores de TI a capacitar os usuários a redefinir ou desbloquear suas contas ou senhas. O sistema inclui relatórios detalhados para acompanhar quando os usuários utilizam o sistema junto com as notificações para alertá-lo para uso indevido ou um abuso.

Este guia pressupõe que você já tem um locatário do Azure AD de trabalho licenciado ou de avaliação. Se precisar de ajuda para configurar o Azure AD, confira o artigo [Introdução ao Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).

1. No seu locatário existente do Azure AD, selecione **"Redefinir senha"**

2. A partir da tela **"Propriedades"**, na opção "Redefinição da Senha de Autoatendimento Habilitada" escolha uma das seguintes opções
    * Ninguém - não é possível usar a funcionalidade da SSPR
    * Grupo A - Somente os membros de um Azure AD específico escolhido são capazes de usar a funcionalidade da SSPR
    * Todos - Todos os usuários com contas no seu locatário do Azure AD são capazes de usar a funcionalidade da SSPR

3. Na tela **"Métodos de autenticação"** escolha
    * "Número de métodos necessário para redefinir" - Oferecemos suporte a um mínimo de um ou um máximo de dois
    * "Métodos disponíveis para os usuários" - é necessário pelo menos um, mas é aconselhável ter uma opção extra disponível
        * **Email** envia um email com um código para o email de autenticação configurado do usuário
        * **Telefone celular** dá ao usuário a opção de receber uma chamada ou um texto com um código para seu número de celular configurado
        * **Telefone comercial** chama o usuário com um código para o número do telefone comercial configurado
        * **Perguntas de segurança** exige que você escolha
            * "Número de perguntas obrigatórias para registrar" é o requisito mínimo para um registro bem-sucedido, o que significa que um usuário pode optar por responder a mais para criar um pool de perguntas a serem usadas. Essa opção pode ser definida de 3 a 5 e deve ser maior ou igual ao número de perguntas obrigatórias para redefinir.
            * "Número de perguntas necessárias para a redefinição" pode ser definido de 3 a 5 perguntas a serem respondidas corretamente antes de permitir que uma senha de usuários seja redefinida ou desbloqueada.
                * Perguntas personalizadas podem ser adicionadas clicando no botão "Personalizar" ao selecionar perguntas de segurança

4. RECOMENDADO: **"Personalização"** permite que você altere o link "Entre em contato com seu administrador" para apontar para um email ou página que você definir

5. OPCIONAL: a tela **"Registro"** fornece aos administradores as opções para:
    * "Exigir que os usuários se registrem ao entrar"
    * "Número de dias para solicitar que os usuários reconfirmem suas informações de autenticação"

6. OPCIONAL: a **"Notificação"** fornece aos administradores as opções de:
    * Notificar os usuários sobre as redefinições de senha
    * Notificar todos os administradores quando outros administradores redefinirem suas próprias senhas

**Neste ponto, você configurou a SSPR para seu locatário do Azure AD**. Você pode parar por aqui ou continuar a configurar a sincronização de senhas para um domínio do AD local.

> [!NOTE]
> Teste a SSPR com um usuário e não como um administrador, uma vez que a Microsoft impõe requisitos de autenticação fortes para contas de administrador do Azure. Para obter mais informações sobre a política de senha do administrador, confira o nosso [artigo de política de senha](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-existing-identity-source"></a>Como configurar a sincronização com fonte de identidade existentes

Para habilitar a sincronização de identidades local ao Azure AD, você precisa instalar e configurar o [Azure AD Connect](./connect/active-directory-aadconnect.md) em um servidor na sua organização. Esse aplicativo lida com a sincronização de usuários e grupos da fonte de identidade existentes para seu domínio do Azure AD.

* [Como atualizar do DirSync ou sincronizar o Azure AD com o Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Introdução ao Azure AD Connect usando configurações expressas](./connect/active-directory-aadconnect-get-started-express.md)
* [Configurar o write-back de senha](active-directory-passwords-writeback.md#configuring-password-writeback) para gravar senhas do Azure AD de volta ao seu diretório local.

## <a name="disabling-self-service-password-reset"></a>Desabilitação da redefinição da senha de autoatendimento

Desabilitar a redefinição de senha de autoatendimento é tão simples quanto abrir seu locatário do Azure AD e acessar **Redefinição de Senha**, **Propriedades** e escolher **Ninguém** em **Redefinição da Senha de Autoatendimento Habilitada**

## <a name="next-steps"></a>Próximas etapas
Os links a seguir fornecem informações adicionais sobre a redefinição de senha usando o Azure AD

* [**Licenciamento** ](active-directory-passwords-licensing.md) - Configuração do licenciamento do Azure AD
* [**Dados**](active-directory-passwords-data.md): entenda os dados que são necessários e como eles são usados para o gerenciamento de senhas
* [**Distribuição**](active-directory-passwords-best-practices.md): planeje e implante o SSPR para seus usuários usando as diretrizes descritas aqui
* [**Personalizar**](active-directory-passwords-customize.md): personalize a aparência da experiência do SSPR em sua empresa.
* [**Política** ](active-directory-passwords-policy.md) - Como entender e definir políticas de senha do Azure AD
* [**Relatórios**](active-directory-passwords-reporting.md): descubra se, quando e onde os usuários estão acessando a funcionalidade SSPR
* [**Detalhamento Técnico**](active-directory-passwords-how-it-works.md): veja os bastidores para entender como o recurso funciona
* [**Perguntas frequentes**](active-directory-passwords-faq.md): como? Por quê? O quê? Onde? Quem? Quando? – respostas para perguntas que você sempre quis fazer
* [**Solucionar problemas**](active-directory-passwords-troubleshoot.md): saiba como resolver problemas comuns encontrados no SSPR

