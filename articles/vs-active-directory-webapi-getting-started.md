<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### O que aconteceu?

Referências foram adicionadas ao seu projeto

###### Referências do pacote NuGet

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin e System.IdentityModel.Tokens.Jwt

###### Referências .NET

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin e System.IdentityModel.Tokens.Jwt

###### Arquivos de código foram adicionados ao seu projeto

Uma classe de inicialização de autenticação, a App\_Start/Startup.Auth.cs, foi adicionada ao seu projeto contendo lógica de inicialização para autenticação do Azure AD.

###### O código de inicialização foi adicionado ao seu projeto

Se você já possuía uma classe Startup no projeto, o método Configuration() foi atualizado para incluir uma chamada para ConfigureAuth(app) e adicionado ao método. Caso contrário, uma classe Startup foi adicionada ao projeto.

###### Seu arquivo app.config ou web.config possui novos valores de configuração.

As entradas de configuração a seguir foram adicionadas.

	<pre>
	`<appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings>` </pre>

### Foi criado um aplicativo do Azure Active Directory (AD)

Um Aplicativo Azure AD foi criado no diretório selecionado no assistente.

### Introdução ao Azure Active Directory (AD)

Veja o que você pode fazer com o código que foi adicionado.

###### Exigir autenticação para acessar os controladores

Todos os controladores do seu projeto receberam o atributo Authorize. Este atributo exigirá que o usuário seja autenticado antes de acessar as APIs definidas por esses controladores. Para permitir que o controlador seja acessado anonimamente, remova este atributo do controlador. Se desejar definir as permissões em um nível mais granular, aplique o atributo a cada método que necessita de autorização em vez de aplicá-lo à classe do controlador.

