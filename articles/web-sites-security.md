<properties title="Securing an Azure Website" pageTitle="Securing an Azure Website." description="Learn how to secure an Azure Website." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

## Protegendo um aplicativo Web em um Site do Azure

Um dos desafios do desenvolvimento de um aplicativo Web é como fornecer um dispositivo seguro e protegido para os clientes. Neste artigo, você saberá os recursos dos Sites do Azure que podem proteger o aplicativo Web.

> [WACOM.NOTE] Uma discussão completa das considerações sobre segurança para aplicativos Web está além do escopo deste documento. Como ponto de partida para mais diretrizes sobre como proteger aplicativos Web, consulte o [Open Web Application Security Project (OWASP)][Open Web Application Security Project (OWASP)], mais especificamente o [o projeto das 10 mais][o projeto das 10 mais], que lista as 10 principais falhas de segurança em aplicativos Web, conforme determinado pelos membros do OWASP.

### Sumário

-   [Proteger comunicação][Proteger comunicação]
-   [Proteger desenvolvimento][Proteger desenvolvimento]
-   [Próximas etapas][Próximas etapas]

## <a name="https"></a>Proteger comunicação

Se usar o nome de domínio **.azurewebsites.net** criado para o site, você poderá usar imediatamente o protocolo HTTPS, porque um certificado SSL é fornecido para todos os nomes de domínio* **.azurewebsites.net**. Se o site usar um [nome de domínio personalizado][nome de domínio personalizado], será possível carregar um certificado SSL para habilitar o protocolo HTTPS para o domínio personalizado.

Para obter mais informações, consulte [Habilitar protocolo HTTPS para um Site do Azure][Habilitar protocolo HTTPS para um Site do Azure].

### Impondo protocolo HTTPS

Os Sites do Azure *não* impõem o protocolo HTTPS. Os visitantes ainda podem acessar o site usando o protocolo HTTPS, o que pode expor informações confidenciais. Para impor o protocolo HTTPS, use o **Módulo de Reescrita de URL**. O Módulo de Reescrita de URL está incluído nos Sites do Azure e permite definir regras aplicadas a solicitações recebidas antes das solicitações serem entregues ao aplicativo. Ele pode ser usado em aplicativos escritos em qualquer linguagem de programação compatível com Sites do Azure.

> [WACOM.NOTE] Os aplicativos MVC do .NET devem usar o filtro [RequireHttps][RequireHttps] em vez da Reescrita de URL. Para obter mais informações sobre como usar RequireHttps, consulte [Implantar um aplicativo MVC 5 do ASP.NET em um Site do Azure][Implantar um aplicativo MVC 5 do ASP.NET em um Site do Azure].
>
> Para obter informações sobre o redirecionamento programático de solicitações usando outras linguagens de programação, consulte a documentação dessas tecnologias.

As regras de Reescrita de URL são definidas em um arquivo **web.config** armazenado na raiz do aplicativo. O exemplo a seguir contém uma regra de Reescrita de URL que força todo o tráfego de entrada a usar o protocolo HTTPS.

<a name="example"></a>**Web.Config de exemplo da Reescrita de URL**

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <rewrite>
          <rules>
            <rule name="Force HTTPS" enabled="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{HTTPS}" pattern="off" />
              </conditions>
              <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>

Essa regra funciona retornando um código de status de protocolo HTTP 301 (redirecionamento permanente) quando o usuário solicita uma página usando o protocolo HTTP. O 301 redireciona a solicitação para a mesma URL solicitada pelo visitante, mas substitui a parte do protocolo HTTP da solicitação com o protocolo HTTPS. Por exemplo, <HTTP://contoso.com> seria redirecionado para <HTTPS://contoso.com>.

> [WACOM.NOTE] Se estiver escrito em **Node.js**, **PHP**, **Python Django** ou **Java**, o aplicativo provavelmente não incluirá um arquivo web.config. Porém, **Node.js**, **Python Django** e **Java** efetivamente usam um web.config quando hospedados no Sites do Azure. Como o Azure cria o arquivo automaticamente durante o desenvolvimento, você jamais o vê. Se você incluir um como parte do aplicativo, ele substituirá o gerado automaticamente pelo Azure.

