---
title: 'Como funciona: gerenciamento de senhas do Azure AD | Microsoft Docs'
description: "Saiba mais sobre os diferentes componentes do gerenciamento de senhas do Azure AD, incluindo em que os usuários se registram, redefinem e alteram suas senhas e em que os administradores configuram, relatam e habilitam o gerenciamento de senhas do Active Directory local."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 24c94f80afdb3d3330a835fae2f77ecb213a9ae5
ms.lasthandoff: 03/28/2017


---
# <a name="how-password-management-works-in-azure-active-directory"></a>Como funciona o gerenciamento de senha no Azure Active Directory
> [!IMPORTANT]
> **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md#reset-your-password).

O gerenciamento de senhas no Azure AD (Azure Active Directory) consiste nos seguintes componentes lógicos:

* **Portal de configuração de gerenciamento de senhas**: você pode controlar diferentes aspectos de como as senhas são gerenciadas em seus locatários, navegando até a guia **Configurar** do diretório no [portal do Azure](https://manage.windowsazure.com).
* **Portal de registro de usuário**: os usuários podem se registrar para a redefinição de senha nesse portal da Web.
* **Portal de redefinição de senha do usuário**: os usuários podem redefinir suas próprias senhas usando diversos desafios diferentes de acordo com a política de redefinição de senha controlada pelo administrador.
* **Portal de alteração de senha do usuário**: os usuários podem alterar suas próprias senhas a qualquer momento inserindo a senha antiga e selecionando uma nova senha por meio desse portal da Web.
* **Relatórios de gerenciamento de senhas**:você pode exibir e analisar a atividade de registro e redefinição de senhas em seu locatário navegando até a seção **Relatórios da Atividade** na guia **Relatórios** de seu diretório no [portal do Azure](https://manage.windowsazure.com).
* **Componente de write-back de senha do Azure AD Connect**: opcionalmente, você pode habilitar o recurso de write-back de senha ao instalar o Azure AD Connect para habilitar o gerenciamento de senhas de usuário sincronizadas ou federadas na nuvem.

## <a name="password-management-configuration-portal"></a>Portal de configuração de gerenciamento de senhas
Você pode configurar as políticas do gerenciamento de senhas para um diretório específico no [portal do Azure](https://manage.windowsazure.com) navegando até a seção **Política de Redefinição de Senhas do Usuário** na guia **Configurar** do diretório. Na página de configuração, você pode controlar vários aspectos de como as senhas são gerenciadas em sua organização, incluindo:

* Habilitar e desabilitar a redefinição de senhas para todos os usuários em um diretório.
* Definir o número de desafios (um ou dois) que um usuário deve superar para redefinir sua senha.
* Definir os tipos específicos de desafios que você deseja habilitar para os usuários em sua organização, entre estas opções:
  * Telefone celular (código de verificação por texto ou chamada de voz)
  * Telefone comercial (chamada de voz)
  * Email alternativo (código de verificação por email)
  * Perguntas de segurança (autenticação baseada em conhecimento)
* Definir o número de perguntas que um usuário deve registrar para usar o método de autenticação de perguntas de segurança (visível somente se perguntas de segurança estiverem habilitadas).
* Definir o número de perguntas que um usuário deve fornecer durante a redefinição para usar o método de autenticação de perguntas de segurança (visível somente se perguntas de segurança estiverem habilitadas).
* Usar perguntas de segurança pré-configuradas e localizadas que um usuário pode escolher ao se registrar para a redefinição de senha (estará visível apenas se as perguntas de segurança estiverem habilitadas).
* Definir as perguntas de segurança personalizadas que um usuário pode escolher ao se registrar para a redefinição de senha (estará visível apenas se as perguntas de segurança estiverem habilitadas).
* Exigindo que os usuários se registrem para a redefinição de senha ao acessarem o [Painel de Acesso](http://myapps.microsoft.com) do aplicativo.
* Exigir que os usuários reconfirmem seus dados previamente registrados depois de um número configurável de dias (visível apenas se o registro imposto estiver habilitado).
* Fornecer um email de assistência técnica personalizada ou uma URL que será exibido aos usuários caso eles tenham um problema ao redefinir suas senhas.
* Habilitar ou desabilitar o recurso de write-back de senha (quando o Write-back de Senha foi implantado usando o Azure AD Connect).
* Exibir o status de agente de write-back de senha (quando o write-back de senha foi implantado usando o Azure AD Connect).
* Habilitar notificações por email para os usuários quando sua própria senha foi redefinida (encontrada na seção **notificações** do [portal do Azure](https://manage.windowsazure.com)).
* Habilitar notificações por email para os administradores quando outros administradores redefinem suas próprias senhas (encontrado na seção **Notificações** do [portal do Azure](https://manage.windowsazure.com)).
* Marcar com identidade visual o portal e emails de redefinição de senhas do usuário com o logotipo e o nome de sua organização usando o recurso de personalização de identidade visual de locatário (encontrado na seção **Propriedades do Diretório** do [portal do Azure](https://manage.windowsazure.com)).

Para saber mais sobre como configurar o gerenciamento de senhas em sua organização, confira [Introdução: gerenciamento de senhas do Azure AD](active-directory-passwords-getting-started.md).

## <a name="user-registration-portal"></a>Portal de registro de usuário
Para que os usuários possam usar a redefinição de senha, você deve atualizar suas contas de usuário de nuvem com os dados de autenticação corretos para garantir que eles possam ser aprovados no número correto de desafios de redefinição de senha que você definir. Você também pode definir essas informações de autenticação em nome do usuário usando os portais do Azure ou Office, DirSync/Azure AD Connect ou Windows PowerShell.

No entanto, se você prefere que os usuários registrem seus próprios dados, também fornecemos uma página da Web que os usuários podem acessar a fim de adicionar essas informações. Essa página permite que os usuários especifiquem informações de autenticação de acordo com as políticas de redefinição de senha habilitadas em sua organização. Depois que esses dados são verificados, ele é armazenado em sua conta de usuário de nuvem para uso na recuperação de conta mais tarde.

Esta é a aparência do portal de registro:

  ![][001]

Para obter mais informações, confira [Introdução: gerenciamento de senhas do Azure AD](active-directory-passwords-getting-started.md) e [Práticas recomendadas: gerenciamento de senhas do Azure AD](active-directory-passwords-best-practices.md).

## <a name="user-password-reset-portal"></a>Portal de redefinição de senha do usuário
Depois que você habilitar a redefinição de senha de autoatendimento, configurar a política de redefinição de senha de autoatendimento de sua organização e garantir que os usuários tenham os dados de contato apropriados no diretório, os usuários em sua organização poderão redefinir suas próprias senhas automaticamente em qualquer página da Web que use uma conta corporativa ou de estudante para entrar (como [portal.microsoftonline.com](https://portal.microsoftonline.com)). Em páginas como essas, os usuários verão um link **Não consegue acessar sua conta?**.

  ![][002]

Clicar nesse link abre o portal de redefinição de senha de autoatendimento.

  ![][003]

Para saber mais sobre como os usuários podem redefinir suas próprias senhas, confira [Introdução: gerenciamento de senhas do Azure AD](active-directory-passwords-getting-started.md).

## <a name="user-password-change-portal"></a>Portal de alteração de senha do usuário
Se os usuários quiserem alterar suas próprias senhas, poderão fazer isso usando o portal de alteração de senha a qualquer momento. Os usuários podem acessar o portal de alteração de senha por meio da página de perfil do Painel de Acesso ou clicando no link **Alterar senha** nos aplicativos do Office 365. Quando as senhas expirarem, os usuários também serão solicitados a alterá-las automaticamente quando entrarem.

  ![][004]

Em ambos os casos, se o Write-back de Senha tiver sido habilitado e o usuário for federado ou sincronizado por senha, essas senhas alteradas serão gravadas no Active Directory local.

Esta é a aparência do portal de alteração de senha:

  ![][005]

Para saber mais sobre como os usuários podem alterar suas próprias senhas do Active Directory local, confira [Introdução: gerenciamento de senhas do Azure AD](active-directory-passwords-getting-started.md).

## <a name="password-management-reports"></a>Relatórios de gerenciamento de senha
Ao navegar até a guia **Relatórios** e conferir a seção **Logs de Atividade**, você verá dois relatórios de gerenciamento de senhas: **Atividade de redefinição de senha** e **Atividade de registro da redefinição de senha**. Você pode usar esses dois relatórios para obter uma exibição dos usuários que se registram e usam a redefinição de senha em sua organização.

Aqui está a aparência desses relatórios no [portal do Azure](https://manage.windowsazure.com):

  ![][006]

Para obter mais informações, confira [Obter percepções: relatórios de gerenciamento de senhas do Azure AD](active-directory-passwords-get-insights.md).

## <a name="password-writeback-component-of-azure-ad-connect"></a>Componente de write-back de senha do Azure AD Connect
Se as senhas de usuários em sua organização se originam de seu ambiente local (por meio de federação ou sincronização de senha), você pode instalar a versão mais recente do Azure AD Connect para habilitar a atualização dessas senhas diretamente na nuvem. Isso significa que quando os usuários se esquecem da senha do Azure AD ou querem modificá-la, podem fazer isso diretamente na Web. Veja onde encontrar o write-back de senha do sssistente de instalação do Azure AD Connect:

  ![][007]

Para obter mais informações sobre o Azure AD Connect, confira [Introdução: Azure AD Connect](active-directory-aadconnect.md). Para obter mais informações sobre o write-back de senha, confira [Introdução: gerenciamento de senhas do Azure AD](active-directory-passwords-getting-started.md).


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, confira as seguintes páginas sobre redefinição de senha do Azure AD:

* **Você está aqui por que está enfrentando problemas para iniciar sessão?** Nesse caso, veja como [alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md#reset-your-password).
* [**Introdução**](active-directory-passwords-getting-started.md): saiba como permitir que os usuários redefinam e alterem suas senhas na nuvem ou no local.
* [**Personalizar**](active-directory-passwords-customize.md): aprenda a personalizar a aparência e o comportamento do serviço de acordo com as necessidades de sua organização.
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md): aprenda a implantar rapidamente e gerenciar com eficiência as senhas em sua organização.
* [**Obter percepções**](active-directory-passwords-get-insights.md): saiba mais sobre nossos recursos integrados de relatórios.
* [**Perguntas frequentes**](active-directory-passwords-faq.md): obtenha respostas para perguntas frequentes.
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md): aprenda a solucionar rapidamente os problemas com o serviço.
* [**Saiba mais**](active-directory-passwords-learn-more.md): aprofunde-se nos detalhes técnicos do funcionamento do serviço.

[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"

