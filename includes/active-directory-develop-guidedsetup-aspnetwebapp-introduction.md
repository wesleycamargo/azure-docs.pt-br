# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Adicionar a opção Entrar com uma Conta da Microsoft a um aplicativo Web ASP.NET

Este guia demonstra como implementar a opção Entrar com uma Conta da Microsoft usando uma solução ASP.NET MVC com um aplicativo tradicional baseado em navegador da Web usando o OpenID Connect. 

Ao final deste guia, seu aplicativo poderá aceitar conexões de contas pessoais (incluindo outlook.com, live.com e outras), bem como contas corporativas ou de estudante de qualquer empresa ou organização que foi integrada ao Azure Active Directory. 

> Este guia exige o Visual Studio 2015 Atualização 3 ou o Visual Studio 2017.  Ainda não tem?  [Baixar o Visual Studio 2017 gratuitamente](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Como funciona este guia

![Como funciona este guia](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.png)

Este guia se baseia no cenário em que um navegador acessa um site ASP.NET, solicitando a autenticação de um usuário por meio de um botão de conexão. Nesse cenário, a maior parte do trabalho de renderização da página da Web ocorre no lado do servidor.

## <a name="libraries"></a>Bibliotecas

Este guia usa as seguintes bibliotecas:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware que permite que um aplicativo use OpenIdConnect para autenticação|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware que permite que um aplicativo mantenha a sessão de usuário usando cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Permite que os aplicativos baseado em OWIN sejam executados no IIS usando o pipeline de solicitação do ASP.NET|

