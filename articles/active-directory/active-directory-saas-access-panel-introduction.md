---
title: "Introdução ao painel de acesso | Microsoft Docs"
description: "Saiba como usar variações do Painel de Acesso (navegador da Web, aplicativo Android, iPhone e iPad) para acessar os aplicativos SaaS."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: c5a0f3f958d42d514a95477c1e39f02c78307d4d
ms.lasthandoff: 04/06/2017


---
# <a name="what-is-the-access-panel"></a>O que é o Painel de Acesso?
Painel de Acesso é um portal baseado na Web. Ele permite que um usuário que tenha uma conta corporativa ou de estudante no Azure Active Directory (Azure AD) exiba e inicie aplicativos baseados em nuvem para os quais o administrador do Azure AD concedeu acesso. Um usuário com as edições do Azure AD também pode usar os recursos de gerenciamento de grupo de autoatendimento por meio do Painel de Acesso.

O Painel de Acesso é separado do Portal do Azure e não exige que os usuários tenham uma assinatura do Azure.

![Painel de acesso][1]

O Painel de Acesso permite aos usuários editar algumas de suas configurações de perfil, incluindo a capacidade de:

* Alterar a senha associada a uma conta corporativa ou de estudante.
* Editar configurações de redefinição de senha.
* Editar configurações de preferências e contato relacionadas a autenticação multifator (para contas cujo administrador solicitou o uso desse recurso).
* Exibir detalhes da conta, como ID de usuário, email alternativo, números de celular e do escritório.
* Exibir e iniciar aplicativos baseados em nuvem aos quais o administrador do Azure AD concedeu acesso. Para saber mais sobre o painel de acesso da perspectiva dos usuários, veja [Uso do painel de acesso](https://msdn.microsoft.com/library/azure/dn756411.aspx).
* Autogerenciar grupos. Mais especificamente, o administrador pode criar e gerenciar grupos de segurança e solicitar associações ao grupo de segurança no Azure AD. Para saber mais, confira [Gerenciamento de grupo de autoatendimento para usuários no Azure AD](active-directory-accessmanagement-self-service-group-management.md) e [Gerenciar seus grupos](active-directory-manage-groups.md).

## <a name="accessing-the-access-panel"></a>Acessando o painel de acesso
Os usuários acessam o painel de acesso visitando a seguinte URL em um navegador da Web:  <br>
**http://myapps.microsoft.com**

Se tiver a identidade visual personalizada configurada para sua página de entrada, vocë poderá carregar essa identidade visual por padrão, anexando o domínio da sua organização ao final da URL:  <br>
**http://myapps.microsoft.com/contosobuild.com**

Nesse caso, você pode usar qualquer nome de domínio ativo ou verificado que tenha sido configurado na guia **Domínios** do diretório no Portal do Azure, conforme ilustrado na captura de tela abaixo:

![Nome de domínio Wingtip Toys][2]  

Essa URL deve ser distribuída a todos os usuários que entrarão nos aplicativos integrados ao Azure AD.

## <a name="authentication"></a>Autenticação
Para acessar o Painel de Acesso, um usuário deve ser autenticado usando uma conta corporativa ou de estudante no Azure AD. Um usuário pode ser autenticado no Azure AD diretamente. Como alternativa, se uma organização tiver configurado a federação usando o AD FS(Serviços de Federação do Active Directory) ou outras tecnologias, os usuários poderão ser autenticados pelo Windows Server Active Directory.

Se um usuário tiver uma assinatura do Azure ou Office 365 e estiver usando o Portal do Azure ou um aplicativo do Office 365, ele verá a lista de aplicativos sem precisar entrar novamente. Usuários não autenticados serão solicitados a entrar usando o nome de usuário e a senha de sua conta no Azure AD. Se a organização tiver configurado a federação, digitar o nome do usuário será suficiente.

Após a autenticação, os usuários poderão interagir com os aplicativos integrados ao diretório pelo administrador. Para saber como integrar aplicativos ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Requisitos de navegador da Web
O Painel de Acesso exige, pelo menos, um navegador com suporte para JavaScript e CSS habilitado. Para que o usuário possa acessar os aplicativos usando SSO (logon único) baseado em senha, a extensão do Painel de Acesso deve estar instalada no navegador do usuário. Essa extensão é baixada automaticamente quando um usuário seleciona um aplicativo configurado para SSO baseado em senha.

No momento, a extensão do Painel de Acesso está disponível para os navegadores Internet Explorer 8 e superior, Edge, Chrome e Firefox.

## <a name="mobile-app-support"></a>Suporte a aplicativos móveis
A equipe do Azure Active Directory publica o aplicativo móvel Meus Aplicativos. Quando os usuários instalam esse aplicativo, eles podem entrar em aplicativos de SSO baseado em senha em dispositivos iOS e Android.

> [!NOTE]
> Os usuários podem entrar em aplicativos que dão suporte à federação com o Azure AD (incluindo Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 e mais de 70 outros) em praticamente qualquer navegador da Web em qualquer dispositivo, sem a necessidade de um plug-in ou aplicativo móvel. O restante da [experiência do Painel de Acesso](https://myapps.microsoft.com/) também não exige que o aplicativo móvel Meus Aplicativos seja usado em um dispositivo móvel.
>
>

### <a name="my-apps-for-android"></a>Meus aplicativos para Android
Meus aplicativos para Android tem suporte em qualquer dispositivo Android que esteja executando o Android versão 4.1 e posteriores. Ele está disponível hoje na [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![Tela de Meus aplicativos para Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>Meus aplicativos para iPhone e iPad
Meus aplicativos para iOS tem suporte em qualquer iPhone ou iPad que esteja executando a versão do iOS 7 e posterior. Ele está disponível hoje na Apple App Store.

![Tela de Meus aplicativos para iOS][4]    

## <a name="tips-for-testing-the-user-experience"></a>Dicas para testar a experiência do usuário
Se você for um administrador do Azure e estiver conectado ao Portal do Azure usando uma conta no diretório, será automaticamente conectado ao painel de acesso como sua conta de administrador atual. Nesse caso, você pode ver todos os aplicativos atribuídos a essa conta.

Para testar como uma conta de usuário *diferente*:

1. Clique no menu de usuário no canto superior direito do Portal do Azure ou no painel de acesso e selecione **Sair**. Isso desconectará você do Azure AD.
2. Acesse o [Painel de Acesso](http://myapps.microsoft.com).
3. Na página de entrada, digite o nome de usuário e a senha da conta no diretório que você quer testar.

## <a name="starting-applications"></a>Iniciar aplicativos
Vários tipos de aplicativos podem aparecer no Painel de Acesso.

### <a name="office-365-applications"></a>Aplicativos do Office 365
Se uma organização estiver usando aplicativos do Office 365 e o usuário for licenciado para eles, os aplicativos do Office 365 serão exibidos no painel de acesso do usuário.

Quando um usuário clica em um bloco do aplicativo para um aplicativo do Office 365, ele é redirecionado para esse aplicativo e conectado automaticamente.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Aplicativos da Microsoft e de terceiros configurados com o SSO baseado em federação
O administrador pode adicionar aplicativos na seção Active Directory do Portal do Azure com o modo SSO definido como **Logon único do Azure AD**. Um usuário verá esses aplicativos somente se o administrador tiver concedido explicitamente a esse usuário acesso aos aplicativos.

Quando um usuário clica em um bloco de um desses aplicativos, ele é redirecionado para esse aplicativo e conectado automaticamente.

### <a name="password-based-sso-without-identity-provisioning"></a>SSO baseado em senha sem provisionamento de identidade
O administrador pode adicionar aplicativos na seção Active Directory do Portal do Azure com o modo SSO definido como **Logon único do baseado em senha**. Todos os usuários no diretório veem todos os aplicativos configurados nesse modo.

Na primeira vez que um usuário clica em um bloco de um desses aplicativos, ele recebe uma solicitação para instalar o plug-in do SSO de senha para o Internet Explorer ou Chrome. A instalação pode exigir ao usuário a reinicialização do navegador da Web. Quando o usuário retorna ao Painel de Acesso e clica novamente no bloco do aplicativo, ele recebe uma solicitação para inserir um nome de usuário e senha para o aplicativo. Após o usuário inserir o nome de usuário e senha, essas credenciais são armazenadas com segurança no Azure AD e vinculadas à conta dele no Azure AD.

Na próxima vez que o usuário clicar no bloco de aplicativo, ele será automaticamente conectado ao aplicativo. O usuário não precisará inserir as credenciais novamente e/ou instalar o plug-in do SSO de senha novamente.

Se as credenciais de um usuário forem alteradas no aplicativo de terceiros de destino, o usuário também deverá atualizar suas credenciais armazenadas no Azure AD. Para atualizar as credenciais, o usuário seleciona o ícone no bloco do aplicativo e seleciona **atualizar credenciais** para inserir novamente o nome de usuário e senha desse aplicativo.

### <a name="password-based-sso-with-identity-provisioning"></a>SSO baseado em senha com provisionamento de identidade
O administrador pode adicionar aplicativos na seção Active Directory do Portal do Azure com o modo SSO definido como **Logon único do baseado em senha** junto com o provisionamento da identidade.

Na primeira vez que um usuário clica em um bloco de um desses aplicativos, ele recebe uma solicitação para instalar o plug-in do SSO de senha para o Internet Explorer ou Chrome. A instalação pode exigir ao usuário a reinicialização do navegador da Web. Quando o usuário retorna ao Painel de Acesso e clica novamente no bloco do aplicativo, ele é automaticamente conectado ao aplicativo.

Alguns aplicativos podem exigir que o usuário altere sua senha na primeira entrada. Se as credenciais de um usuário forem alteradas no aplicativo de terceiros de destino, o usuário também deverá atualizar suas credenciais armazenadas no Azure AD. Para atualizar as credenciais, o usuário seleciona o ícone no bloco do aplicativo e seleciona **atualizar credenciais** para inserir novamente o nome de usuário e senha desse aplicativo.

### <a name="application-with-existing-sso-solutions"></a>Aplicativos com soluções de SSO existentes
Para configurar o SSO para um aplicativo, o Portal do Azure fornece uma terceira opção de **Logon Único Existente**. Essa opção permite ao administrador criar um link para um aplicativo e colocá-lo no Painel de Acesso para os usuários selecionados.

Por exemplo, se um aplicativo for configurado para autenticar usuários usando o AD FS 2.0, o administrador poderá usar a opção **Logon Único Existente** para criar um link para ele no Painel de Acesso. Quando os usuários acessam o link, eles são autenticados por meio do AD FS 2.0 ou por qualquer solução de SSO existente fornecida pelo aplicativo.

## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para gerenciamento de aplicativos no Azure Active Directory](active-directory-apps-index.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)
* [Introdução ao logon único e gerenciamento de acesso a aplicativos com o Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS](active-directory-saas-app-provisioning.md)

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/ic767166.png
[2]: ./media/active-directory-saas-access-panel-introduction/ic767167.png
[3]: ./media/active-directory-saas-access-panel-introduction/ic767168.png
[4]: ./media/active-directory-saas-access-panel-introduction/ic767169.png

