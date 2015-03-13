<properties 
	pageTitle="Como implantar um site do Azure" 
	description="Saiba quais métodos estão disponíveis para a implantação de conteúdo para um site do Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/07/2015" 
	ms.author="tdykstra"/>

#Como implantar um site do Azure

Você tem várias opções para implantar seu próprio conteúdo em um Site do Azure. Este tópico apresenta uma breve visão geral de cada opção e links para mais informações.

* [Implantando de um sistema de controle do código-fonte hospedado em nuvem](#cloud)
	* Visual Studio Online
	* Sites de repositório usando o Git
	* Sites de repositório usando o Mercurial
	* Dropbox
* [Implantando de um IDE](#ide)
	* Visual Studio
	* WebMatrix
* [Implantando usando um utilitário FTP](#ftp)
* [Implantando de um sistema de controle do código-fonte local](#onpremises)
	* Team Foundation Server (TFS)
	* Repositórios locais do Git ou do Mercurial
* [Usando ferramentas de linha de comando e a API de gerenciamento do REST do Azure](#commandline)
	* MSBuild
	* Scripts do FTP
	* Windows PowerShell
	* API de gerenciamento do .NET
	* Linha de comando entre plataformas (xpat-cli)
	* Linha de comando de Implantação da Web


##<a name="cloud"></a>Implantando de um sistema de controle do código-fonte hospedado em nuvem

A melhor maneira de implantar um site é configurando um [fluxo de trabalho de entrega contínua](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) integrado com seu [sistema de controle do código-fonte](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control). A automação não só torna o processo de desenvolvimento mais eficiente, mas também pode tornar os processos de backup e restauração mais gerenciáveis e confiáveis. 

Se você não tiver configurado o controle do código-fonte ainda, a maneira mais fácil de começar será usando um sistema de controle do código-fonte hospedado em nuvem.

###<a name="vso"></a>Visual Studio Online

O [Visual Studio Online](http://www.visualstudio.com/) (anteriormente conhecido como Team Foundation Service) é a solução baseada em nuvem da Microsoft para colaboração de equipe e controle do código-fonte. O serviço é grátis para uma equipe de até 5 desenvolvedores. Você pode fazer o fornecimento contínuo de um Website do Azure e seu repositório pode usar [Git ou TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs).

Para obter mais informações, consulte os recursos a seguir:

* [Entrega contínua no Azure usando o Visual Studio Online e TFVC](../cloud-services-continuous-delivery-use-vso/).Tutorial passo a passo que mostra como configurar o fornecimento contínuo do Visual Studio Online para um Website do Azure, usando o TFVC. TFVC é a opção de controle do código-fonte centralizado, ao contrário do Git, que é a opção de controle do código-fonte distribuído.
* [Entrega contínua no Azure usando o Visual Studio Online e o Git](../cloud-services-continuous-delivery-use-vso-git/). Semelhante ao tutorial anterior, porém usando o Git em vez do TFVC.

###<a name="git"></a>Sites de repositório usando o Git

O [Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) é um sistema de controle do código-fonte distribuído popular. O Azure tem recursos internos que facilitam a automação da implantação de um site do Azure a partir de sites de repositório populares baseados na web que armazenam repositórios Git, incluindo o [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) e o [BitBucket](https://bitbucket.org/). Uma vantagem de usar do Git para a implantação é a reversão relativamente fácil para uma implantação anterior se isso for necessário em algum momento. 

Para obter mais informações, consulte os recursos a seguir:

* [Publicando do controle de origem para Websites do Azure com Git](../web-sites-publish-source-control/). Como usar o Git para publicar diretamente do computador local em um site do Azure (no Azure, esse método de publicação é denominado Git local). Ele também mostra como habilitar a implantação contínua de repositórios Git a partir do GitHub, do CodePlex ou do BitBucket.
* [Implantar em sites da Web com o GitHub usando o Kudu](/pt-br/documentation/videos/deploying-to-azure-from-github/). Vídeo de Scott Hanselman e David Ebbo que mostra como implantar um site da Web diretamente do GitHub para um site do Azure.
* [Implantar em um botão do Azure para Websites do Azure](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Blog sobre um método para acionar a implantação de um repositório Git.
* [Fórum do Azure para Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="mercurial"></a>Sites de repositório usando o Mercurial

Se você utilizar o [Mercurial](http://mercurial.selenic.com/) como seu sistema de controle do código-fonte e armazenar seu repositório no [CodePlex](http://www.codeplex.com/) ou [BitBucket](https://bitbucket.org/), será possível utilizar recursos internos nos sites do Azure para implantar automaticamente seu conteúdo.

Para obter informações sobre como implantar usando o Mercurial, consulte os recursos a seguir:

* [Publicando do controle de origem para Websites do Azure com Git](../web-sites-publish-source-control/). Embora este tutorial mostre como publicar um repositório Git, o processo para repositórios Mercurial hospedados no CodePlex ou nonBitBucket é semelhante.
* [Fórum do Azure para Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="dropbox"></a>Dropbox

O [Dropbox](https://www.dropbox.com/) não é um sistema de controle do código-fonte, mas, se você armazenar seu código-fonte nele, poderá automatizar a implantação da sua conta.

* [Implantar em Websites do Azure do Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Como usar o Portal de Gerenciamento do Azure para configurar a implantação do Dropbox.
* [Implantação do dropbox para Websites do Azure](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). Este vídeo o orientará pelo processo de conexão de uma pasta Dropbox a um site do Azure e mostrará a rapidez em que você colocará um site em funcionamento ou o manterá usando a implantação simples do tipo "arrastar e soltar".
* [Fórum do Azure para Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).







##<a name="ide"></a>Implantando de um IDE

O [Visual Studio](http://www.visualstudio.com/) e o [WebMatrix](http://www.microsoft.com/web/webmatrix/) são IDEs (ambientes de desenvolvimento integrado) da Microsoft que você pode usar para desenvolvimento na web. Ambos fornecem recursos internos que facilitam a implantação de sites do Azure.  Ambos podem usar a [Implantação da Web](http://www.iis.net/downloads/microsoft/web-deploy) para automatizar as tarefas adicionais relacionadas à implantação, como alterações na cadeia de conexão e implantação de banco de dados. Ambos também podem implantar usando [FTP ou FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol). 

O WebMatrix é rápido de instalar e fácil de aprender, mas o Visual Studio oferece muito mais recursos para trabalhar com os sites do Azure. No IDE do Visual Studio, você poderá criar, parar, iniciar e excluir sites do Azure, exibir logs após sua criação em tempo real, depurar remotamente etc.. O Visual Studio também se integra com sistemas de controle de origem como [Visual Studio Online](#vso), [Team Foundation Server](#tfs), e [repositórios Git](#git).

###<a name="vs"></a>Visual Studio

Para obter informações sobre como implantar sites do Azure a partir do Visual Studio, consulte os recursos a seguir:

* [Introdução ao Microsoft Azure e ao ASP.NET](../web-sites-dotnet-get-started/). Como criar e implantar um projeto da web ASP.NET MVC simples usando o Visual Studio e a Implantação da Web.
* [Como implantar o Trabalhos Web do Azure nos sites do Azure](../websites-dotnet-deploy-webjobs/). Como configurar projetos de Aplicativo de console para que sejam implantados como Trabalhos Web.  
* [Implantar um aplicativo ASP.NET MVC 5 seguro com Associação, OAuth e Banco de Dados SQL em um site do Azure](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/). Como criar e implantar um projeto da web ASP.NET MVC com um banco de dados SQL, usando o Visual Studio, a Implantação da Web e as Migrações do Entity Framework Code First.
* [Visão geral sobre a implantação da Web para o Visual Studio e o ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Uma introdução básica à implantação da web usando o Visual Studio. Desatualizado, mas inclui informações que ainda são relevantes, incluindo uma visão geral das opções para a implantação de um banco de dados junto com o aplicativo da web e uma lista de tarefas de implantação adicionais que você talvez tenha que executar ou configurar o Visual Studio manualmente para executá-las para você. Este tópico é sobre implantação em geral, e não apenas sobre a implantação de sites do Azure.
* [Implantação da Web do ASP.NET usando o Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Uma série de tutoriais de 12 partes que abrange uma gama mais completa de tarefas de implantação do que os outros nessa lista. Alguns recursos de implantação do Azure foram adicionados desde que o tutorial foi escrito, mas anotações adicionadas posteriormente explicam o que está faltando. 
* [Implantação direta de um site do ASP.NET no Azure no Visual Studio 2012 a partir de um repositório Git](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Explica como implantar um projeto da web ASP.NET no Visual Studio, usando o plug-in do Git para confirmar o código para o Git e conectar o Azure ao repositório Git. A partir do Visual Studio 2013, o suporte do Git é incorporado e não requer a instalação de um plug-in.

###<a name="webmatrix"></a>WebMatrix

Para obter informações sobre como implantar sites do Azure a partir do WebMatrix, consulte os recursos a seguir:

* [Desenvolver e implantar um site com o Microsoft WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-using-webmatrix/). Como criar um site do ASP.NET simples usando um modelo do WebMatrix e implantá-lo em um site do Azure usando o WebMatrix e a Implantação da Web.
* [Compilar e implantar um site do node.js no Azure usando o WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-use-webmatrix/).
* [Criar e implantar um site do Azure PHP-MySQL usando o WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-php-mysql-use-webmatrix/).
* [WebMatrix 3: Git integrado e implantação no Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Como usar o WebMatrix para implantar de um repositório de controle do código-fonte do Git.

##<a name="ftp"></a>Implantando usando um utilitário FTP

Independentemente de qual IDE for utilizado, você também poderá implantar conteúdo no seu site utilizando o [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para copiar arquivos. É fácil criar credenciais FTP para um site do Azure, e você pode usá-las em qualquer aplicativo que funcione com FTP, incluindo navegadores, como o Internet Explorer, e utilitários gratuitos completos, como o [FileZilla](https://filezilla-project.org/).  Os sites do Azure também oferecem suporte ao protocolo FTPS, mais seguro. 

Embora seja fácil copiar os arquivos do seu site para o Azure utilizando os utilitários FTP, eles não cuidam das tarefas de implantação relacionadas, nem as coordenam automaticamente, como implantar um banco de dados ou alterar as cadeias de conexão. Além disso, várias ferramentas de FTP não comparam os arquivos de origem com os arquivos de destino para ignorar a cópia dos arquivos inalterados. Os sites grandes, que sempre copiam todos os arquivos, podem resultar em longos tempos de implantação, até mesmo para atualizações menores, pois todos os arquivos são sempre copiados.

Para obter mais informações, consulte os recursos a seguir:

* [Criar um site do PHP-MySQL Azure e implantar usando o FTP](../web-sites-php-mysql-deploy-use-ftp/). 
* [Como gerenciar sites](../web-sites-manage/#ftp-credentials). Inclui informações adicionais não inseridas no tutorial do PHP sobre como configurar as credenciais de FTP. 


##<a name="onpremises"></a>Implantando de um sistema de controle do código-fonte local

Se estiver usando o TFS, o Git ou o Mercurial em um repositório local (não hospedado em nuvem), você poderá implantar diretamente desse repositório nos sites do Azure.

###<a name="tfs"></a>Team Foundation Server (TFS)

O Team Foundation Server é uma solução local da Microsoft para colaboração de equipe e controle do código-fonte. Você pode configurar o TFS fazer entrega contínua para um site do Azure.

Para obter mais informações, consulte o recurso a seguir:

* [Entrega contínua de serviços de nuvem no Azure](../cloud-services-dotnet-continuous-delivery/). Este documento é destinado a um serviço de nuvem do Azure, mas parte do conteúdo é relevante para sites.

###<a name="gitmercurial"></a>Repositórios locais do Git ou do Mercurial

No Azure, você pode inserir a URL de qualquer repositório que utiliza o Git ou o Mercurial para implantar desse local. Você também pode enviar diretamente por push de um repositório Git local para um site do Azure. 

Para obter mais informações, consulte os recursos a seguir:

* [Publicando do controle de origem para Websites do Azure com Git](../web-sites-publish-source-control/). Como usar o Git para publicar diretamente do computador local em um site do Azure (no Azure, esse método de publicação é denominado Git local). Ele também mostra como habilitar a implantação contínua de repositórios Git a partir do GitHub, do CodePlex ou do BitBucket.
* [Publicando nos sites do Azure de qualquer repositório git/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog que explica o recurso "Repositório externo" nos Websites do Azure.
* [Fórum do Azure para Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).
* [Implantando DOIS sites no Azure de um repositório Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Postagem de blog de Scott Hanselman.











##<a name="commandline"></a> Usando ferramentas de linha de comando e a API de gerenciamento do REST do Azure

É sempre melhor automatizar o fluxo de trabalho de desenvolvimento, mas se você não puder fazer isso diretamente no sistema de controle do código-fonte, poderá configurá-lo manualmente utilizando as ferramentas de linha de comando. Isso geralmente envolve o uso de mais de uma ferramenta ou estrutura, pois a implantação geralmente abrange a execução de funções de gerenciamento de site, bem como a cópia de conteúdo.

O Azure simplifica as tarefas de gerenciamento de site que você terá que executar para a implantação, fornecendo uma API de gerenciamento do REST e várias estruturas que facilitam o trabalho com a API.

###<a name=msbuild></a>MSBuild

Se você usar o [IDE do Visual Studio](#vs) para desenvolvimento, poderá usar o  [MSBuild](http://msbuildbook.com/) para automatizar qualquer tarefa que possa ser executada no seu IDE. Você pode configurar o MSBuild para usar a [Implantação da Web](#webdeploy) ou o [FTP/FTPS](#ftp) para copiar arquivos. A Implantação da Web também pode automatizar várias outras tarefas relacionadas à implantação tarefas, como a implantação de bancos de dados.

Para obter mais informações sobre a implantação de linha de comando usando o MSBuild, consulte os recursos a seguir:

* [Implantação da Web do ASP.NET usando o Visual Studio: Implantação de linha de comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Décimo de uma série de tutoriais sobre implantação no Azure usando o Visual Studio. Mostra como usar a linha de comando para implantar após a configuração de perfis de publicação no Visual Studio.
* [Dentro do mecanismo de compilação da Microsoft: Usando o MSBuild e o Team Foundation Build](http://msbuildbook.com/). Livro impresso que contém capítulos sobre como usar o MSBuild para implantação.

###<a name="ftp2"></a>Scripts do FTP

É fácil criar credenciais de [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para um site do Azure e você pode utilizá-las com os scripts em lotes FTP.

Para obter mais informações, consulte o recurso a seguir:

* [Usando scripts em lotes FTP](http://support.microsoft.com/kb/96269).

###<a name="powershell"></a>Windows PowerShell

É possível executar funções de implantação do MSBuild ou de FTP por meio do [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Se fizer isso, você também poderá usar uma coleção de cmdlets do Windows PowerShell que facilitam chamar a API de gerenciamento do REST do Azure.

Para obter mais informações, consulte o recurso a seguir:

* [Compilando aplicativos de nuvem do mundo real com o Azure - automatizar tudo](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Capítulo de livro eletrônico que explica como o aplicativo de amostra mostrado no livro eletrônico usa scripts do Windows PowerShell para criar um ambiente de teste do Azure e implantá-lo. Consulte a seção [Recursos](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) para obter links para a documentação adicional do PowerShell do Azure.
* [Usando scripts do Windows PowerShell para publicar para ambientes de desenvolvimento e teste](http://msdn.microsoft.com/library/dn642480.aspx). Como usar os scripts de implantação do Windows PowerShell que o Visual Studio gera.

###<a name="api"></a>API de gerenciamento do .NET

É possível escrever um código C# para executar as funções do MSBuild ou do FTP para implantação. Se fizer isso, você poderá acessar a API de gerenciamento do REST do Azure para executar as funções de gerenciamento de site.

Para obter mais informações, consulte o recurso a seguir:

* [Automatizando tudo com as bibliotecas de gerenciamento do Azure e o .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introdução à API de gerenciamento do .NET e links para mais documentação.

###<a name="cli"></a>Linha de comando entre plataformas (xplat-cli)

É possível utilizar a linha de comando em máquinas Mac ou Linux para implantar utilizando o FTP. Se fizer isso, você também poderá acessar a API de gerenciamento do REST do Azure usando a interface de linha de comando entre plataformas (xpat-cli) do Azure. A xplat-cli também pode ser usada em máquinas Windows.

Para obter mais informações, consulte o recurso a seguir:

* [Ferramentas de linha de comando](/pt-br/downloads/#cmd-line-tools). Página de portal no WindowsAzure.com que fornece informações da ferramenta de linha de comando.

###<a name="webdeploy"></a>Linha de comando de Implantação da Web

A [Implantação da Web](http://www.iis.net/downloads/microsoft/web-deploy) é o software da Microsoft para a implantação do IIS que não só fornece recursos de sincronização de arquivos inteligentes, mas também pode executar ou coordenar várias outras tarefas relacionadas à implantação que não poderão ser automatizadas quando você usar o FTP. Por exemplo, a Implantação da Web pode implantar um novo banco de dados ou atualizações de banco de dados junto com seu site. A Implantação da Web também pode minimizar o tempo necessário para atualizar um site existente, pois ele pode copiar apenas os arquivos alterados de forma inteligente. O Microsoft WebMatrix, o Visual Studio, o Visual Studio Online e o Team Foundation Server têm suporte para a Implantação da Web interna, mas você também pode usar a Implantação da Web diretamente da linha de comando para automatizar a implantação. Os comandos de Implantação da Web são muito avançados, mas a curva de aprendizado pode ser acentuada.

Para obter mais informações, consulte o recurso a seguir:

* [Websites do Azure simples: Implantação](http://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog de David Ebbo sobre uma ferramenta que ele escreveu para torná-lo mais fácil de usar a implantação da Web.
* [Ferramenta de Implantação de Web](http://technet.microsoft.com/library/dd568996). Documentação oficial no site do Microsoft TechNet. Desatualizada, mas ainda um bom ponto de partida.
* [Usando a Implantação da Web](http://www.iis.net/learn/publish/using-web-deploy). Documentação oficial no site do Microsoft IIS.NET. Também desatualizada, mas ainda um bom ponto de partida.
* [StackOverflow](http://www.stackoverflow.com). O melhor lugar para obter informações mais atuais sobre como usar a Implantação da Web da linha de comando.
* [Implantação da Web do ASP.NET usando o Visual Studio: Implantação por Linha de comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). O MSBuild é o mecanismo de compilação usado pelo Visual Studio e também pode ser usado a partir da linha de comando para implantar aplicativos da web nos sites do Azure. Este tutorial faz parte de uma série que trata principalmente da implantação do Visual Studio.

##<a name="nextsteps"></a>Próximas etapas

Em alguns situações, é provável que você deseje ser capaz de alternar facilmente entre a versão de preparo e a versão de produção do seu site da Web. Você pode fazer isso usando a ferramenta de implantação de preparo nos Sites do Azure. Para obter mais informações, consulte [Implantação em etapas em Sites do Microsoft Azure](../web-sites-staged-publishing/).

Uma parte importante de qualquer fluxo de trabalho de implantação é ter um plano de backup e restauração. Para obter mais informações sobre o recurso de backup e restauração dos Sites do Azure, consulte [Backups de Sites do Azure](../web-sites-backup/).  

Para obter informações sobre como usar o controle de acesso baseado em função do Azure para gerenciar o acesso à implantação de site, consulte [RBAC e publicação de Websites do Azure](http://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing).

Para obter mais informações sobre outros tópicos de implantação, consulte a seção Implantar na [documentação de Sites do Azure](/pt-br/documentation/services/web-sites/).


<!--HONumber=42-->
