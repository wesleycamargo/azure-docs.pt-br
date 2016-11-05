---
title: 'Como funciona: Gerenciamento de Senhas do AD do Azure | Microsoft Docs'
description: Saiba mais sobre os diferentes componentes do Gerenciamento de Senhas do AD do Azure, incluindo onde os usuários se registram, redefinem e alteram suas senhas e onde os administradores configuram, relatam e habilitam o gerenciamento de senhas do Active Directory local.
services: active-directory
documentationcenter: ''
author: asteen
manager: femila
editor: curtand

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: asteen

---
# Como funciona o gerenciamento de senhas
> [!IMPORTANT]
> **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md).
> 
> 

O Gerenciamento de Senhas no Active Directory do Azure consiste em vários componentes lógicos que são descritos abaixo. Clique em cada link para saber mais sobre esse componente.

* [**Portal de Configuração de Gerenciamento de Senha**](#password-management-configuration-portal) – os administradores podem controlar diferentes aspectos de como as senhas são gerenciadas em seus locatários, navegando até a guia Configurar de seu diretório no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
* [**Portal de Registro de Usuário**](#user-registration-portal) – os usuários podem se registrar para a redefinição de senha nesse portal da Web.
* [**Portal de Redefinição de Senha do Usuário**](#user-password-reset-portal) – os usuários podem redefinir suas próprias senhas usando diversos desafios diferentes de acordo com a política de redefinição de senha controlada pelo administrador
* [**Portal de Alteração de Senha do Usuário**](#user-password-change-portal) – os usuários podem alterar suas próprias senhas a qualquer momento inserindo a senha antiga e selecionando uma nova senha usando esse portal da Web
* [**Relatórios de Gerenciamento de Senhas**](#password-management-reports) – os administradores podem exibir e analisar a atividade de registro e redefinição de senhas em seu locatário navegando até a seção "Relatórios de Atividade" na guia "Relatórios" de seu diretório no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com)
* [**Componente de Write-back de Senha do Azure AD Connect**](#password-writeback-component-of-azure-ad-connect) – os administradores podem, opcionalmente, habilitar o recurso de write-back de senha ao instalar o Azure AD Connect para habilitar o gerenciamento de senhas de usuário sincronizadas ou federadas na nuvem.

## Portal de Configuração de Gerenciamento de Senhas
Você pode configurar políticas de Gerenciamento de Senhas para um diretório específico usando o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) navegando até a seção **Política de Redefinição de Senhas do Usuário** na guia **Configurar** do diretório. Na página de configuração, você pode controlar vários aspectos de como as senhas são gerenciadas em sua organização, incluindo:

* Habilitar e desabilitar a redefinição de senhas para todos os usuários em um diretório
* Definir o número de desafios (um ou dois) que um usuário deve superar para redefinir sua senha
* Definir os tipos específicos de desafios que você deseja habilitar para os usuários em sua organização, entre as opções a seguir:
  * Telefone celular (código de verificação por texto ou chamada de voz)
  * Telefone comercial (chamada de voz)
  * Email alternativo (código de verificação por email)
  * Perguntas de segurança (autenticação baseada em conhecimento)
* Definir o número de perguntas que um usuário deve registrar para usar o método de autenticação de perguntas de segurança (visível somente se perguntas de segurança estiverem ativadas)
* Definir o número de perguntas que um usuário deve fornecer durante a redefinição para usar o método de autenticação de perguntas de segurança (visível somente se perguntas de segurança estiverem ativadas)
* Usar perguntas de segurança pré-configuradas e localizadas que um usuário pode escolher ao se registrar para a redefinição de senha (estará visível apenas se as perguntas de segurança estiverem ativadas)
* Definir as perguntas de segurança personalizadas que um usuário pode escolher ao se registrar para a redefinição de senha (estará visível apenas se as perguntas de segurança estiverem ativadas)
* Exigir que os usuários se registrem para a redefinição de senha ao irem para o Painel de Acesso ao Aplicativo em [http://myapps.microsoft.com](http://myapps.microsoft.com).
* Exigir que os usuários reconfirmem seus dados previamente registrados depois de um número configurável de dias (visível apenas se o registro imposto estiver habilitado)
* Fornecer um email de assistência técnica personalizada ou uma URL que será exibido aos usuários caso eles tenham um problema ao redefinir suas senhas
* Habilitar ou desabilitar o recurso de write-back de senha (quando o Write-back de Senha foi implantado usando AAD Connect)
* Exibir o status de agente de write-back de senha (quando o write-back de senha foi implantado usando AAD Connect)
* Habilitar notificações por email para os usuários quando sua própria senha foi redefinida (encontrada na seção **notificações** do [Portal de Gerenciamento do Azure](https://manage.windowsazure.com))
* Habilitar notificações por email para os administradores quando outros administradores redefinem suas próprias senhas (encontrado na seção **Notificações** do [Portal de Gerenciamento do Azure](https://manage.windowsazure.com)
* Marcar com identidade visual o portal e emails de redefinição de senhas do usuário com o logotipo e o nome de sua organização usando o recurso de personalização de identidade visual de locatário (encontrado na seção **Propriedades do Diretório** do [Portal de Gerenciamento do Azure](https://manage.windowsazure.com)

Para saber mais sobre como configurar o Gerenciamento de Senhas na sua organização, consulte [Introdução: Gerenciamento de Senhas do AD do Azure](active-directory-passwords-getting-started.md).

## Portal de registro de usuário
Para queos usuários possam usar a redefinição de senha, suas contas de usuário de nuvem devem ser atualizadas com os dados de autenticação corretos para garantir que eles possam passar pelo número correto de desafios de redefinição de senha definidos pelo administrador. Os administradores também podem definir essas informações de autenticação em nome de seu usuário usando os portais do Azure ou Office, DirSync/Azure AD Connect ou o Windows PowerShell.

No entanto, se você preferir que os usuários registrem seus próprios dados, também fornecemos uma página da Web que os usuários podem acessar para fornecer essas informações. Essa página permitirá que os usuários especifiquem informações de autenticação de acordo com as políticas de redefinição de senha habilitadas em sua organização. Depois que esses dados são verificados, são armazenados em sua conta de usuário de nuvem para serem usados para recuperação de conta em um momento posterior. Esta é a aparência do portal de registro:

  ![][001]

Para obter mais informações, consulte [Introdução: Gerenciamento de Senhas do AD do Azure](active-directory-passwords-getting-started.md) e [Práticas recomendadas: Gerenciamento de Senhas do AD do Azure](active-directory-passwords-best-practices.md).

## Portal de redefinição de senha do usuário
Depois que você habilitar a redefinição de senha de autoatendimento, configurar a política de redefinição de senha de autoatendimento de sua organização e garantir que os usuários tenham os dados de contato apropriados no diretório, os usuários em sua organização poderão redefinir suas próprias senhas automaticamente em qualquer página da Web que use uma conta do trabalho ou da escola para entrar (como [portal.microsoftonline.com](https://portal.microsoftonline.com)). Em páginas como essas, os usuários verão um link **Não é possível acessar sua conta?**

  ![][002]

Clicar nesse link abrirá o portal de redefinição de senha de autoatendimento.

  ![][003]

Para saber mais sobre como os usuários podem redefinir suas próprias senhas, consulte [Introdução: Gerenciamento de Senhas do AD do Azure](active-directory-passwords-getting-started.md).

## Portal de alteração de senha do usuário
Se os usuários quiserem alterar suas próprias senhas, eles poderão fazer isso usando o portal de alteração de senha a qualquer momento. Os usuários podem acessar o portal de alteração de senha por meio da página de perfil do Painel de Acesso ou clicando no link "alterar senha" nos aplicativos do Office 365. Caso suas senhas expieam, os usuários também deverão alterá-las automaticamente ao entrar.

  ![][004]

Em ambos os casos, se o write-back de senha tiver sido habilitado e o usuário for federado ou sincronizado por senha, essas senhas alteradas serão gravadas no Active Directory local. Esta é a aparência do portal de alteração de senha:

  ![][005]

Para saber mais sobre como os usuários podem alterar suas próprias senhas do Active Directory local, consulte [Introdução: Gerenciamento de Senhas do AD do Azure](active-directory-passwords-getting-started.md).

## Relatórios de gerenciamento de senha
Navegando para a guia **Relatórios** e consultando a seção **Logs de atividade**, você verá dois relatórios de gerenciamento de senhas: **Atividade de redefinição de senhas** e **Atividade de registro de redefinição de senhas**. Usando esses dois relatórios, você pode obter uma exibição dos usuários que se registram e usando a redefinição de senhas em sua organização. Esta é a aparência desses relatórios no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com):

  ![][006]

Para obter mais informações, consulte [Obter percepções: relatórios de gerenciamento de senhas do AD do Azure](active-directory-passwords-get-insights.md).

## Componente de write-back de senha do Azure AD Connect
Se as senhas de usuários em sua organização se originam de seu ambiente local (por meio de federação ou sincronização de senha), você pode instalar a versão mais recente do Azure AD Connect para habilitar a atualização dessas senhas diretamente na nuvem. Isso significa que quando os usuários se esquecem da senha do AD ou querem modificá-la, eles podem fazer isso diretamente na Web. Veja onde encontrar o write-back de senha do sssistente de instalação do Azure AD Connect:

  ![][007]

Para obter mais informações sobre o Azure AD Connect, consulte [Introdução: Azure AD Connect](active-directory-aadconnect.md). Para obter mais informações sobre o write-back de senha, consulte [Introdução: Gerenciamento de Senhas do AD do Azure](active-directory-passwords-getting-started.md).

<br/> <br/> <br/>

## Links para a documentação de redefinição de senha
Veja abaixo links para todas as páginas de documentação sobre Redefinição de Senha do AD do Azure:

* **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md).
* [**Introdução**](active-directory-passwords-getting-started.md) - saiba como permitir que os usuários redefinam e alterem suas senhas na nuvem ou no local
* [**Personalizar**](active-directory-passwords-customize.md) - aprenda a personalizar a aparência e o comportamento do serviço de acordo com as necessidades de sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) - aprenda a implantar rapidamente e gerenciar com eficiência as senhas em sua organização
* [**Obter percepções**](active-directory-passwords-get-insights.md) - saiba mais sobre nossos recursos integrados de relatórios
* [**Perguntas frequentes**](active-directory-passwords-faq.md) - obtenha respostas para perguntas frequentes
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) - aprenda a solucionar rapidamente os problemas com o serviço
* [**Saiba mais**](active-directory-passwords-learn-more.md) - aprofunde-se nos detalhes técnicos do funcionamento do serviço

[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"

<!---HONumber=AcomDC_0713_2016-->