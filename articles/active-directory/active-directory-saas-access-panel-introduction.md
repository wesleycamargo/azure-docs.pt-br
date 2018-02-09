---
title: "O que é o painel de acesso no Azure Active Directory? | Microsoft Docs"
description: "Saiba como usar variações do painel de acesso (navegador da Web, aplicativo Android, iPhone e iPad) para acessar os aplicativos SaaS."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c9069cb0b46ddc1155c64bd63a7fcd8a685abbad
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="what-is-the-access-panel"></a>O que é o painel de acesso?

O painel de acesso é um portal baseado na Web. Caso você tenha uma conta corporativa ou de estudante no Azure Active Directory (Azure AD), pode usar o painel de acesso para exibir e iniciar aplicativos baseados em nuvem para os quais um administrador do Azure AD concedeu acesso. Você também pode usar os recursos de gerenciamento de grupo de autoatendimento e aplicativo por meio do painel de acesso.

O painel de acesso é separado do portal do Azure. Não é preciso ter uma assinatura do Azure.

![Painel de acesso][1] Ao usar o painel de acesso, é possível editar algumas configurações do seu perfil e fazer o seguinte:

- Alterar a senha associada a uma conta corporativa ou de estudante.

- Editar configurações de redefinição de senha.

- Editar configurações de preferências e contato relacionadas a autenticação multifator (para contas cujo administrador solicitou o uso desse recurso).

- Exibir detalhes da conta, como ID de usuário, email alternativo, número de celular e do escritório e dispositivos.

- Exibir e iniciar aplicativos baseados em nuvem aos quais o administrador do Azure AD concedeu acesso. 

- Autogerenciar grupos. Administradores podem criar e gerenciar grupos de segurança e solicitar associações ao grupo de segurança no Azure AD. Para saber mais, confira [Gerenciamento de grupo de autoatendimento para usuários no Azure AD](active-directory-accessmanagement-self-service-group-management.md) e [Gerenciar seus grupos](active-directory-manage-groups.md).




## <a name="access-the-access-panel"></a>Acessar o painel de acesso

É possível acessar o painel de acesso em `http://myapps.microsoft.com`.

Se tiver a identidade visual personalizada configurada para sua página de entrada, poderá carregar a identidade visual anexando o domínio da sua organização à URL (por exemplo, `http://myapps.microsoft.com/<your domain>.com`).

É possível usar qualquer nome de domínio ativo ou verificado que tenha sido configurado em seu portal do Azure, como mostrado aqui:

![Nome de domínio Wingtip Toys][2]  

Distribua a URL a todos os usuários que entram nos aplicativos integrados ao Azure AD.

## <a name="authentication"></a>Autenticação

Para acessar o painel de acesso, você deve ser autenticado usando uma conta corporativa ou de estudante no Azure AD. Você pode ser autenticado no Azure AD diretamente. Como alternativa, se uma organização tiver configurado a federação usando o AD FS (Serviços de Federação do Active Directory) ou outras tecnologias, você pode ser autenticado pelo Windows Server Active Directory.

Se você tiver uma assinatura do Azure ou do Office 365 e estiver usando o Portal do Azure ou um aplicativo do Office 365, pode ver a lista de aplicativos sem entrar novamente. Se você não estiver autenticado, receberá uma solicitação para entrar usando o nome de usuário e a senha de sua conta no Azure AD. Se sua organização tiver configurado a federação, digitar o nome do usuário será suficiente.

Quando está autenticado, você pode interagir com os aplicativos integrados ao diretório pelo administrador. Para saber como integrar aplicativos ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Requisitos de navegador da Web

O painel de acesso exige, pelo menos, um navegador com suporte para JavaScript e CSS habilitado. Para entrar nos aplicativos usando SSO (logon único) baseado em senha, é preciso ter a extensão do painel de acesso instalada no seu navegador. A extensão é baixada automaticamente quando você seleciona um aplicativo configurado para SSO baseado em senha.

A extensão do painel de acesso está atualmente disponível para:
- **Edge**: no Windows 10 Anniversary Edition ou posterior. 
- **Chrome**: no Windows 7 ou posterior e no MacOS X ou posterior.
- **Firefox 26.0 ou posterior**: no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior.
- **Internet Explorer 8, 9, 10 ou 11**: no Windows 7 ou posterior (suporte limitado).

