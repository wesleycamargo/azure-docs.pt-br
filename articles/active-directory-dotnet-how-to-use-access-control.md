<properties linkid="dev-net-how-to-access-control" urlDisplayName="Access Control" pageTitle="How to use Access Control (.NET) - Azure feature guide" metaKeywords="Azure Access Control Service authentication C#" description="Learn how to use Access Control Service (ACS) in your Azure application to authenticate users when they try to gain access to a web app." metaCanonical="" services="active-directory" documentationCenter=".NET" title="How to Authenticate Web Users with Azure Active Directory Access Control" authors="mbaldwin, juneb" solutions="" manager="mbaldwin" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="mbaldwin, juneb"></tags>

# Como autenticar usuários da Web com o Access Control do Active Directory do Azure

Este guia mostra como usar o Access Control do Active Directory do Azure (também conhecido como Access Control Service ou ACS) para autenticar usuários de provedores de identidade, como Microsoft, Google, Yahoo e Facebook quando eles tentarem obter acesso a um aplicativo da Web.

## <span class="short-header">Índice</span>índice

-   [O que é ACS?][]
-   [Conceitos][]
-   [Pré-requisitos][]
-   [Criar um espaço reservado do Access Control][]
-   [Criar um aplicativo ASP.NET MVC][]
-   [Integrar seu aplicativo Web com o ACS][]
-   [Testar a integração com o ACS][]
-   [Exibir declarações enviadas pelo ACS][]
-   [Exibir o aplicativo no Portal de Gerenciamento do ACS][]
-   [Adicionar um provedor de identidade][]
-   [E agora?][]

## <span class="short-header">O que é ACS?</span>o que é ACS?

A maioria dos desenvolvedores não são especialistas em identidade e não querem gastar tempo desenvolvendo a autenticação e mecanismos de autenticação para seus aplicativos e serviços. O ACS é um serviço do Azure que fornece uma maneira fácil para autenticar os usuários para acessar seus aplicativos da Web e serviços sem a necessidade de adicionar lógica de autenticação complexa em seu código.

Os seguintes recursos estão disponíveis no ACS:

-   Integração com o Windows Identity Foundation (WIF).
-   Suporte para provedores de identidade (IPs) populares da Web incluindo contas da Microsoft (anteriormente conhecidas como Windows Live ID), Google, Yahoo e Facebook.
-   Suporte para o Serviço de Federação do Active Directory (AD FS) 2.0.
-   Um serviço de gerenciamento baseado em Open Data Protocol (OData) que fornece
    acesso programático para configurações do ACS.
-   Um Portal de Gerenciamento que permite o acesso administrativo para as
    configurações do ACS.

Para obter mais informações sobre o ACS, consulte [Access Control Service 2.0][].

## <span class="short-header">Conceitos</span>Conceitos

O ACS baseia-se nos princípios de identidade baseada em declarações -- uma abordagem consistente para a criação de mecanismos de autenticação para aplicativos executados localmente ou na nuvem. Identidade baseada em declarações fornece uma maneira comum para aplicativos e serviços obter as informações de identidade necessárias sobre os usuários de sua organização, em outras organizações e na Internet.

Para concluir as tarefas deste guia, você deve entender os seguintes termos e conceitos usados neste guia:

**Cliente** - Um navegador que está tentando obter acesso ao seu aplicativo da Web.

**Aplicativos de terceiros confiável (RP)** - seu aplicativo da Web. Um aplicativo de RP é um site ou serviço que outsources a autenticação para uma autoridade externa. No jargão de identidade, dizemos que o RP confia essa autoridade. Este guia explica como configurar seu aplicativo para relação de confiança de ACS.

**Token** - um usuário obtém acesso a um aplicativo de RP, apresentando um token válido emitido por uma autoridade que o aplicativo da RP confia. Um conjunto de dados de segurança emitidos quando um cliente é autenticado. Ele contém um conjunto de declarações, que são atributos do usuário autenticado, como um identificador para uma função de usuário, nome do usuário ou idade. Um token é assinado digitalmente para que seu emissor pode ser identificado e seu conteúdo não possa ser alterado.

**Provedor de Identidade (IP)** - uma autoridade que autentica as identidades dos usuários e emite tokens de segurança, como conta da Microsoft (Windows Live ID), Facebook, Google, Twitter e Active Directory. Quando o ACS está configurado para confiar em um IP, ele aceita e valida os tokens que emite o IP. Como o ACS pode confiar vários IPs ao mesmo tempo, quando o seu aplicativo confia no ACS, é possível que seu aplicativo possa oferecer aos usuários a opção de ser autenticado por qualquer um dos IPs que o ACS confia em seu nome.

