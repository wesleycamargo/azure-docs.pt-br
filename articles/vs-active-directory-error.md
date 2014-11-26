<properties title="Erro na Detec&ccedil;&atilde;o da Autentica&ccedil;&atilde;o" pageTitle="Erro na Detec&ccedil;&atilde;o da Autentica&ccedil;&atilde;o" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

### Erro na Detecção da Autenticação

Ao detectar o código de autenticação anterior, o assistente detectou um tipo de autenticação incompatível.

##### O que está sendo verificado?

O assistente tentou detectar versões do código de autenticação que foram configurados com versões anteriores do Visual Studio. Se você recebeu esse erro, significa que seu projeto contém um tipo de autenticação incompatível. O assistente detecta os seguintes tipos de autenticação de versões anteriores do Visual Studio:

-   Autenticação do Windows
-   Contas Individuais de Usuário
-   Contas organizacionais

Para detectar a Autenticação do Windows em um projeto MVC, o assistente procura o elemento `authentication` de seu arquivo **web.config**.

<pre class="prettyprint">
    &lt;configuration&gt;
        &lt;system.web&gt;
            &lt;authentication mode=&quot;Windows&quot; /&gt;
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Para detectar a Autenticação do Windows em um projeto da API da Web, o assistente procura o elemento `IISExpressWindowsAuthentication` do arquivo **.csproj** de seu projeto:

<pre class="prettyprint">
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            &lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;
        &lt;/PropertyGroup&gt;
    &lt;/Project&gt;
</pre>

Para detectar a autenticação de contas de usuário individual, o assistente procura o elemento de pacote de seu arquivo **Packages.config**.

<pre class="prettyprint">
    &lt;packages&gt;
        &lt;package id=&quot;Microsoft.AspNet.Identity.EntityFramework&quot; version=&quot;2.1.0&quot; targetFramework=&quot;net45&quot; /&gt;
    &lt;/packages&gt;
</pre>

Para detectar uma forma anterior de Autenticação de conta organizacional, o assistente procura o seguinte elemento **web.config**:

<pre class="prettyprint">
    &lt;configuration*gt;
        &lt;appSettings&gt;
            &lt;add key=&quot;ida:Realm&quot; value=&quot;***&quot; /&gt;
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Para alterar o tipo de autenticação, remova o tipo de autenticação incompatível e execute o assistente novamente.

Para obter mais informações, consulte [Cenários de autenticação para o Azure AD][Cenários de autenticação para o Azure AD].

  [Cenários de autenticação para o Azure AD]: http://msdn.microsoft.com/library/azure/dn499820.aspx