### .NET

Para aplicativos do .NET, modifique o aplicativo e adicione a seção **\<rewrite\>** do [exemplo][exemplo] à seção **\<system.WebServer\>**.

Se o arquivo web.config já incluir uma seção **\<rewrite\>**, adicione a **\<rule\>** do [exemplo][exemplo] como a primeira entrada na seção **\<rules\>**.

### PHP

Para aplicativos PHP, basta salvar o [exemplo][exemplo] como um arquivo web.config na raiz do aplicativo e reimplantar o aplicativo no Site do Azure.

### Node.js, Python Django e Java

Um arquivo web.config é criado automaticamente para aplicativos Node.js, Python Django e Java, caso ainda não forneçam um, mas ele só exista no servidor por ser criado durante a implantação. O arquivo gerado automaticamente contém configurações que informam ao Azure como hospedar o aplicativo.

Para recuperar e modificar o arquivo gerado automaticamente pelo Site, use as etapas a seguir.

1.  Baixe o arquivo usando o protocolo FTP (consulte [Carregando/baixando arquivos via protocolo FTP e coletando logs de diagnóstico][Carregando/baixando arquivos via protocolo FTP e coletando logs de diagnóstico]).

2.  Adicione-o à raiz do aplicativo.

3.  Adicione as regras de reescrita usando as informações a seguir.

    -   **Node.js e Python Django**

        O arquivo web.config gerado para os aplicativos Node.js e Python Django já terá uma seção **\<rewrite\>**, contendo entradas **\<rule\>** necessárias ao funcionamento apropriado do site. Para forçar o site a usar o protocolo HTTPS, adicione a **\<rule\>** do exemplo como a primeira entrada na seção **\<rules\>**. Isso forçará o protocolo HTTPS, deixando o restante das regras intacto.

    -   **Java**

        Como o arquivo web.config para aplicativos Java que usam o Apache Tomcat não contêm uma seção **\<rewrite\>**, você deve adicionar a seção **\<rewrite\>** do exemplo à seção **\<system.webServer\>**.

4.  Reimplantar o projeto (inclusive o web.config atualizado) no Azure

Depois que você implantar um web.config com uma regra de reescrita para forçar o protocolo HTTPS, ele deverá entrar em vigor imediatamente e redirecionar todas as solicitações para o protocolo HTTPS.

Para obter mais informações sobre o Módulo de Reescrita de URL do IIS, consulte a documentação [Reescrita de URL][Reescrita de URL].

## <a name="develop"></a>Proteger desenvolvimento

### Publicando configurações de publicação de perfis e publicação

Durante o desenvolvimento de aplicativos, a realização de tarefas de gerenciamento ou a automação de tarefas usando utilitários como **Visual Studio**, **Web Matrix**, **Azure PowerShell** ou a **interface de linha de comando de plataforma cruzada** do Azure, é possível usar um arquivo de *configurações de publicação* ou um *perfil de publicação*. Ambos autenticam você no Azure e devem ser protegidos para evitar o acesso não autorizado.

-   Um arquivo de **configurações de publicação** contém

    -   ID da assinatura do Azure

    -   Um certificado de gerenciamento que permite realizar tarefas de gerenciamento para a assinatura *sem precisar fornecer um nome de conta ou uma senha*.

-   Um arquivo de **perfil de publicação** contém

    -   Informações de publicação no Site do Azure

Se você usa um utilitário que utiliza configurações de publicação ou perfil de publicação, importe o arquivo que contém as configurações de publicação ou o perfil para o utilitário e **exclua** o arquivo. Se você precisar manter o arquivo para compartilhar com outros que trabalhem no projeto, por exemplo, armazene-o em um local seguro, como um diretório **criptografado** com permissões restritas.