**Provedor de Federação (FP)** - provedores de identidade (IPs) têm conhecimento direto dos usuários, autenticam os usuários usando suas credenciais e emitem declarações sobre os usuários. Um Provedor de Federação (FP) é um tipo diferente de autoridade. Em vez de autenticar usuários diretamente, o FP intermédia a autenticação. Ele atua como um intermediário entre um aplicativo de terceiros confiável e um ou mais IPs. O ACS é um provedor de federação (FP).

**Mecanismo de Regras do ACS** - regras de transformação de declarações convertem as declarações em tokens de IPs confiáveis para que possam ser usadas por uma RP. O ACS inclui um mecanismo de regra que aplica as regras de transformação de declarações que você especificar para o RP.

**Namespace do Access Control** - Fornece um escopo exclusivo para endereçar recursos do ACS a partir do aplicativo. O namespace contém as configurações, como os IPs confiáveis, os aplicativos de RP que você deseja servir, as regras aplicáveis aos tokens de entrada, e exibe os pontos de extremidade do aplicativo e o desenvolvedor usa para se comunicar com o ACS.

A figura a seguir mostra como a autenticação do ACS funciona com um aplicativo da Web:

![][]

1.  O cliente (neste caso, um navegador) solicita uma página do RP.
2.  Como a solicitação ainda não foi autenticada, o RP redireciona o
    usuário para a autoridade que ele confia, que é o ACS. O ACS apresenta
    ao usuário a opção de IPs que foram especificados para essa RP. O
    usuário seleciona o IP apropriado.
3.  O cliente navega até a página de autenticação do IP e solicita ao
    usuário para fazer logon.
4.  Depois que o cliente é autenticado (por exemplo,
    as credenciais de identidade foram inseridas), o IP emite um token de segurança.
5.  Depois de emitir um token de segurança, o IP instrui o cliente para enviar o token de segurança emitido o IP ao ACS.
6.  O ACS valida o token de segurança emitido por IP, insere a
    declaração de identidade nesse token para o mecanismo de regras do ACS, calcula
    as declarações de identidade de saída e emite um novo token de segurança que
    contém essas declarações de saída.
7.  O ACS instrui o cliente a enviar o token de segurança que o ACS emitiu para o RP. O RP valida a assinatura no token de segurança, extrai declarações para uso com a lógica comercial do aplicativo e retorna a página originalmente solicitada.

## <span class="short-header">Pré-requisitos</span>Pré-requisitos

Para concluir as tarefas deste guia, você precisará do seguinte:

