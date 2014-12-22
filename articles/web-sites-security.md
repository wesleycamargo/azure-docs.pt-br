<properties title="Securing an Azure Website" pageTitle="Protegendo um site do Azure." description="Learn how to secure an Azure Website." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />


#Protegendo um aplicativo Web em um Site do Azure

Um dos desafios do desenvolvimento de um aplicativo Web é como fornecer um dispositivo seguro e protegido para os clientes. Neste artigo, você saberá os recursos dos sites do Azure que podem proteger o aplicativo Web.

> [WACOM.NOTE] Uma discussão completa das considerações sobre segurança para aplicativos Web está além do escopo deste documento. Como ponto de partida para mais diretrizes sobre como proteger aplicativos Web, consulte o [Open Web Application Security Project (OWASP)]( https://www.owasp.org/index.php/Main_Page), mais especificamente o [projeto das 10 mais](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), que lista as 10 principais falhas de segurança em aplicativos Web, conforme determinado pelos membros do OWASP.

###Sumário

* [Proteger comunicação](#https)
* [Proteger desenvolvimento](#develop)
* [Próximas etapas](#next)
 
##<a name="https"></a>Proteger comunicação

Se usar o nome de domínio ***.azurewebsites.net** criado para o site, você poderá usar imediatamente o protocolo HTTPS, porque um certificado SSL é fornecido para todos os nomes de domínio ***.azurewebsites.net**. Se o site usar um [nome de domínio personalizado](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-custom-domain-name/), será possível carregar um certificado SSL para habilitar o protocolo HTTPS para o domínio personalizado.

##<a name="develop"></a>Proteger desenvolvimento 

###Publicando configurações de publicação de perfis e publicação

Durante o desenvolvimento de aplicativos, a realização de tarefas de gerenciamento ou a automação de tarefas usando utilitários como **Visual Studio**, **Web Matrix**, **Azure PowerShell** ou a **interface de linha de comando entre plataformas** do Azure, é possível usar um arquivo *publish settings* ou *publishing profile*. Ambos autenticam você no Azure e devem ser protegidos para evitar o acesso não autorizado.

* Um arquivo de **configurações de publicação** contém

	* ID da assinatura do Azure

	* Um certificado de gerenciamento que permite realizar tarefas de gerenciamento para a assinatura *sem precisar fornecer um nome de conta ou uma senha*.

* Um arquivo de **perfil de publicação** contém

	* Informações de publicação no Site do Azure

Se você usa um utilitário que utiliza configurações de publicação ou perfil de publicação, importe o arquivo que contém as configurações de publicação ou o perfil para o utilitário e **exclua** o arquivo. Se você precisar manter o arquivo para compartilhar com outros que trabalhem no projeto, por exemplo, armazene-o em um local seguro, como um diretório **criptografado** com permissões restritas.

Além disso, você deve se certificar de que as credenciais importadas sejam seguras. Por exemplo, o **Azure PowerShell** e a **interface de linha de comando entre plataformas do Azure** armazenam informações importadas no **diretório base** (*~* nos sistemas Linux ou OS X e */users/yourusername* nos sistemas Windows.) Para ter segurança extra, convém **criptografar** esses locais usando as ferramentas de criptografia disponíveis para o sistema operacional.

###Definições de configuração e cadeias de conexão
É uma prática comum armazenar cadeias de conexão, credenciais de autenticação e outras informações confidenciais em arquivos de configuração. Infelizmente, esses arquivos podem ser expostos no site ou pode haver check-in deles em um repositório público, expondo essas informações.

Os sites do Azure permitem armazenar informações de configuração como parte do ambiente de tempo de execução dos sites como **configurações do aplicativo** e **cadeias de conexão**. Os valores são expostos ao aplicativo durante o tempo de execução por meio de variáveis do ambiente na maioria das linguagens de programação. Para aplicativos do .NET, esses valores são injetados na configuração do .NET durante o tempo de execução.

**Configurações do aplicativo** e **cadeias de conexão** são configuráveis usando o portal de gerenciamento do Azure ou os utilitários como o PowerShell ou a interface de linha de comando entre plataformas do Azure.

Para obter mais informações sobre configurações do aplicativo e cadeias de conexão, consulte [Configurando sites](/pt-br/documentation/articles/web-sites-configure/).

###Protocolo FTPS

O Azure oferece acesso via protocolo FTP seguro ao sistema de arquivos para o site por meio do protocolo **FTPS**. Isso permite acessar com segurança o código do aplicativo no Site, bem como os logs de diagnóstico. O link do protocolo FTPS para o site pode ser encontrado no **Painel** do site no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

Para obter mais informações sobre o protocolo FTPS, consulte [Protocolo FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

##Próximas etapas

Para obter mais informações sobre a segurança da plataforma Azure, informações sobre como relatar um **incidente de segurança ou um abuso**, ou informar à Microsoft que você realizará **testes de penetração** no site, consulte a seção de segurança da [Central de Confiabilidade do Microsoft Azure](/pt-br/support/trust-center/security/).

Para obter mais informações sobre o arquivo **web.config** ou **applicationhost.config** nos sites do Azure, consulte [Opções de configuração desbloqueadas nos sites do Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Para obter informações sobre como registrar em log informações de sites do Azure, que podem ser úteis na detecção de ataques, consulte [Habilitar registro em log de diagnóstico](/pt-br/documentation/articles/web-sites-enable-diagnostic-log/).
