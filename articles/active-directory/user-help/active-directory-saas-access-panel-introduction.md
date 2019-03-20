---
title: O que é o portal do MyApps - Azure Active Directory? | Microsoft Docs
description: Aprenda a usar variações do portal MyApps (navegador da Web, aplicativo Android, iPhone e iPad) para acessar aplicativos SaaS.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.reviewer: asteen
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47c624a9f1d3989e9146f7c32745ca892f6467e0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58115448"
---
# <a name="what-is-the-myapps-portal"></a>O que é o portal do MyApps?

Se você tiver uma conta de trabalho ou escola no Azure AD (Azure AD), poderá usar o portal baseado na web Meus Aplicativos para exibir e iniciar aplicativos baseados na nuvem aos quais um administrador do Azure AD concedeu acesso. Você também pode usar recursos de gerenciamento de aplicativos e grupos de autoatendimento por meio do portal MyApps.

O portal MyApps é separado do portal do Azure. Ele não requer que você tenha uma assinatura do Azure.

![Portal MyApps][1] Usando o portal MyApps, você pode editar algumas das configurações do seu perfil e fazer o seguinte:

- Alterar a senha associada a uma conta corporativa ou de estudante.

- Editar configurações de redefinição de senha.

- Editar configurações de preferências e contato relacionadas a autenticação multifator (para contas cujo administrador solicitou o uso desse recurso).

- Exibir detalhes da conta, como ID de usuário, email alternativo, número de celular e do escritório e dispositivos.

- Exibir e iniciar aplicativos baseados em nuvem aos quais o administrador do Azure AD concedeu acesso. 

- Autogerenciar grupos. Administradores podem criar e gerenciar grupos de segurança e solicitar associações ao grupo de segurança no Azure AD. Para saber mais, confira [Gerenciamento de grupo de autoatendimento para usuários no Azure AD](../users-groups-roles/groups-self-service-management.md) e [Gerenciar seus grupos](../fundamentals/active-directory-manage-groups.md).

## <a name="access-the-myapps-portal"></a>Acessar o portal do MyApps

Você pode acessar o portal do MyApps acessando `https://myapps.microsoft.com`.

Se tiver a identidade visual personalizada configurada para sua página de entrada, poderá carregar a identidade visual anexando o domínio da sua organização à URL (por exemplo, `https://myapps.microsoft.com/<your domain>.com`).

É possível usar qualquer nome de domínio ativo ou verificado que tenha sido configurado em seu portal do Azure, como mostrado aqui: ![Nome de domínio Wingtip Toys][2]  

Distribua a URL a todos os usuários que entram nos aplicativos integrados ao Azure AD.

## <a name="authentication"></a>Authentication

Para acessar o portal MyApps, você deve ser autenticado por meio de uma conta de trabalho ou escola no AD do Azure. Você pode ser autenticado no Azure AD diretamente. Como alternativa, se uma organização tiver configurado a federação usando o AD FS (Serviços de Federação do Active Directory) ou outras tecnologias, você pode ser autenticado pelo Windows Server Active Directory.

Se você tiver uma assinatura do Azure ou do Office 365 e estiver usando o Portal do Azure ou um aplicativo do Office 365, pode ver a lista de aplicativos sem entrar novamente. Se você não estiver autenticado, receberá uma solicitação para entrar usando o nome de usuário e a senha de sua conta no Azure AD. Se sua organização tiver configurado a federação, digitar o nome do usuário será suficiente.

Quando está autenticado, você pode interagir com os aplicativos integrados ao diretório pelo administrador. Para saber como integrar aplicativos ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure?](../manage-apps/what-is-single-sign-on.md).

## <a name="web-browser-requirements"></a>Requisitos de navegador da Web

No mínimo, o portal MyApps requer um navegador que suporte JavaScript e tenha CSS ativado. Para estar conectado aos aplicativos por meio de logon único (SSO) baseado em senha, você deve ter a extensão do portal MyApps instalada no navegador. A extensão é baixada automaticamente quando você seleciona um aplicativo configurado para SSO baseado em senha.

O instalador é específico da arquitetura. Se clicar no link de download, você só obterá o instalador para a arquitetura do sistema operacional em que está trabalhando no momento. Se você é um administrador de implantação de aplicativos, certifique-se de visitar o link de download de um dispositivo de 64 bits e 32 bits para obter os dois instaladores.


A extensão do portal MyApps está atualmente disponível para:
- **Microsoft Edge**: Edição de Aniversário do Windows 10 ou posterior. 
- **Chrome**: no Windows 7 ou posterior e no MacOS X ou posterior.
- **Firefox 26.0 ou posterior**: no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior.
- **Internet Explorer 11**: no Windows 7 ou posterior (suporte limitado).