Além disso, você deve se certificar de que as credenciais importadas sejam seguras. Por exemplo, o **Azure PowerShell** e a **interface de linha de comando de plataforma cruzada** armazenam informações importadas no **diretório base** (*~* em sistemas Linux ou OS X e */users/yourusername* em sistemas Windows.) Para ter segurança extra, convém **criptografar** esses locais usando as ferramentas de criptografia disponíveis para o sistema operacional.

### Definições de configuração e cadeias de conexão

É uma prática comum armazenar cadeias de conexão, credenciais de autenticação e outras informações confidenciais em arquivos de configuração. Infelizmente, esses cookies podem ser expostos no site ou pode haver check-in deles em um repositório público, expondo essas informações.

Os Sites do Azure permitem armazenar informações de configuração como parte do ambiente de tempo de execução dos Sites como **configurações do aplicativo** e **cadeias de conexão**. Os valores são expostos ao aplicativo durante o tempo de execução por meio de *variáveis do ambiente* na maioria das linguagens de programação. Para aplicativos do .NET, esses valores são injetados na configuração do .NET durante o tempo de execução.

**Configurações do aplicativo** e **cadeias de conexão** são configuráveis usando-se o portal de gerenciamento do Azure ou os utilitários como o PowerShell ou a interface de linha de comando de plataforma cruzada.

Para obter mais informações sobre configurações do aplicativo e cadeias de conexão, consulte [Configurando Sites][Configurando Sites].

### Protocolo FTPS

O Azure oferece acesso via protocolo FTP seguro ao sistema de arquivos para o site por meio do protocolo **FTPS**. Isso permite acessar com segurança o código do aplicativo no Site, bem como os logs de diagnóstico. O link do protocolo FTPS para o Site pode ser encontrado no **Painel** do site no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

Para obter mais informações sobre o protocolo FTPS, consulte [Protocolo FTPS][Protocolo FTPS].

## Próximas etapas

Para obter mais informações sobre a segurança da plataforma Azure, informações sobre como relatar um **incidente de segurança ou um abuso**, ou informar à Microsoft que você realizará **testes de penetração** no site, consulte a seção de segurança da [Central de Confiabilidade do Microsoft Azure][Central de Confiabilidade do Microsoft Azure].

Para obter mais informações sobre o arquivo **web.config** ou **applicationhost.config** nos Sites do Azure, consulte [Opções de configuração desbloqueadas nos Sites do Azure][Opções de configuração desbloqueadas nos Sites do Azure].

Para obter informações sobre como registrar em log informações de Sites do Azure, que podem ser úteis na detecção de ataques, consulte [Habilitar registro em log de diagnóstico][Habilitar registro em log de diagnóstico].

  [Open Web Application Security Project (OWASP)]: https://www.owasp.org/index.php/Main_Page
  [o projeto das 10 mais]: https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project
  [Proteger comunicação]: #https
  [Proteger desenvolvimento]: #develop
  [Próximas etapas]: #next
  [nome de domínio personalizado]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-custom-domain-name/
  [Habilitar protocolo HTTPS para um Site do Azure]: /pt-br/documentation/articles/web-sites-configure-ssl-certificate/
  [RequireHttps]: http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx
  [Implantar um aplicativo MVC 5 do ASP.NET em um Site do Azure]: /pt-br/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [exemplo]: #example
  [Carregando/baixando arquivos via protocolo FTP e coletando logs de diagnóstico]: http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx
  [Reescrita de URL]: http://www.iis.net/downloads/microsoft/url-rewrite
  [Configurando Sites]: /pt-br/documentation/articles/web-sites-configure/
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Protocolo FTPS]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
  [Central de Confiabilidade do Microsoft Azure]: /en-us/support/trust-center/security/
  [Opções de configuração desbloqueadas nos Sites do Azure]: http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/
  [Habilitar registro em log de diagnóstico]: /pt-br/documentation/articles/web-sites-enable-diagnostic-log/
