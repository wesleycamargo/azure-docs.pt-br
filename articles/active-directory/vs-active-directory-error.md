<properties 
	pageTitle="Erro na Detecção da Autenticação" 
	description="O Assistente de conexão do Active Directory detectou um tipo de autenticação incompatível" 
	services="active-directory" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="patshea"/>

# Erro na Detecção da Autenticação

Ao detectar o código de autenticação anterior, o assistente detectou um tipo de autenticação incompatível.

##O que está sendo verificado?

**Observação:** para detectar corretamente o código de autenticação anterior em um projeto, o projeto deve ser criado. Se este erro ocorrer e você não tiver o código de autenticação anterior em seu projeto, recrie o projeto e tente novamente.

###Tipos de projeto

O assistente verifica o tipo de projeto que você está desenvolvendo para que possa injetar a lógica de autenticação adequada ao projeto. Se houver qualquer controlador que deriva de `ApiController` no projeto, será considerado um projeto de API da Web. Se houver apenas os controladores que derivam de `MVC.Controller` no projeto, será considerado um projeto MVC. Qualquer outra coisa é considerada sem suporte pelo assistente. Atualmente, não há suporte para projetos de formulários da Web.

###Código de autenticação compatível

O assistente também verifica se as configurações de autenticação configuradas anteriormente com o assistente são compatíveis com ele. Se todas as configurações estiverem presentes, ele é considerado um caso reentrante e o assistente será aberto e exibirá as configurações. Se apenas algumas das configurações estiverem presentes, ele será considerado um caso de erro.

Em um projeto do MVC, o assistente verifica qualquer uma das configurações a seguir, fruto do uso anterior do assistente:

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:AADInstance" value="" />
	<add key="ida:PostLogoutRedirectUri" value="" />

Além disso, o assistente verifica qualquer uma das configurações a seguir em um projeto de API da Web, fruto do uso anterior do assistente:

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:Audience" value="" />

###Código de autenticação incompatível

Por fim, o assistente tenta detectar versões do código de autenticação que foram configuradas com versões anteriores do Visual Studio. Se você recebeu esse erro, significa que seu projeto contém um tipo de autenticação incompatível. O assistente detecta os seguintes tipos de autenticação de versões anteriores do Visual Studio:

* Autenticação do Windows 
* Contas Individuais de Usuário 
* Contas organizacionais 
 

Para detectar a Autenticação do Windows em um projeto MVC, o assistente procura pelo elemento `authentication` de seu arquivo **web.config**.

<pre>
	&lt;configuration>
	    &lt;system.web>
	        <span style="background-color: yellow">&lt;authentication mode="Windows" /></span>
	    &lt;/system.web>
	&lt;/configuration>
</pre>

Para detectar a Autenticação do Windows em um projeto da API da Web, o assistente procura pelo elemento `IISExpressWindowsAuthentication` do arquivo **.csproj** de seu projeto:

<pre>
	&lt;Project>
	    &lt;PropertyGroup>
	        <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication>habilitado&lt;/IISExpressWindowsAuthentication></span>
	    &lt;/PropertyGroup>
	&lt;/Project>
</pre>

Para detectar a autenticação de contas de usuário individual, o assistente procura o elemento de pacote de seu arquivo **Packages.config**.

<pre>
	&lt;packages>
	    <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
	&lt;/packages>
</pre>

Para detectar uma forma anterior de Autenticação de conta organizacional, o assistente procura o seguinte elemento **web.config**:

<pre>
	&lt;configuration>
	    &lt;appSettings>
	        <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /></span>
	    &lt;/appSettings>
	&lt;/configuration>
</pre>

Para alterar o tipo de autenticação, remova o tipo de autenticação incompatível e execute o assistente novamente.

Para obter mais informações, consulte [Cenários de autenticação para o Azure AD](active-directory-authentication-scenarios.md).
 

<!---HONumber=August15_HO7-->