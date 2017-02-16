---
title: "Introdução ao painel de acesso | Microsoft Docs"
description: "Saiba como usar as várias versões do painel de acesso (navegador da Web, aplicativo Android, iPhone e iPad) para acessar os aplicativos SaaS atribuídos a você."
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
ms.date: 01/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: 2dd9c96e1c15e424588869b61eb26501b1b66be3


---
# <a name="introduction-to-the-access-panel"></a>Introdução ao painel de acesso
O painel de acesso é um portal baseado na Web que permite que um usuário final, com uma conta organizacional no Active Directory do Azure, veja e inicie aplicativos baseados em nuvem aos quais ele tenha acesso concedido pelo administrador do AD do Azure. Se você for um usuário final com edições do Active Directory do Azure, também poderá utilizar os recursos de gerenciamento de grupo de autoatendimento por meio do painel de acesso. <br>
 O painel de acesso é separado do Portal de Gerenciamento do Azure e não requer que os usuários tenham uma assinatura do Azure. 

![Painel de acesso][1] 

O painel de acesso permite aos usuários editar algumas de suas configurações de perfil, incluindo a capacidade de:

* Alterar a senha associada à sua conta organizacional
* Editar configurações de redefinição de senha
* Editar configurações de preferências e contato relacionadas a autenticação multifator (para contas solicitadas por um administrador a usá-las)
* Exibir detalhes da conta, como ID de usuário, email alternativo, números de celular e do escritório
* Exibir e iniciar aplicativos baseados em nuvem, aos quais foi concedido acesso pelo administrador do AD do Azure. Para obter mais informações sobre o painel de acesso da perspectiva dos usuários finais, consulte [Usando o painel de acesso](https://msdn.microsoft.com/library/azure/dn756411.aspx).
* Autogerenciar grupos. Mais especificamente, você pode criar e gerenciar grupos de segurança e solicitar associações ao grupo de segurança no AD do Azure. Para saber mais, confira [Gerenciamento de grupo de autoatendimento para usuários no Azure AD](active-directory-accessmanagement-self-service-group-management.md) e [Gerenciar seus grupos](active-directory-manage-groups.md). 

## <a name="accessing-the-access-panel"></a>Acessando o painel de acesso
Os usuários acessam o painel de acesso visitando a seguinte URL em um navegador da Web:  <br> 
**http://myapps.microsoft.com**

Se tiver a identidade visual personalizada configurada para sua página de entrada, vocë poderá carregar essa identidade visual por padrão, anexando o domínio da sua organização ao final da URL:  <br> 
**http://myapps.microsoft.com/contosobuild.com**

Nesse caso, qualquer nome de domínio ativo ou verificado que tenha sido configurado na guia Domínios do diretório no portal de gerenciamento do Azure pode ser usado, conforme ilustrado na captura de tela abaixo.

![Wingtip toys][2]  

Essa URL deve ser distribuída a todos os usuários que façam logon em aplicativos integrados ao AD do Azure.

## <a name="authentication"></a>Autenticação
Para acessar o painel de acesso, um usuário deve ser autenticado usando uma conta organizacional no AD do Azure. <br>
 Um usuário pode ser autenticado no Azure AD diretamente. <br>
 Como alternativa, se uma organização configurou a federação usando o AD FS ou outras tecnologias, os usuários poderão ser autenticados pelo Windows Server Active Directory.

Se um usuário tiver uma assinatura do Azure ou Office 365 e estiver usando o Portal de Gerenciamento do Azure ou um aplicativo do Office 365, ele verá a lista de aplicativos sem precisar entrar novamente. Usuários não autenticados serão solicitados a entrar usando o nome de usuário e a senha de sua conta no AD do Azure. Se a organização configurou a federação, digitar o nome do usuário é suficiente.

Uma vez autenticados, os usuários poderão interagir com os aplicativos integrados ao diretório pelo administrador. Para saber como integrar aplicativos ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Requisitos de navegador da Web
No mínimo, o painel de acesso requer um navegador com suporte para JavaScript e CSS habilitado. Para que o usuário possa acessar os aplicativos usando SSO baseado em senha, a extensão do painel de acesso deve estar instalada no navegador do usuário. Essa extensão do painel de acesso é baixada automaticamente quando um usuário seleciona um aplicativo configurado para SSO baseado em senha.

No momento, a extensão do painel de acesso está disponível para os navegadores Internet Explorer 8 e superior, Chrome e Firefox.

## <a name="mobile-app-support"></a>Suporte a aplicativos móveis
Para acessar aplicativos SSO baseados em senha em dispositivos iOS com Android, os usuários devem instalar o aplicativo móvel Meus aplicativos publicado pela equipe do Active Directory do Azure.

### <a name="my-apps-for-android"></a>Meus aplicativos para Android
Meus aplicativos para Android tem suporte em qualquer dispositivo Android executando o Android versão 4.1 e superior e está disponível hoje no [repositório do Google Play](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![Meus aplicativos][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>Meus aplicativos para iPhone e iPad
Meus aplicativos para iOS tem suporte em qualquer iPhone ou iPad que executem o iOS versão 7 e superior e está disponível hoje no Apple App Store.

![Perfil de aplicativos][4]    

> [!NOTE]
> Aplicativos que dão suporte a federação com o AD do Azure (incluindo Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 e mais de 70 outros), podem ser conectados a praticamente qualquer navegador da Web em qualquer dispositivo, sem a necessidade de um plug-in ou aplicativo móvel. O restante da experiência do painel de acesso em [https://myapps.microsoft.com](https://myapps.microsoft.com/) também não requer que o aplicativo móvel Meus aplicativos seja usado em um dispositivo móvel.
> 
> 

## <a name="tips-for-testing-the-end-user-experience"></a>Dicas para testar a experiência do usuário final
Se você for um administrador do Azure e estiver conectado ao Portal de Gerenciamento do Azure usando uma conta no diretório, será automaticamente conectado ao painel de acesso como sua conta de administrador atual. Nesse caso, você pode ver todos os aplicativos atribuídos a essa conta.

**Para testar como uma conta de usuário diferente:**

1. Clique no menu de usuário no canto superior direito do portal do Azure ou no painel de acesso e selecione "**Sair**". Isso o desconectará do AD do Azure.
2. Vá para o Painel de Acesso em **http://myapps.microsoft.com**.
3. Na página de entrada, digite o nome de usuário e a senha da conta no diretório que você quer testar.

## <a name="launching-applications"></a>Iniciando aplicativos
Há vários tipos de aplicativos que podem aparecer no painel de acesso.

### <a name="office-365-applications"></a>Aplicativos do Office 365
Se uma organização estiver usando aplicativos do Office 365 e o usuário for licenciado para eles, os aplicativos do Office 365 serão exibidos no painel de acesso do usuário.

Quando um usuário clica em um bloco do aplicativo para um aplicativo do Office 365, ele é redirecionado para esse aplicativo e conectado automaticamente.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Aplicativos da Microsoft e de terceiros configurados com o SSO baseado em federação
São aplicativos que o administrador adicionou na seção Active Directory do Portal de Gerenciamento do Azure com o modo de logon único definido como "*Logon único do Azure AD*". Um usuário só verá esses aplicativos se tiver acesso explicitamente concedido ao aplicativo pelo administrador.

Quando um usuário clica em um bloco do aplicativo para um desses aplicativos, ele é redirecionado para esse aplicativo e conectado automaticamente.

### <a name="password-based-sso-without-identity-provisioning"></a>SSO baseado em senha sem provisionamento de identidade
São aplicativos que o administrador adicionou na seção Active Directory do Portal de Gerenciamento do Azure com o modo de logon único definido como "*Logon único baseado em senha*". <br>  Todos os usuários no diretório verão todos os aplicativos configurados nesse modo.

Quando um usuário clica em um bloco do aplicativo pela primeira vez para um desses aplicativos, ele é solicitado a instalar o plug-in do SSO de senha para o Internet Explorer ou Chrome, que pode exigir a reinicialização do navegador da Web. Quando ele é retornado ao painel de acesso e clica novamente no bloco do aplicativo, será solicitado a ele um nome de usuário e uma senha para o aplicativo. Depois que o nome de usuário e a senha são inseridos, essas credenciais são armazenadas com segurança no AD do Azure e vinculadas à sua conta no AD do Azure, e o painel de acesso conectará automaticamente o usuário ao aplicativo usando essas credenciais.

Na próxima vez que um usuário clicar no bloco do aplicativo, ele será conectado automaticamente ao aplicativo sem a necessidade de inserir as credenciais novamente e sem instalar o plug-in do SSO de senha novamente.

Se as credenciais de um usuário forem alteradas no aplicativo de terceiros de destino, o usuário deverá também atualizar suas credenciais armazenadas no AD do Azure. Para atualizar as credenciais, um usuário deve selecionar o ícone no canto inferior direito do bloco do aplicativo e selecionar "Atualizar credenciais" para inserir novamente o nome de usuário e a senha para o aplicativo.

### <a name="password-based-sso-with-identity-provisioning"></a>SSO baseado em senha com provisionamento de identidade
São aplicativos que o administrador adicionou na seção Active Directory do Portal de Gerenciamento do Azure com o modo de logon único definido como "*Logon único baseado em senha*", além de provisionamento de identidade.

Quando um usuário clica em um bloco do aplicativo pela primeira vez para um desses aplicativos, ele é solicitado a instalar o plug-in do SSO de senha para o Internet Explorer ou Chrome, que pode exigir a reinicialização do navegador da Web. Quando ele é retornado ao painel de acesso e clica novamente no bloco do aplicativo, é solicitado a ele um nome de usuário e uma senha para o aplicativo.

Alguns aplicativos podem exigir que um usuário altere sua senha na primeira entrada. Se as credenciais de um usuário forem alteradas no aplicativo de terceiros de destino, o usuário deverá também atualizar suas credenciais armazenadas no AD do Azure. Para atualizar as credenciais, um usuário deve selecionar o ícone no canto inferior direito do bloco do aplicativo e selecionar "Atualizar credenciais" para inserir novamente o nome de usuário e a senha para o aplicativo.

### <a name="application-with-existing-sso-solutions"></a>Aplicativos com soluções de SSO existentes
Ao configurar o logon único para um aplicativo, o portal de gerenciamento do Azure fornece uma terceira opção de "Logon único existente". Simplesmente, essa opção permite ao administrador criar um link para um aplicativo e colocá-lo no painel de acesso para os usuários selecionados. Por exemplo, se houver um aplicativo que está configurado para autenticar usuários usando o Active Directory Federation Services 2.0, um administrador pode usar a opção "Logon único existente" para criar um link para ele no painel de acesso. Quando os usuários acessam o link, eles são autenticados usando o Active Directory Federation Services 2.0 ou qualquer que seja a solução de logon único existente fornecida pelo aplicativo.

## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)
* [Introdução ao logon único e gerenciamento de acesso a aplicativos com o Active Directory do Azure](active-directory-appssoaccess-whatis.md)
* [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS](active-directory-saas-app-provisioning.md)

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/ic767166.png
[2]: ./media/active-directory-saas-access-panel-introduction/ic767167.png
[3]: ./media/active-directory-saas-access-panel-introduction/ic767168.png
[4]: ./media/active-directory-saas-access-panel-introduction/ic767169.png



<!--HONumber=Dec16_HO5-->


