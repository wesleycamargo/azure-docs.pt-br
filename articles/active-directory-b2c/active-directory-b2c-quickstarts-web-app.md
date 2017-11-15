---
title: Fazer test drive de um aplicativo Web habilitado para o Azure AD B2C | Microsoft Docs
description: "Faça um test drive das jornadas do usuário para entrar, inscrever-se, editar o perfil e redefinir a senha usando um ambiente de teste do Azure AD B2C"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 2ffb780d-2c51-4c2e-b8d6-39c40a81a77e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: patricka
ms.openlocfilehash: 07f2c21409176d30f4570e267a4472745f843f85
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2017
---
# <a name="test-drive-an-azure-ad-b2c-enabled-web-app"></a>Fazer test drive de um aplicativo Web habilitado para o Azure AD B2C

O Azure Active Directory B2C fornece gerenciamento de identidades de nuvem para manter seu aplicativo, negócios e clientes protegidos. Este guia de início rápido usa um aplicativo de lista de tarefas pendentes de exemplo para demonstrar:

> [!div class="checklist"]
> * A entrada com uma página de logon personalizada.
> * A entrada usando um provedor de identidade social.
> * Criação e gerenciamento de sua conta e seu perfil do usuário do Azure AD B2C.
> * Chamada a uma API Web protegida pelo Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) com a carga de trabalho **ASP.NET e desenvolvimento para a Web**. 
* Uma conta social do Facebook, Google, Microsoft ou Twitter.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Baixar o exemplo

[Baixar ou clonar o aplicativo de exemplo](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) do GitHub.

## <a name="run-the-app-in-visual-studio"></a>Executar o aplicativo no Visual Studio

Na pasta de projeto do aplicativo de exemplo, abra a solução `B2C-WebAPI-DotNet.sln` no Visual Studio. 

A solução é um aplicativo de exemplo de lista de tarefas pendentes que consiste em dois projetos:

* **TaskWebApp** – Um aplicativo Web ASP.NET MVC no qual um usuário pode gerenciar seus itens de lista de tarefas pendentes.  
* **TaskService** – um back-end da API Web ASP.NET que gerencia as operações executadas nos itens de lista de tarefas pendentes de um usuário. O aplicativo Web chama essa API Web e exibe os resultados.

Para este início rápido, você precisará executar ambos os projetos `TaskWebApp` e `TaskService` ao mesmo tempo. 

1. No menu do Visual Studio, selecione **Projetos > Definir Projetos de Inicialização...**. 
2. Selecione o botão de opção **Vários projetos de inicialização**.
3. Altere a **Ação** para ambos os projetos para **Iniciar**. Clique em **OK**.

![Definir a página de inicialização no Visual Studio](media/active-directory-b2c-quickstarts-web-app/setup-startup-projects.png)

Selecione **Depurar > Iniciar depuração** para compilar e executar ambos os aplicativos. Cada aplicativo é aberto em sua própria guia do navegador:

`https://localhost:44316/` – Esta página é o aplicativo Web ASP.NET. Você interage diretamente com este aplicativo no início rápido.
`https://localhost:44332/` – Essa página é a API Web que é chamada pelo aplicativo Web ASP.NET.

## <a name="create-an-account"></a>Criar uma conta

Clique no link **Criar conta / Entrar** no aplicativo Web ASP.NET para iniciar o fluxo de trabalho **Criar conta ou entrar**. Ao criar uma conta, você poderá usar a conta de um provedor de identidade social existente ou uma conta de email. Para este guia de início rápido, use uma conta de provedor de identidade social do Facebook, do Google, da Microsoft ou do Twitter.

![Aplicativo Web ASP.NET de exemplo](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

### <a name="sign-up-using-a-social-identity-provider"></a>Inscrever-se usando um provedor de identidade social

Para inscrever-se usando um provedor de identidade social, clique no botão do provedor de identidade que você deseja usar. 

![Provedor de criar conta ou entrar](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

Você precisará autenticar-se (entrar) usando as credenciais da sua conta social e autorizar o aplicativo a ler as informações dessa conta. Ao conceder o acesso, o aplicativo poderá recuperar informações de perfil da conta social, tais como seu nome e cidade. 

Conclua o processo de entrada para o provedor de identidade. Por exemplo, se você escolher o Twitter, insira suas credenciais do Twitter e clique em **Entrar**.

![Autenticar e autorizar usando uma conta social](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

Os detalhes do perfil de sua nova conta do Azure AD B2C são pré-populados com as informações de sua conta social.

Atualize os campos Nome de exibição, Cargo e Cidade e clique em **Continuar**.  Os valores inseridos são usados seu perfil de conta de usuário do Azure AD B2C.

![Novos detalhes do perfil de criação de conta](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

Você teve êxito em:

> [!div class="checklist"]
> * Autenticar-se usando um provedor de identidade.
> * Criar uma conta de usuário do Azure AD B2C. 

## <a name="edit-your-profile"></a>Editar o perfil

O Azure Active Directory B2C fornece funcionalidade para permitir que usuários atualizem seus perfis. Na barra de menus do aplicativo Web, clique no nome do perfil e selecione **Editar perfil** para editar o perfil criado.

![Editar perfil](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

Altere seu **Nome de exibição** e **Cidade**.  Clique em **Continuar** para atualizar seu perfil.

![Atualizar perfil](media/active-directory-b2c-quickstarts-web-app/update-profile-web.png)

Observe que o nome de exibição na parte superior direita da página mostra o nome atualizado. 

## <a name="access-a-secured-web-api-resource"></a>Acessar um recurso seguro da API Web

Clique em **Lista de tarefas pendentes** para inserir e modificar os itens da lista de tarefas pendentes. O aplicativo Web ASP.NET inclui um token de acesso na solicitação para a recurso da API Web que solicita a permissão para executar operações em itens de lista de tarefas pendentes do usuário. 

Insira texto na caixa de texto **Novo Item**. Clique em **Adicionar** para chamar a API Web protegida do Azure AD B2C que adiciona um item de lista de tarefas pendentes.

![Adicionar um item de lista de tarefas pendentes](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

Você usou com êxito sua conta de usuário do Azure AD B2C para fazer uma chamada autorizada a uma API Web protegida pelo Azure AD B2C.

## <a name="next-steps"></a>Próximas etapas

A amostra usada neste guia de início rápido pode ser usada para testar outros cenários do Azure AD B2C, incluindo:

* Criação de uma nova conta local usando um endereço de email.
* Redefinição da senha de sua conta local.

Caso esteja pronto para se aprofundar na criação de seu próprio locatário do Azure AD B2C e configurar a amostra a ser executada usando seu próprio locatário, experimente o tutorial a seguir.

> [!div class="nextstepaction"]
> [Criar um aplicativo Web ASP.NET com inscrição, entrada, edição de perfil e redefinição de senha do Azure Active Directory B2C](active-directory-b2c-devquickstarts-web-dotnet-susi.md)