## <a name="my-apps-secure-sign-in-extension"></a>Extensão de Entrada Segura dos Meus Aplicativos
Para entrar com logon único baseado em senha, você deve usar a extensão. Depois que a extensão estiver instalada, você pode entrar para habilitar recursos adicionais selecionando **Entrar para começar**. 

- Você pode entrar em um aplicativo diretamente usando a **URL de logon** do aplicativo. Quando você usa a URL do aplicativo, a extensão detecta a ação e oferece a opção de entrar a partir da extensão.
- Você também pode iniciar qualquer um de seus aplicativos no painel de acesso usando o recurso de *pesquisa rápida* da extensão. 
- A extensão mostra os três últimos aplicativos iniciados na seção **Usados Recentemente**.

> [!NOTE]
> Recursos adicionais estão disponíveis apenas para Edge, Chrome e Firefox.
>

Caso esteja usando uma URL dos Meus Aplicativos diferente de `https://myapps.microsoft.com`, configure sua URL padrão fazendo o seguinte:
1. Enquanto *não* estiver conectado à extensão, clique com o botão direito do mouse no ícone da extensão.
2. No menu, selecione **URL dos Meus aplicativos**.
3. Selecione a URL padrão.
4. Selecione o ícone da extensão.
5. Selecione **Entrar para começar**.

## <a name="mobile-app-support"></a>Suporte a aplicativos móveis

A equipe do Azure Active Directory publica o aplicativo móvel Meus Aplicativos. Quando você instala esse aplicativo, você pode entrar em aplicativos de SSO baseado em senha em dispositivos iOS e Android.

