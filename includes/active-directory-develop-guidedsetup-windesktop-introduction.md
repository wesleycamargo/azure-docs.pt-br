# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Chamar a API do Microsoft Graph em um aplicativo da Área de Trabalho do Windows

Este guia demonstra como um aplicativo .NET nativo da Área de Trabalho do Windows (XAML) pode obter um token de acesso e chamar a API do Microsoft Graph ou outras APIs que exigem tokens de acesso por meio de um ponto de extremidade do Azure Active Directory v2.

Depois de concluir o guia, seu aplicativo poderá chamar uma API protegida que usa contas pessoais (incluindo o outlook.com, live.com e outros). O aplicativo também usará contas corporativas e de estudante de qualquer empresa ou organização que usa o Azure Active Directory.  

> [!NOTE] 
> O guia exige o Visual Studio 2015 Atualização 3 ou o Visual Studio 2017.  Não tem nenhuma dessas versões? [Faça o download do Visual Studio 2017 gratuitamente](https://www.visualstudio.com/downloads/).

## <a name="how-this-guide-works"></a>Como funciona este guia

![Como funciona este guia](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

O aplicativo de exemplo que você cria com este guia permite que um aplicativo da Área de Trabalho do Windows consulte a API do Microsoft Graph, ou uma API Web que aceita tokens, de um ponto de extremidade do Azure Active Directory v2. Para esse cenário, você adiciona um token às solicitações HTTP por meio do cabeçalho de Autorização. A MSAL (Biblioteca de Autenticação da Microsoft) lida com a aquisição e a renovação de tokens.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Manipulando a aquisição de token para acessar APIs Web protegidas

Após a autenticação do usuário, o aplicativo de exemplo recebe um token que pode ser usado para consultar a API do Microsoft Graph ou uma API Web protegida pelo Azure Active Directory v2.

APIs, como o Microsoft Graph, exigem um token para permitir o acesso a recursos específicos. Por exemplo, um token é necessário para ler o perfil ou acessar o calendário de um usuário, ou enviar um email. O aplicativo pode solicitar um token de acesso usando a MSAL para acessar esses recursos especificando escopos de API. Esse token de acesso é então adicionado ao cabeçalho de Autorização HTTP de cada chamada feita no recurso protegido. 

A MSAL gerencia o armazenamento em cache e a atualização de tokens de acesso para você, de forma que o aplicativo não precise fazer isso.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia usa os seguintes pacotes NuGet:

|Biblioteca|DESCRIÇÃO|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|MSAL (Biblioteca de Autenticação da Microsoft)|

