<properties 
   pageTitle="Exemplos de código do Active Directory do Azure" 
   description="Um índice de exemplos de código do Active Directory do Azure, organizados por cenário." 
   services="active-directory" 
   documentationCenter="dev-center-name" 
   authors="msmbaldwin" 
   manager="mbaldwin" 
   editor=""/>

<tags
   ms.service="azure"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity" 
   ms.date="02/20/2015"
   ms.author="mbaldwin"/>

# Exemplos de código do Active Directory do Azure 


Você pode usar o Active Directory do Microsoft Azure (AD do Azure) para adicionar autenticação e autorização a aplicativos da Web e APIs da Web. Esta seção leva a exemplos de código que mostram como isso é feito, bem como trechos de código que você pode usar em seus aplicativos. Na página de exemplo de código, você encontrará tópicos de leitura detalhados que ajudam nos requisitos, instalação e configuração. E o código é comentado para ajudá-lo a entender as seções críticas.

Para entender o cenário básico para cada tipo de exemplo, consulte Cenários de Autenticação do Azure AD.

Contribua com nossos exemplos no GitHub: [Exemplos e documentação do Active Directory do Microsoft Azure](https://github.com/AzureADSamples).

## Navegador da Web para o aplicativo Web 

Estes exemplos mostram como escrever um aplicativo Web que direciona o navegador do usuário para entrar no AD do Azure.



| Linguagem/Plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-OpenIDConnect-DotNet](http://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | Usa OpenID Connect (ASP.Net OpenID Connect OWIN middleware) para autenticar usuários de um locatário AD do Azure.
| C#/.NET | [WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet) | Um aplicativo Web .NET MVC multilocatário que usa o OpenID Connect (ASP.Net OpenID Connect OWIN middleware) para autenticar usuários de vários locatários do AD do Azure.
| C#/.NET | [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | Usar Web Services Federation(ASP.Net WS-Federation OWIN middleware) para autenticar usuários de um locatário do AD do Azure.






## SPA (Aplicativo de Página Única)

Este exemplo mostra como escrever um aplicativo de página única protegido com o AD do Azure.  

| Linguagem/Plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| JavaScript, C#/.NET | [SinglePageApp-DotNet](https://github.com/AzureADSamples/SinglePageApp-DotNet) | Usa ADAL para JavaScript e o AD do Azure para proteger um aplicativo de página única baseado em AngularJS implementado com back-end da API Web ASP.NET.


## Aplicativo nativo para API da Web
 
Estes exemplos de código mostram como compilar aplicativos cliente nativos que chamam APIs da Web protegidas pelo AD do Azure. Eles usam a [Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) e o [OAuth 2.0 no AD do Azure](https://msdn.microsoft.com/library/azure/dn645545.aspx).
 
| Linguagem/Plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C#/.NET | [NativeClient-DotNet](http://github.com/AzureADSamples/NativeClient-DotNet) | Um aplicativo .NET WPF que chama uma API da Web protegida usando o AD do Azure.
| C#/.NET | [NativeClient-WindowsStore](http://github.com/AzureADSamples/NativeClient-WindowsStore) | Um aplicativo Windows Store que chama a API da Web protegida com o AD do Azure.
| C#/.NET | [NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore) | Um aplicativo Windows Store que chama uma API da Web multilocatário protegida com o AD do Azure.
| C#/.NET | [WebAPI-OnBehalfOf-DotNet](http://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet) | Um aplicativo cliente nativo que chama uma API da Web, a qual obtém um token para agir em nome do usuário original, usando então este token para chamar outra API da Web.
| C#/.NET | [NativeClient-WindowsPhone8.1](https://github.com/AzureADSamples/NativeClient-WindowsPhone8.1) | Um aplicativo Windows Store para Windows Phone 8.1 que chama a API da Web protegida pelo AD do Azure.
| ObjC | [NativeClient-iOS](http://github.com/AzureADSamples/NativeClient-iOS) | Um aplicativo iOS que chama uma API da Web que requer o AD do Azure para autenticação.
| C#/.NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/AzureADSamples/WebAPI-ManuallyValidateJwt-DotNet) | Um aplicativo cliente nativo que inclui lógica para processar um token JWT em uma API da web, em vez de usar o middleware OWIN.
| C#/Xamarin | [NativeClient-Xamarin-Android](https://github.com/AzureADSamples/NativeClient-Xamarin-Android) | Uma associação Xamarin para a ADAL (Biblioteca de Autenticação Nativa do AD do Azure) para a biblioteca Android.
| C#/Xamarin | [NativeClient-Xamarin-iOS](http://github.com/AzureADSamples/NativeClient-Xamarin-iOS) | Uma associação Xamarin para a ADAL (Biblioteca de Autenticação Nativa do AD do Azure) para a iOs.
| C#/Xamarin | [NativeClient-MultiTarget-DotNet](http://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet) | Um projeto Xamarin direcionado a cinco plataformas e que chama uma API da Web protegida pelo AD do Azure.
| C#/.NET | [NativeClient-Headless-DotNet](http://github.com/AzureADSamples/NativeClient-Headless-DotNet) | Um aplicativo nativo que realiza a autenticação não interativa e chama uma API da web protegida pelo AD do Azure.

   

## Aplicativo Web para API da Web

Estes exemplos de código mostram como usar o [OAuth 2.0 no AD do Azure](https://msdn.microsoft.com/library/azure/dn645545.aspx) para compilar aplicativos Web que chamam APIs da Web protegidas pelo AD do Azure.

| Linguagem/Plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-WebAPI-OpenIDConnect-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNet) | Chama uma API da Web com permissões do usuário conectado.
| C#/.NET | [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) | Chama uma API da Web com as permissões do aplicativo.
| C#/.NET | [WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-UserIdentity-Dotnet) | Adiciona autorização com [OAuth 2.0 no AD do Azure](https://msdn.microsoft.com/library/azure/dn645545.aspx) a um aplicativo Web existente para que ele possa chamar uma API da Web.
| JavaScript | [WebAPI-Nodejs](http://github.com/AzureADSamples/WebAPI-Nodejs) | Configura um serviço de API REST que é integrado ao AD do Azure para proteção de API. Inclui um servidor Node.js com uma API da Web.
| C#/.NET | [WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet) | Um aplicativo Web .NET MVC multilocatário que usa o OpenID Connect (ASP.Net OpenID Connect OWIN middleware) para autenticar usuários de um locatário do AD do Azure. Usa um código de autorização para invocar a Graph API.

## Servidor ou aplicativo Daemon para API da Web

Estes exemplos de código mostram como compilar um aplicativo de daemon ou para servidores que obtém os recursos de uma API da Web usando a [ADAL (Biblioteca de Autenticação do AD do Azure)](http://go.microsoft.com/fwlink/?LinkID=258232) e o [OAuth 2.0 no AD do Azure](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Linguagem/Plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C#/.NET | [Daemon-DotNet](http://github.com/AzureADSamples/Daemon-DotNet) | Um aplicativo de console chama uma API da Web. A credencial do cliente é uma senha.
| C#/.NET | [Daemon-CertificateCredential-DotNet](http://github.com/AzureADSamples/Daemon-CertificateCredential-DotNet) | Um aplicativo de console chama uma API da Web. A credencial do cliente é um certificado.


## Chamando a Graph API do AD do Azure

Esses exemplos de código mostram como compilar aplicativos que chamam a Graph API do AD do Azure para ler e gravar dados no diretório.

| Linguagem/Plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| Java | [WebApp-GraphAPI-Java](http://github.com/AzureADSamples/WebApp-GraphAPI-Java) | Um aplicativo Web que usa a Graph API para acessar os dados do diretório do AD do Azure.
| PHP | [WebApp-GraphAPI-PHP](http://github.com/AzureADSamples/WebApp-GraphAPI-PHP) | Um aplicativo Web que usa a Graph API para acessar os dados do diretório do AD do Azure.
| C#/.NET | [WebApp-GraphAPI-DotNet](http://github.com/AzureADSamples/WebApp-GraphAPI-DotNet) | Um aplicativo Web que usa a Graph API para acessar os dados do diretório do AD do Azure.
| C#/.NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/AzureADSamples/ConsoleApp-GraphAPI-DotNet) | Esse aplicativo de console demonstra chamadas comuns de leitura e gravação para a Graph API, bem como executar atribuição de licença de usuário e atualizar uma foto em miniatura de usuário e links.
| C#/.NET | [ConsoleApp-GraphAPI-DotNet-DiffQuery](https://github.com/AzureADSamples/ConsoleApp-GraphAPI-DiffQuery-DotNet) | Um aplicativo de console que utiliza a consulta diferencial na Graph API para obter alterações periódicas nos objetos de usuário em um locatário do AD do Azure.
| C#/.NET | [WebApp-GraphAPI-DotNet-DirectoryExtensions](https://github.com/AzureADSamples/WebApp-GraphAPI-DirectoryExtensions-DotNet)| Um aplicativo MVC que usa consultas de Graph API para gerar uma organograma organizacional simples.
| PHP | [WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/AzureADSamples/WebApp-GraphAPI-DirectoryExtensions-PHP) | Um aplicativo PHP que chama a Graph API para registrar uma extensão e, em seguida, ler, atualizar e excluir valores no atributo de extensão.


## Autorização

Estes exemplos de código mostram como usar o AD do Azure para autorização.

| Linguagem/Plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) | Executa o controle de acesso baseado em função (RBAC) usando declarações de grupo do Active Directory do Azure em um aplicativo que é integrado ao AD do Azure.
| C#/.NET | [WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) | Executa o controle de acesso baseado em função (RBAC) usando funções do aplicativo do Active Directory do Azure em um aplicativo que é integrado ao AD do Azure.


## Passo a passo herdado

Essas orientações usam tecnologia ligeiramente mais antiga, mas ainda podem ser interessantes.

| Linguagem/Plataforma | Exemplo | Descrição
| ----------------- | ------ | -----------
| C#/.NET | [Autorização baseada em função e em ACL em um aplicativo do AD do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=331694) | Executa a autorização baseada em função (RBAC) e a autorização baseada em ACL em um aplicativo que é integrado ao AD do Azure.
| C#/.NET | [AAL - aplicativo da Windows Store para serviço REST - Autenticação](http://go.microsoft.com/fwlink/?LinkId=330605) | Usa a [ADAL (Biblioteca de Autenticação do AD do Azure)](http://go.microsoft.com/fwlink/?LinkID=258232) (antigo AAL) para Windows Store Beta para adicionar recursos de autenticação de usuário para um aplicativo da Windows Store.
| C#/.NET | [ADAL - aplicativo nativo para serviço REST - Autenticação com AAD via caixa de diálogo do Navegador](http://go.microsoft.com/fwlink/?LinkId=259814) | Usa a [ADAL (Biblioteca de Autenticação do AD do Azure)](http://go.microsoft.com/fwlink/?LinkID=258232) para adicionar recursos de autenticação de usuário para um cliente WPF.
| C#/.NET | [ADAL - aplicativo nativo para serviço REST - Autenticação com ACS via caixa de diálogo do navegador](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) | Usa a [ADAL (Biblioteca de Autenticação do AD do Azure)](http://go.microsoft.com/fwlink/?LinkID=258232) e [ACS (Serviço de Controle de Acesso 2.0)](http://msdn.microsoft.com/library/azure/hh147631.aspx) para adicionar recursos de autenticação de usuário para um cliente WPF.
| C#/.NET | [ADAL - autenticação de servidor para servidor](http://go.microsoft.com/fwlink/?LinkId=259816) | Usa [ADAL (Biblioteca de Autenticação do AD do Azure)](http://go.microsoft.com/fwlink/?LinkID=258232) para proteger chamadas de serviço de um processo do lado servidor para um serviço REST da API da Web MVC4.
| C#/.NET | [Adicionando logon no seu aplicativo Web usando o AD do Azure](https://msdn.microsoft.com/library/azure/dn151790.aspx) | Configurar um aplicativo .NET para executar logon único da Web em seu diretório de empresa do AD do Azure.
| C#/.NET | [Desenvolvendo aplicativos Web multilocatário com o AD do Azure](https://msdn.microsoft.com/library/azure/dn151789.aspx) | Usa o AD do Azure para adicionar o logon único e recursos de acesso de diretório de aplicativos .NET para trabalhar em várias organizações.
JAVA | [Aplicativo de exemplo Java para Graph API do AD do Azure](http://go.microsoft.com/fwlink/?LinkId=263969)| Usa a Graph API para acessar dados de diretório do AD do Azure.
PHP | [Aplicativo de exemplo PHP para Graph API do AD do Azure](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb)| Usa a API Graph para acessar dados de diretório do AD do Azure.
| C#/.NET | [Aplicativo de exemplo para a Graph API do AD do Azure](http://go.microsoft.com/fwlink/?LinkID=262648) | Usa a Graph API para acessar dados de diretório do AD do Azure.
| C#/.NET | [Aplicativo de exemplo consulta diferencial do Graph do AD do Azure ](http://go.microsoft.com/fwlink/?LinkId=275398)| Usa a consulta diferencial na Graph API para obter mudanças periódicas para objetos de usuário em um locatário do AD do Azure.
| C#/.NET | [Aplicativo de exemplo para integração do aplicativo multilocatário em nuvem para AD do Azure](http://go.microsoft.com/fwlink/?LinkId=275397)| Integra um aplicativo multilocatário no AD do Azure.
| C#/.NET | [Protegendo um aplicativo da Windows Store e serviço Web REST usando o AD do Azure (Visualização)](https://msdn.microsoft.com/library/azure/dn169448.aspx) | Cria um recurso de API da Web simples e um aplicativo de cliente da Windows Store usando o AD do Azure e a [ADAL (Biblioteca de Autenticação do AD do Azure)](http://go.microsoft.com/fwlink/?LinkID=258232).
| C#/.NET| [Usando a Graph API para consultar o AD do Azure](https://msdn.microsoft.com/library/azure/dn151791.aspx) | Configura um aplicativo Microsoft .NET para usar o a Graph API do AD do Azure para acessar dados de um diretório de locatário do AD do Azure.

## Consulte também

##### Outros recursos


[Biblioteca Auxiliar da Graph API do AD do Azure](http://code.msdn.microsoft.com/Windows-Azure-AD-Graph-API-a8c72e18) 

[Desenvolvendo aplicativos modernos com OAuth e Serviços de Federação do Active Directory](http://msdn.microsoft.com/library/dn633593.aspx)




<!--HONumber=47--> 
