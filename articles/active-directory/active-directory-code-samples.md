<properties
   pageTitle="Exemplos de código do Active Directory do Azure | Microsoft Azure"
   description="Um índice de exemplos de código do Active Directory do Azure, organizados por cenário."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/17/2015"
   ms.author="mbaldwin"/>

# Exemplos de código do Active Directory do Azure

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Você pode usar o Active Directory do Microsoft Azure (AD do Azure) para adicionar autenticação e autorização a aplicativos da Web e APIs da Web. Esta seção leva a exemplos de código que mostram como isso é feito, bem como trechos de código que você pode usar em seus aplicativos. Na página de exemplo de código, você encontrará tópicos de leitura detalhados que ajudam nos requisitos, instalação e configuração. E o código é comentado para ajudá-lo a entender as seções críticas.

Para entender o cenário básico para cada tipo de exemplo, consulte Cenários de Autenticação do Azure AD.

Contribua com nossos exemplos no GitHub: [Exemplos e documentação do Microsoft Azure Active Directory](https://github.com/AzureADSamples).

## Navegador da Web para o aplicativo Web

Estes exemplos mostram como escrever um aplicativo Web que direciona o navegador do usuário para entrar no AD do Azure.



| Linguagem/plataforma | Amostra | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [WebApp-OpenIDConnect-DotNet](http://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | Use o OpenID Connect (middleware ASP.Net OpenID Connect OWIN) para autenticar usuários de um locatário do Azure AD.
| C# / .NET | [WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet) | Um aplicativo Web .NET MVC multilocatário que usa o OpenID Connect (middleware ASP.Net OpenID Connect OWIN) para autenticar usuários de vários locatários do Azure AD.
| C# / .NET | [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | Use o WS-Federation (middleware ASP.Net WS-Federation OWIN) para autenticar usuários de um locatário do Azure AD.






## SPA (Aplicativo de Página Única)

Este exemplo mostra como escrever um aplicativo de página única protegido com o AD do Azure.

| Linguagem/plataforma | Amostra | Descrição
| ----------------- | ------ | -----------
| JavaScript, C#/.NET | [SinglePageApp-DotNet](https://github.com/AzureADSamples/SinglePageApp-DotNet) | Use o ADAL para JavaScript e o Azure AD para proteger um aplicativo de página única baseado em AngularJS implementado com um back-end da API da Web ASP.NET.


## Aplicativo nativo para API da Web

Estes exemplos de código mostram como compilar aplicativos cliente nativos que chamam APIs da Web protegidas pelo AD do Azure. Eles usam a [ biblioteca de autenticação do Azure AD (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) e o [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Linguagem/plataforma | Amostra | Descrição
| ----------------- | ------ | -----------
| JavaScript | [NativeClient-MultiTarget-Cordova](https://github.com/AzureADSamples/NativeClient-MultiTarget-Cordova) | Use o plug-in ADAL para Apache Cordova para compilar um aplicativo Apache Cordova que chama uma API da Web e usa o Azure AD para autenticação.
| C# / .NET | [NativeClient-DotNet](http://github.com/AzureADSamples/NativeClient-DotNet) | Um aplicativo .NET WPF que chama uma API da Web protegida usando o Azure AD.
| C# / .NET | [NativeClient-WindowsStore](http://github.com/AzureADSamples/NativeClient-WindowsStore) | Um aplicativo da Windows Store que chama uma API da Web protegida pelo Azure AD.
| C# / .NET | [NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore) | Um aplicativo da Windows Store que chama uma API da Web de multilocatários protegida pelo Azure AD.
| C# / .NET | [WebAPI-OnBehalfOf-DotNet](http://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet) | Um aplicativo cliente nativo que chama uma API da Web, a qual obtém um token para agir em nome do usuário original, e depois usa esse token para chamar outra API da Web.
| C# / .NET | [NativeClient-WindowsPhone8.1](https://github.com/AzureADSamples/NativeClient-WindowsPhone8.1) | Um aplicativo da Windows Store para Windows Phone 8.1 que chama uma API da Web protegida pelo Azure AD.
| ObjC | [NativeClient-iOS](http://github.com/AzureADSamples/NativeClient-iOS) | Um aplicativo iOS que chama uma API da Web que requer o Azure AD para autenticação.
| C# / .NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/AzureADSamples/WebAPI-ManuallyValidateJwt-DotNet) | Um aplicativo cliente nativo que inclui lógica para processar um token JWT em uma API da Web, em vez de usar o middleware OWIN.
| C#/Xamarin | [NativeClient-Xamarin-Android](https://github.com/AzureADSamples/NativeClient-Xamarin-Android) | Uma associação Xamarin para a ADAL (Biblioteca de Autenticação do Azure AD) para a biblioteca Android.
| C#/Xamarin | [NativeClient-Xamarin-iOS](http://github.com/AzureADSamples/NativeClient-Xamarin-iOS) | Uma associação Xamarin para a ADAL (Biblioteca de Autenticação do Azure AD) nativa para iOS.
| C#/Xamarin | [NativeClient-MultiTarget-DotNet](http://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet) | Um projeto Xamarin direcionado a cinco plataformas e que chama uma API da Web protegida pelo Azure AD.
| C# / .NET | [NativeClient-Headless-DotNet](http://github.com/AzureADSamples/NativeClient-Headless-DotNet) | Um aplicativo nativo que realiza a autenticação não interativa e chama uma API da Web protegida pelo Azure AD.



## Aplicativo Web para API da Web

Esses exemplos de código mostram como usar o [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) para compilar aplicativos Web que chamam APIs da Web protegidas pelo Azure AD.

| Linguagem/plataforma | Amostra | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [WebApp-WebAPI-OpenIDConnect-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNet) | Chame uma API da Web com as permissões do usuário conectado.
| C# / .NET | [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) | Chame uma API da Web com as permissões do aplicativo.
| C# / .NET | [WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-UserIdentity-Dotnet) | Adicione uma autorização com o [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) a um aplicativo Web existente para que ele possa chamar uma API da Web.
| JavaScript | [WebAPI-Nodejs](http://github.com/AzureADSamples/WebAPI-Nodejs) | Configure um serviço de API REST que é integrado ao Azure AD para proteção da API. Inclui um servidor Node.js com uma API da Web.
| C# / .NET | [WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet) | Um aplicativo Web MVC multilocatário que usa o OpenID Connect (middleware ASP.Net OpenID Connect OWIN) para autenticar usuários de um locatário do Azure AD. Usa um código de autorização para invocar a Graph API.

## Servidor ou aplicativo Daemon para API da Web

Esses exemplos de código mostram como compilar um aplicativo de daemon ou para servidores que obtém os recursos de uma API da Web usando a [ADAL (Biblioteca de Autenticação do Azure AD)](http://go.microsoft.com/fwlink/?LinkID=258232) e o [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Linguagem/plataforma | Amostra | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [Daemon-DotNet](http://github.com/AzureADSamples/Daemon-DotNet) | Um aplicativo de console chama uma API da Web. A credencial do cliente é uma senha.
| C# / .NET | [Daemon-CertificateCredential-DotNet](http://github.com/AzureADSamples/Daemon-CertificateCredential-DotNet) | Um aplicativo de console que chama uma API da Web. A credencial do cliente é um certificado.


## Chamando a Graph API do AD do Azure

Esses exemplos de código mostram como compilar aplicativos que chamam a Graph API do AD do Azure para ler e gravar dados no diretório.

| Linguagem/plataforma | Amostra | Descrição
| ----------------- | ------ | -----------
| Java | [WebApp-GraphAPI-Java](http://github.com/AzureADSamples/WebApp-GraphAPI-Java) | Um aplicativo Web que usa a Graph API para acessar os dados do diretório do Azure AD.
| PHP | [WebApp-GraphAPI-PHP](http://github.com/AzureADSamples/WebApp-GraphAPI-PHP) | Um aplicativo Web que usa a Graph API para acessar os dados do diretório do Azure AD.
| C# / .NET | [WebApp-GraphAPI-DotNet](http://github.com/AzureADSamples/WebApp-GraphAPI-DotNet) | Um aplicativo Web que usa a Graph API para acessar os dados do diretório do Azure AD.
| C# / .NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/AzureADSamples/ConsoleApp-GraphAPI-DotNet) | Esse aplicativo de console demonstra chamadas comuns de leitura e gravação para a Graph API, e mostra como executar a atribuição de licença de usuário e atualizar uma foto em miniatura do usuário e links.
| C# / .NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/AzureADSamples/ConsoleApp-GraphAPI-DiffQuery-DotNet) | Um aplicativo de console que utiliza a consulta diferencial na Graph API para obter alterações periódicas nos objetos de usuário em um locatário do Azure AD.
| C# / .NET | [WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/AzureADSamples/WebApp-GraphAPI-DirectoryExtensions-DotNet) | Um aplicativo MVC que usa consultas de Graph API para gerar um organograma comercial simples.
| PHP | [WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/AzureADSamples/WebApp-GraphAPI-DirectoryExtensions-PHP) | Um aplicativo PHP que chama a Graph API para registrar uma extensão e, em seguida, ler, atualizar e excluir valores no atributo de extensão.


## Autorização

Estes exemplos de código mostram como usar o AD do Azure para autorização.

| Linguagem/plataforma | Amostra | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) | Executa o RBAC (controle de acesso baseado em função) usando declarações de grupo do Azure Active Directory em um aplicativo que é integrado ao Azure AD.
| C# / .NET | [WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) | Executa o RBAC (controle de acesso baseado em função) usando funções de aplicativo do Azure Active Directory em um aplicativo que é integrado ao Azure AD.


## Passo a passo herdado

Essas orientações usam tecnologia ligeiramente mais antiga, mas ainda podem ser interessantes.

| Linguagem/plataforma | Amostra | Descrição
| ----------------- | ------ | -----------
| C# / .NET | [Autorização baseada em função e baseada em ACL em um aplicativo do Microsoft Azure AD](http://go.microsoft.com/fwlink/?LinkId=331694) | Execute RBAC (autorização baseada em função) e autorização baseada em ACL em um aplicativo que é integrado ao Azure AD.
| C# / .NET | [AAL - aplicativo da Windows Store para serviço REST - autenticação](http://go.microsoft.com/fwlink/?LinkId=330605) | Use a [ADAL (Biblioteca de Autenticação do Azure AD)](http://go.microsoft.com/fwlink/?LinkID=258232) (antigo AAL) para Windows Store Beta para adicionar recursos de autenticação de usuário para um aplicativo da Windows Store.
| C# / .NET | [ADAL - aplicativo nativo para serviço REST - autenticação com AAD via caixa de diálogo de navegador](http://go.microsoft.com/fwlink/?LinkId=259814) | Use a [ADAL (Biblioteca de Autenticação do Azure AD)](http://go.microsoft.com/fwlink/?LinkID=258232) para adicionar recursos de autenticação de usuário para um cliente WPF.
| C# / .NET | [ADAL - aplicativo nativo para serviço REST - autenticação com ACS via caixa de diálogo de navegador](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) | Use a [ADAL (Biblioteca de Autenticação do Azure AD)](http://go.microsoft.com/fwlink/?LinkID=258232) e o [ACS (Access Control Service 2.0)](http://msdn.microsoft.com/library/azure/hh147631.aspx) para adicionar recursos de autenticação de usuário para um cliente WPF.
| C# / .NET | [ADAL - Autenticação de servidor a servidor](http://go.microsoft.com/fwlink/?LinkId=259816) | Use a [ADAL (Biblioteca de Autenticação do Azure AD)](http://go.microsoft.com/fwlink/?LinkID=258232) para proteger chamadas de serviço de um processo do lado do servidor para um serviço REST da API da Web MVC4.
| C# / .NET | [Adicionando logon ao seu aplicativo Web usando o Azure AD](https://msdn.microsoft.com/library/azure/dn151790.aspx) | Configure um aplicativo .NET para executar logon único da Web em seu diretório comercial do Azure AD.
| C# / .NET | [Desenvolvendo aplicativos Web multilocatários com o Azure AD](https://msdn.microsoft.com/library/azure/dn151789.aspx) | Use o Azure AD para adicionar recursos de logon único e de acesso de diretório de um aplicativo .NET para funcionar em várias organizações.
JAVA | [Aplicativo de exemplo Java para Graph API Azure AD](http://go.microsoft.com/fwlink/?LinkId=263969) | Use a Graph API para acessar dados do diretório a partir do Azure AD.
PHP | [Aplicativo de exemplo PHP para Graph API do Azure AD](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Use a Graph API para acessar dados do diretório a partir do Azure AD.
| C# / .NET | [Aplicativo de exemplo para Graph API do Azure AD](http://go.microsoft.com/fwlink/?LinkID=262648) | Use a Graph API para acessar dados do diretório a partir do Azure AD.
| C# / .NET | [Aplicativo de exemplo para consulta diferencial gráfica do Azure AD](http://go.microsoft.com/fwlink/?LinkId=275398) | Use a consulta diferencial na Graph API para obter alterações periódicas nos objetos de usuário em um locatário do Azure AD.
| C# / .NET | [Aplicativo de exemplo para integrar aplicativos multilocatários em nuvem para o Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) | Integre um aplicativo multilocatário no Azure AD.
| C# / .NET | [Proteger um aplicativo da Windows Store e o serviço Web REST usando o Azure AD (visualização)](https://msdn.microsoft.com/library/azure/dn169448.aspx) | Crie um recurso de API da Web simples e um aplicativo de cliente da Windows Store usando o Azure AD e a [ADAL (Biblioteca de Autenticação do Azure AD)](http://go.microsoft.com/fwlink/?LinkID=258232).
| C# / .NET| [Usando a Graph API para consultar o Azure AD](https://msdn.microsoft.com/library/azure/dn151791.aspx) | Configure um aplicativo Microsoft .NET para usar a Graph API do Azure AD para acessar dados de um diretório de locatário do Azure AD.

## Consulte também

##### Outros recursos

[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

[Biblioteca auxiliar da Graph API do Azure AD](http://code.msdn.microsoft.com/Windows-Azure-AD-Graph-API-a8c72e18)

[Desenvolvendo aplicativos modernos com OAuth e Serviços de Federação do Active Directory](http://msdn.microsoft.com/library/dn633593.aspx)

<!---HONumber=Sept15_HO4-->