## <a name="my-apps-secure-sign-in-extension"></a>Extensão de Entrada Segura dos Meus Aplicativos
Para entrar com logon único baseado em senha, você deve usar a extensão. Depois que a extensão estiver instalada, você pode entrar para habilitar recursos adicionais selecionando **Entrar para começar**. 

- Você pode entrar em um aplicativo diretamente usando a **URL de logon** do aplicativo. Quando você usa a URL do aplicativo, a extensão detecta a ação e oferece a opção de entrar a partir da extensão.
- Você pode iniciar qualquer um dos seus aplicativos do portal do MyApps usando o *pesquisa rápida* recurso da extensão. 
- A extensão mostra os três últimos aplicativos iniciados na seção **Usados Recentemente**.
- Você pode usar URLs internas da empresa ao remoto por meio de [Proxy de aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)

> [!NOTE]
> Recursos adicionais estão disponíveis apenas para Microsoft Edge, Chrome e Firefox.
> 
> Você pode baixar a extensão diretamente dos seguintes sites:
> - [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
> - [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
> - [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Caso esteja usando uma URL dos Meus Aplicativos diferente de `https://myapps.microsoft.com`, configure sua URL padrão fazendo o seguinte:
1. Enquanto você *não* estiver conectado à extensão, clique com o botão direito do mouse no ícone de extensão.
2. No menu, selecione **URL dos Meus aplicativos**.
3. Selecione a URL padrão.
4. Selecione o ícone da extensão.
5. Selecione **Entrar para começar**.

Para usar URLs internas da empresa ao remota usando a extensão, faça o seguinte:
1. [Configurar Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) no seu locatário
2. [Publicar o aplicativo](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) e a URL por meio do Application Proxy
3. Instale a extensão, conecte-se a ela selecionando Conectar-se para começar
4. Agora você pode navegar para a URL interna da empresa, mesmo em remoto

> [!NOTE]
> Você também pode desabilitar o redirecionamento automático para as URLs da empresa selecionando a engrenagem de configurações no menu principal e escolhendo **desabilitar** para a opção de redirecionamento de URL interna da empresa.


## <a name="mobile-app-support"></a>Suporte a aplicativos móveis

A equipe do Azure Active Directory publica o aplicativo móvel Meus Aplicativos. Quando você instala esse aplicativo, você pode entrar em aplicativos de SSO baseado em senha em dispositivos iOS e Android.

> [!NOTE]
> Você pode entrar em aplicativos que dão suporte à federação com o Azure AD (incluindo Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 e mais de 70 outros) em praticamente qualquer navegador da Web em qualquer dispositivo, sem a necessidade de um plug-in ou aplicativo móvel. A ser usado em um dispositivo móvel, o outro [experiências do portal do MyApps](https://myapps.microsoft.com/) também não exigem o aplicativo móvel meus aplicativos.

### <a name="my-apps-for-iphone-and-ipad"></a>Meus aplicativos para iPhone e iPad

O Meus Aplicativos para iOS tem suporte em qualquer iPhone ou iPad que esteja executando a versão 7 ou posterior do iOS.  

Está disponível na [Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Meus Aplicativos para iOS][4]    


## <a name="intune-managed-browser-for-my-apps"></a>Intune Managed Browser para meus aplicativos

O Meus Aplicativos também é integrado ao Intune Managed Browser. O Intune Managed Browser para dispositivos iOS e Android ajuda a exibir com mais segurança e navegar por páginas da Web que podem conter informações da empresa, ajudando a fornecer uma experiência de navegação na web mais segura.  

Você pode obter para meus aplicativos na página de início do Managed Browser e de seus indicadores, que significa que há menos cliques, necessários para alcançar seus aplicativos.

O Intune Managed Browser está disponível na [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) e na [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

![Navegador gerenciado para Meus Aplicativos][5]    


## <a name="tips-for-testing-the-user-experience"></a>Dicas para testar a experiência do usuário

Se você for um administrador do Azure e estiver conectado ao portal do Azure usando uma conta no diretório, estará automaticamente conectado ao portal MyApps como sua conta atual. Essa exibição mostra todos os aplicativos atribuídos a você.

Para testar uma conta de usuário *diferente*, faça o seguinte:

1. No canto superior direito do portal do Azure ou no portal MyApps, selecione **Sair**. 
2. Vá para o [portal do MyApps](https://myapps.microsoft.com).
3. Na página de entrada, digite o nome de usuário e a senha da conta no diretório que você deseja testar.


## <a name="starting-applications"></a>Iniciar aplicativos

Esta seção discute vários tipos de aplicativos que podem aparecer no portal MyApps.

### <a name="office-365-applications"></a>Aplicativos do Office 365

Se a sua organização estiver usando aplicativos do Office 365 e você estiver licenciado para eles, os aplicativos do Office 365 aparecerão no seu portal MyApps.

Ao selecionar um bloco do aplicativo para um aplicativo do Office 365, você é redirecionado para o aplicativo e conectado automaticamente.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Aplicativos da Microsoft e de terceiros configurados com o SSO baseado em federação

Seu administrador pode adicionar aplicativos na seção Active Directory do Portal do Azure com o modo SSO definido como **Logon único do Azure AD**. Você só poderá ver esses aplicativos se seu administrador tiver explicitamente lhe concedido acesso aos aplicativos.

Ao selecionar um bloco para um aplicativo, você é redirecionado e automaticamente conectado a ele.

### <a name="password-based-sso-without-identity-provisioning"></a>SSO baseado em senha sem provisionamento de identidade

Seu administrador pode adicionar aplicativos na seção Active Directory do Portal do Azure com o modo SSO definido como **Logon único do baseado em senha**. Todos os usuários no diretório podem ver todos os aplicativos configurados nesse modo.

Na primeira vez que você seleciona um bloco de aplicativo, recebe uma solicitação para instalar o plug-in do SSO de senha para o Internet Explorer ou Chrome. A instalação pode exigir você a reinicialização do seu navegador da Web. Quando você retornar ao portal MyApps e selecionar o bloco do aplicativo novamente, será solicitado um nome de usuário e uma senha para o aplicativo. Ao inserir o nome de usuário e senha, as credenciais são armazenadas com segurança e vinculadas à sua conta no Azure AD.

Na próxima vez que selecionar o bloco do aplicativo, você será automaticamente conectado ao aplicativo.  

Você não precisará inserir suas credenciais novamente e/ou instalar o plug-in do SSO de senha.

Se suas credenciais de um usuário forem alteradas no aplicativo de terceiros de destino, você também deverá atualizar suas credenciais armazenadas no Azure AD. 

Para atualizar suas credenciais, faça o seguinte:

1. Selecione o ícone no bloco do aplicativo.
2. Selecione **atualizar credenciais** para inserir novamente o nome de usuário e a senha para o aplicativo.


### <a name="password-based-sso-with-identity-provisioning"></a>SSO baseado em senha com provisionamento de identidade

Seu administrador pode adicionar aplicativos na seção Active Directory do Portal do Azure com o modo SSO definido como **Logon único baseado em senha**, junto com o provisionamento da identidade.

Na primeira vez que você seleciona um bloco de aplicativo, recebe uma solicitação para instalar o plug-in do SSO de senha para o Internet Explorer ou Chrome. A instalação pode exigir você a reinicialização do seu navegador da Web.  

Quando você retornar ao portal MyApps e selecionar o bloco do aplicativo novamente, será automaticamente conectado ao aplicativo.

Alguns aplicativos podem exigir que você altere sua senha na primeira entrada. Se suas credenciais de um usuário forem alteradas no aplicativo de terceiros de destino, você também deverá atualizar as credenciais armazenadas no Azure AD. 

Para atualizar suas credenciais, faça o seguinte:

1. Selecione o ícone no bloco do aplicativo.
2. Selecione **atualizar credenciais** para inserir novamente o nome de usuário e a senha para o aplicativo.


### <a name="application-with-existing-sso-solutions"></a>Aplicativos com soluções de SSO existentes

Para configurar o SSO para um aplicativo, o Portal do Azure fornece uma terceira opção chamada de Logon Único Existente. Essa opção permite que o administrador crie um link para um aplicativo e coloque-o no portal MyApps para usuários selecionados.

Por exemplo, se um aplicativo estiver configurado para autenticar usuários usando o AD FS 2.0, seu administrador poderá usar a opção Logon Único Existente para criar um link para ele no portal MyApps. Quando os você acessar o link, você é autenticado por meio do AD FS 2.0 ou por qualquer solução de SSO existente fornecida pelo aplicativo.


## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre gerenciamento de aplicativos, consulte [gerenciamento de aplicativos no Azure Active Directory](../manage-apps/what-is-application-management.md).
 
- Para saber como integrar um aplicativo SaaS no Azure AD, confira a [lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md).
 
- Para saber mais sobre como gerenciar aplicativos com o AD do Azure, consulte a [Introdução ao acesso ao aplicativo de gerenciamento e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
 
- Para obter mais informações sobre provisionamento de usuário, consulte [aplicativos SaaS para provisionamento e desprovisionamento automatizado do usuário](../manage-apps/user-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