> [!NOTE]
> Você pode entrar em aplicativos que dão suporte à federação com o Azure AD (incluindo Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 e mais de 70 outros) em praticamente qualquer navegador da Web em qualquer dispositivo, sem a necessidade de um plug-in ou aplicativo móvel. Para ser usado em um dispositivo móvel, as outras [experiências do painel de acesso](https://myapps.microsoft.com/) também não precisam do aplicativo móvel Meus Aplicativos.
>
>

### <a name="my-apps-for-android"></a>Meus aplicativos para Android

O Meus Aplicativos para Android tem suporte em qualquer dispositivo Android que esteja executando a versão 4.1 ou posterior do Android.  

Está disponível na [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![Meus aplicativos para Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>Meus aplicativos para iPhone e iPad

O Meus Aplicativos para iOS tem suporte em qualquer iPhone ou iPad que esteja executando a versão 7 ou posterior do iOS.  

Está disponível na [Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Meus Aplicativos para iOS][4]    


## <a name="managed-browser-for-my-apps"></a>Navegador gerenciado para Meus Aplicativos

O Meus Aplicativos também é integrado ao Intune Managed Browser. O Intune Managed Browser para dispositivos iOS e Android desempenha uma função fundamental para ajudar a assegurar que os dados em dispositivos móveis permaneçam seguros. O navegador permite exibir e navegar com segurança por páginas da Web que podem conter informações da empresa e ajuda a fornecer uma experiência segura de navegação na Web.  

Você obtém acesso rápido ao Meus Aplicativos em sua home page no Managed Browser e em seus favoritos. Assim, são necessários poucos cliques para acessar qualquer aplicativo que desejar.

O Intune Managed Browser está disponível na [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) e na [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en).

![Navegador gerenciado para Meus Aplicativos][5]    


## <a name="tips-for-testing-the-user-experience"></a>Dicas para testar a experiência do usuário

Se você for um administrador do Azure e estiver conectado ao Portal do Azure usando uma conta no diretório, será automaticamente conectado ao painel de acesso como sua conta atual. Essa exibição mostra todos os aplicativos atribuídos a você.

Para testar uma conta de usuário *diferente*, faça o seguinte:

1. No canto superior direito do portal do Azure ou no painel de acesso, selecione **Sair**. 
2. Acesse o [painel de acesso](http://myapps.microsoft.com).
3. Na página de entrada, digite o nome de usuário e a senha da conta no diretório que você deseja testar.


## <a name="starting-applications"></a>Iniciar aplicativos

Nesta seção, são discutidos vários tipos de aplicativos que podem aparecer no painel de acesso.

### <a name="office-365-applications"></a>Aplicativos do Office 365

Se sua organização estiver usando aplicativos do Office 365 e você for licenciado para eles, os aplicativos do Office 365 serão exibidos no seu painel de acesso.

Ao selecionar um bloco do aplicativo para um aplicativo do Office 365, você é redirecionado para o aplicativo e conectado automaticamente.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Aplicativos da Microsoft e de terceiros configurados com o SSO baseado em federação

Seu administrador pode adicionar aplicativos na seção Active Directory do Portal do Azure com o modo SSO definido como **Logon único do Azure AD**. Você só poderá ver esses aplicativos se seu administrador tiver explicitamente lhe concedido acesso aos aplicativos.

Ao selecionar um bloco para um aplicativo, você é redirecionado e automaticamente conectado a ele.

### <a name="password-based-sso-without-identity-provisioning"></a>SSO baseado em senha sem provisionamento de identidade

Seu administrador pode adicionar aplicativos na seção Active Directory do Portal do Azure com o modo SSO definido como **Logon único do baseado em senha**. Todos os usuários no diretório podem ver todos os aplicativos configurados nesse modo.

Na primeira vez que você seleciona um bloco de aplicativo, recebe uma solicitação para instalar o plug-in do SSO de senha para o Internet Explorer ou Chrome. A instalação pode exigir você a reinicialização do seu navegador da Web. Ao retornar ao painel de acesso e selecionar novamente o bloco do aplicativo, você recebe uma solicitação para inserir um nome de usuário e senha para o aplicativo. Ao inserir o nome de usuário e senha, as credenciais são armazenadas com segurança e vinculadas à sua conta no Azure AD.

Na próxima vez que selecionar o bloco do aplicativo, você será automaticamente conectado ao aplicativo.  

Você não precisará inserir suas credenciais novamente e/ou instalar o plug-in do SSO de senha.

Se suas credenciais de um usuário forem alteradas no aplicativo de terceiros de destino, você também deverá atualizar suas credenciais armazenadas no Azure AD. 

Para atualizar suas credenciais, faça o seguinte:

1. Selecione o ícone no bloco do aplicativo.
2. Selecione **atualizar credenciais** para inserir novamente o nome de usuário e a senha para o aplicativo.


### <a name="password-based-sso-with-identity-provisioning"></a>SSO baseado em senha com provisionamento de identidade

Seu administrador pode adicionar aplicativos na seção Active Directory do Portal do Azure com o modo SSO definido como **Logon único baseado em senha**, junto com o provisionamento da identidade.

Na primeira vez que você seleciona um bloco de aplicativo, recebe uma solicitação para instalar o plug-in do SSO de senha para o Internet Explorer ou Chrome. A instalação pode exigir você a reinicialização do seu navegador da Web.  

Ao retornar ao painel de acesso e selecionar novamente o bloco do aplicativo, você é automaticamente conectado ao aplicativo.

Alguns aplicativos podem exigir que você altere sua senha na primeira entrada. Se suas credenciais de um usuário forem alteradas no aplicativo de terceiros de destino, você também deverá atualizar as credenciais armazenadas no Azure AD. 

Para atualizar suas credenciais, faça o seguinte:

1. Selecione o ícone no bloco do aplicativo.
2. Selecione **atualizar credenciais** para inserir novamente o nome de usuário e a senha para o aplicativo.


### <a name="application-with-existing-sso-solutions"></a>Aplicativos com soluções de SSO existentes

Para configurar o SSO para um aplicativo, o Portal do Azure fornece uma terceira opção chamada de Logon Único Existente. Essa opção permite que o administrador crie um link para um aplicativo e coloque-o no painel de acesso para os usuários selecionados.

Por exemplo, se um aplicativo for configurado para autenticar usuários usando o AD FS 2.0, seu administrador poderá usar a opção Logon Único Existente para criar um link para ele no painel de acesso. Quando os você acessar o link, você é autenticado por meio do AD FS 2.0 ou por qualquer solução de SSO existente fornecida pelo aplicativo.


## <a name="next-steps"></a>Próximas etapas

- Para exibir uma lista de todos os tópicos relacionados ao gerenciamento de aplicativos, confira o [índice do artigo para gerenciamento de aplicativos no Azure Active Directory](active-directory-apps-index.md).
 
- Para saber como integrar um aplicativo SaaS no Azure AD, confira a [lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md).
 
- Para saber mais sobre como gerenciar aplicativos com o AD do Azure, consulte a [Introdução ao acesso ao aplicativo de gerenciamento e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).
 
- Para obter mais informações sobre provisionamento de usuário, consulte [aplicativos SaaS para provisionamento e desprovisionamento automatizado do usuário](active-directory-saas-app-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