-   Assinatura do Azure
-   Microsoft Visual Studio 2012
-   Identidade e ferramenta de acesso para o Visual Studio 2012 (para baixar, consulte [Identidade e ferramenta de acesso][]

## <span class="short-header">Criar um Namespace do Access Control</span>Criar um Namespace do Access Control

Para começar a usar o Active Directory Access Control no Azure, crie um namespace do Access Control. O namespace fornece um escopo exclusivo para
endereçar recursos do ACS a partir do aplicativo.

1.  Faça logon no [Portal de Gerenciamento do Azure][](<https://manage.WindowsAzure.com>).

2.  Clique em **Active Directory**.

    ![][1]

3.  Para criar um novo namespace do Access Control, clique em **Novo** **Serviços de aplicativos** e **Access Control** será selecionado. Clique em **Criação Rápida**.

    ![][2]

4.  Insira um nome para o namespace. O Azure verifica se o nome é exclusivo.

5.  Selecione a região na qual o namespace é usado. Para obter melhor desempenho, use a região em que você estiver implantando seu aplicativo e clique em **Criar**.

O Windows Azure cria e ativa o namespace.

## <span class="short-header">Criar um aplicativo ASP.NET MVC</span>Criar um aplicativo ASP.NET MVC

Nesta etapa, você cria um aplicativo ASP.NET MVC. Em etapas posteriores, iremos integrar este aplicativo de formulários da Web simples com o ACS.

1.  Inicie o Visual Studio 2012 ou Visual Studio Express para Web 2012 (versões anteriores do Visual Studio não funcionarão com este tutorial).
2.  Clique em **Arquivo** e clique em **Novo Projeto**.
3.  Selecione Visual C#/modelo de Web e, em seguida, selecione **aplicativo da Web do ASP.NET MVC 4**

    Usaremos um aplicativo MVC para este guia, mas você pode usar qualquer tipo de aplicativo da Web para esta tarefa.

    ![][3]

4.  Em **Nome**, digite **MvcACS**, e, em seguida, clique em **OK**.
5.  Na próxima caixa de diálogo, selecione **Aplicativo da Internet** e clique em **OK**.
6.  Edite o arquivo *Views\\Shared\_LoginPartial.cshtml* e substitua o conteúdo pelo código a seguir:

        @if (Request.IsAuthenticated)
        {
            string name = "Null ID";
            if (!String.IsNullOrEmpty(User.Identity.Name))
            {
                name = User.Identity.Name;
            }    
            <text>
            Hello, @Html.ActionLink(name, "Manage", "Account", routeValues: null, htmlAttributes: new { @class = "username", title = "Manage" })!
                    @using (Html.BeginForm("LogOff", "Account", FormMethod.Post, new { id = "logoutForm" }))
                    {
                        @Html.AntiForgeryToken()
                        <a href="javascript:document.getElementById('logoutForm').submit()">Log off</a>
                    }
            </text>
        }
        else
        {
            <ul>
                <li>@Html.ActionLink("Register", "Register", "Account", routeValues: null, htmlAttributes: new { id = "registerLink" })</li>
                <li>@Html.ActionLink("Log in", "Login", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
            </ul>
        }

Atualmente, o ACS não define o User.Identity.Name, portanto, é necessário fazer a alteração acima.

1.  Pressione F5 para executar o aplicativo. O aplicativo ASP .NET MVC padrão é exibido no seu navegador da Web.

## <span class="short-header">Integrar seu aplicativo da Web com o ACS</span>integrar seu aplicativo da Web com o ACS

Esta tarefa, você integrará o aplicativo da Web ASP.NET com o ACS.

1.  No Explorador de Solução, clique com o botão direito no projeto MvcACS e selecione **Identidade e Acesso**.

    Se a opção **Identidade e Acesso** não aparece no menu de contexto, instale a Ferramenta de Identidade e Acesso. Para obter informações, consulte [Ferramenta de Identidade e Acesso][Identidade e ferramenta de acesso]

    ![][4]

2.  Na guia **Provedores**, selecione **Usar o Azure Access Control Service**.

    ![][5]

3.  Clique no link **Configurar**.

    ![][6]

    O Visual Studio solicita informações sobre o namespace do Access Control. Digite o nome do namespace criado anteriormente (teste nas imagens acima, mas você terá um namespace diferente). Alterne de volta para o Portal de Gerenciamento do Azure para obter a chave simétrica.

    ![][7]

4.  No Portal de Gerenciamento do Azure, clique o namespace do Access Control e, em seguida, clique em **Gerenciar**.

    ![][8]

5.  Clique em **Serviço de Gerenciamento** e, em seguida, clique em **Cliente de Gerenciamento**.

    ![][9]

6.  Clique em **Chave Simétrica**, clique em **Mostrar Chave** e copie o valor da chave. Em seguida, clique em **Cancelar** para sair da página Editar cliente de gerenciamento sem fazer alterações.

    ![][10]

7.  No Visual Studio, cole a chave no campo **Inserir a chave de gerenciamento para o namespace** e clique em **Salvar chave de gerenciamento** e em **OK**.

    ![][11]

    O Visual Studio usa as informações sobre o namespace para se conectar ao Portal de Gerenciamento do ACS e obter as configurações de seu namespace, incluindo os provedores de identidade, o território e a URL de retorno.

8.  Selecione **Windows Live ID** (conta da Microsoft) e clique em OK.

    ![][12]

## <span class="short-header">Testar a integração com o ACS</span>Testar a integração com o ACS

Esta tarefa explica como testar a integração do seu aplicativo de RP e o ACS.

-   Pressione F5 no Visual Studio para executar o aplicativo.

Quando seu aplicativo estiver integrado com o ACS e você selecionar o Windows Live ID (conta da Microsoft), em vez de abrir o aplicativo ASP.NET Web Forms padrão, seu navegador é redirecionado para a página de logon para contas da Microsoft. Quando você entrar com um nome de usuário válido e senha, você, você será redirecionado para o aplicativo MvcACS.

![][13]

Parabéns! Você integrou com êxito o ACS com o aplicativo da Web ASP.NET. O ACS agora está manipulando a autenticação de usuários usando suas credenciais de conta da Microsoft.

## <a name="bkmk_viewClaims"></a>Exibir declarações enviadas pelo ACS

Nesta seção, vamos modificar o aplicativo para exibir as declarações enviadas pelo ACS. A ferramenta Identidades e Acesso criou um grupo de regras que passa por todas as declarações de IP para o seu aplicativo. Observe que os diferentes provedores de identidade enviam declarações diferentes.

1.  Abra o arquivo *Controllers\\HomeController.cs*. Adicione uma declaração **usando** para **System.Threading**:

    usando o System.Threading;

2.  Na classe HomeController, adicione o método *Declarações*:

    público ActionResult Claims()
    {
     ViewBag.Message = "Sua página de declarações.";

        ViewBag.ClaimsIdentity = Thread.CurrentPrincipal.Identity;

        return View();

    }

3.  Clique com o botão direito do mouse no método *Declarações* e selecione **Adicionar Exibição**.

![][14]

1.  Clique em **Adicionar**.

2.  Substitua o conteúdo do arquivo *Views\\Home\\Claims.cshtml* pelo código a seguir:

        @{
            ViewBag.Title = "Claims";
        }
        <hgroup class="title">
            <h1>@ViewBag.Title.</h1>
            <h2>@ViewBag.Message</h2>
        </hgroup>
        <h3>Values from Identity</h3>
        <table>
            <tr>
                <td>
                    IsAuthenticated: 
                </td>
                <td>
                    @ViewBag.ClaimsIdentity.IsAuthenticated 
                </td>
            </tr>
            <tr>
                <td>
                    Name: 
                </td>        
                <td>
                    @ViewBag.ClaimsIdentity.Name
                </td>        
            </tr>
        </table>
        <h3>Claims from ClaimsIdentity</h3>
        <table>
            <tr>
                <th>
                    Claim Type
                </th>
                <th>
                    Claim Value
                </th>
            </tr>
                @foreach (System.Security.Claims.Claim claim in ViewBag.ClaimsIdentity.Claims ) {
            <tr>
                <td>
                    @claim.Type
                </td>
                <td>
                    @claim.Value
                </td>
            </tr>
        }
        </table>

3.  Execute o aplicativo e navegue para o método *Declarações*:

![][15]

Para obter mais informações sobre o uso de declarações em seu aplicativo, consulte [Biblioteca Windows Identity Foundation][].

## <a name="bkmk_VP"></a>Exibir o aplicativo no Portal de Gerenciamento do ACS

A ferramenta Identidade e Acesso no Visual Studio integra automaticamente seu aplicativo com o ACS.

Quando você seleciona a opção Usar o Azure Acesse Controle e executa o aplicativo, a ferramenta Identidade e Acesso adiciona seu aplicativo como uma parte confiante, configura para usar os provedores de identidade selecionados, gera e seleciona as regras de transformação de declarações padrão para o aplicativo.

Você pode revisar e alterar essas configurações no Portal de Gerenciamento do ACS. Use as etapas a seguir para revisar as alterações no portal.

1.  Faça logon no [Portal de Gerenciamento do Windows Azure][Portal de Gerenciamento do Azure].

2.  Clique em **Active Directory**.

    ![][8]

3.  Selecione um namespace do Access Control e clique em **Gerenciar**. Essa ação abre o Portal de Gerenciamento do ACS.

    ![][16]

4.  Clique em **Aplicativos de terceiros de confiança**

    O novo aplicativo MvcACS aparece na lista de aplicativos de terceiros de confiança. O território é definido automaticamente para a página principal do aplicativo.

    ![][17]

5.  Clique em **MvcACS**.

    A página Editar aplicativo de terceiros confiável contém definições do aplicativo da Web MvcACS. Quando você alterar as configurações desta página e salvar, elas são aplicadas imediatamente ao aplicativo.

    ![][18]

6.  Role a página para ver as configurações restantes para o aplicativo MvcACS, incluindo as regras de transformação de declarações e provedores de identidade.

    ![][19]

Na próxima seção, vamos usar os recursos do Portal de Gerenciamento do ACS para fazer uma alteração no aplicativo da Web -- apenas para mostrar como é fácil de fazer.

## <span class="short-header">Adicionar um provedor de identidade</span>Adicionar um provedor de identidade

Vamos usar o Portal de Gerenciamento do ACS para alterar a autenticação de nosso aplicativo MvcACS. Neste exemplo, vamos adicionar Google como um provedor de identidade para o MvcACS.

1.  Clique em **Provedores de identidade** (no menu de navegação) e em **Adicionar**.

    ![][20]

2.  Clique em **Google** e em seguida clique em **Avançar**. A caixa de seleção do aplicativo MvcACS está marcada por padrão.

    ![][21]

3.  Clique em Salvar.

    ![][22]

Feito! Se você voltar ao Visual Studio, abra o projeto para o aplicativo MvcACS e clique em **Identidade e Acesso**, a ferramenta lista os provedores de identidade do Windows Live ID e Google.

![][23]

Quando você executar o aplicativo, você verá o efeito. Quando um aplicativo oferecer suporte a mais de um provedor de identidade, o navegador do usuário é primeiro direcionado para uma página hospedada pelo ACS que solicita ao usuário para escolher um provedor de identidade.

![][24]

Após o usuário selecionar um provedor de identidade, o navegador vai para a página de entrada do provedor de identidade.

## <span class="short-header">E agora</span>E agora

Você criou um aplicativo Web que é integrado com o ACS. Mas isso é apenas o começo! Você pode expandir esse cenário.

Por exemplo, você pode adicionar mais provedores de identidade para essa RP ou permitir que os usuários registrados no diretórios corporativos, como o Active Directory Domain Services, façam o logon no aplicativo Web.

Você também pode adicionar regras para seu namespace que determinam quais declarações são enviadas para um aplicativo para processamento na lógica de negócios do aplicativo.

Para explorar melhor a funcionalidade do ACS e experimentar mais cenários, consulte [Access Control Service 2.0][].

  [O que é ACS?]: #what-is
  [Conceitos]: #concepts
  [Pré-requisitos]: #pre
  [Criar um espaço reservado do Access Control]: #create-namespace
  [Criar um aplicativo ASP.NET MVC]: #create-web-app
  [Integrar seu aplicativo Web com o ACS]: #Identity-Access
  [Testar a integração com o ACS]: #Test-ACS
  [Exibir declarações enviadas pelo ACS]: #bkmk_viewClaims
  [Exibir o aplicativo no Portal de Gerenciamento do ACS]: #bkmk_VP
  [Adicionar um provedor de identidade]: #add-IP
  [E agora?]: #whats-next
  [Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
  []: ./media/active-directory-dotnet-how-to-use-access-control/acs-01.png
  [Identidade e ferramenta de acesso]: http://go.microsoft.com/fwlink/?LinkID=245849
  [Portal de Gerenciamento do Azure]: http://manage.WindowsAzure.com
  [1]: ./media/active-directory-dotnet-how-to-use-access-control/acsCreateNamespace.png
  [2]: ./media/active-directory-dotnet-how-to-use-access-control/acsQuickCreate.png
  [3]: ./media/active-directory-dotnet-how-to-use-access-control/rzMvc.png
  [4]: ./media/active-directory-dotnet-how-to-use-access-control/rzIA.png
  [5]: ./media/active-directory-dotnet-how-to-use-access-control/rzPT.png
  [6]: ./media/active-directory-dotnet-how-to-use-access-control/rzC.png
  [7]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace.png
  [8]: ./media/active-directory-dotnet-how-to-use-access-control/acsClickManage.png
  [9]: ./media/active-directory-dotnet-how-to-use-access-control/acsManagementService.png
  [10]: ./media/active-directory-dotnet-how-to-use-access-control/acsShowKey.png
  [11]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace2.png
  [12]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndAccess1.png
  [13]: ./media/active-directory-dotnet-how-to-use-access-control/acsMSFTAcct.png
  [14]: ./media/active-directory-dotnet-how-to-use-access-control/rzAv.png
  [15]: ./media/active-directory-dotnet-how-to-use-access-control/rzCl.png
  [Biblioteca Windows Identity Foundation]: http://msdn.microsoft.com/pt-br/library/hh377151.aspx
  [16]: ./media/active-directory-dotnet-how-to-use-access-control/acsACSPortal.png
  [17]: ./media/active-directory-dotnet-how-to-use-access-control/acsRPPage.png
  [18]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP.png
  [19]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP2.png
  [20]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Idp.png
  [21]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Google.png
  [22]: ./media/active-directory-dotnet-how-to-use-access-control/acsSave-Google.png
  [23]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndA-after.png
  [24]: ./media/active-directory-dotnet-how-to-use-access-control/acsSignIn.png
