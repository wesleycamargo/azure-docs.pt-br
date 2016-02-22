<properties
	pageTitle="Implantar seu aplicativo no Serviço de Aplicativo do Azure"
	description="Saiba como implantar seu aplicativo no Serviço de Aplicativo do Azure."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="cephalin;tdykstra"/>
    
# Implantar seu aplicativo no Serviço de Aplicativo do Azure

Este artigo ajuda você a determinar a melhor opção para implantar os arquivos de seu aplicativo Web, back-end de aplicativo móvel ou aplicativo de API no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) e conduz você até os artigos e blogs apropriados com instruções específicas para sua opção preferida.

## <a name="overview"></a>Visão geral dos processos de implantação

Quando você cria ou provisiona um aplicativo no Serviço de Aplicativo do Azure, e antes de implantar qualquer código nele, o Serviço de Aplicativo do Azure mantém a estrutura do aplicativo para você (ASP.NET, PHP, Node.js, etc.). Algumas estruturas estão habilitadas por padrão enquanto outras, como Java e Python, talvez precisem de uma simples marca de seleção para habilitá-las. Além disso, você pode personalizar a estrutura do aplicativo, como a versão do PHP ou o número de bits de seu tempo de execução. Para saber mais, confira [Configurar seu aplicativo no Serviço de Aplicativo do Azure](web-sites-configure.md).

Como você não precisa se preocupar com a estrutura do servidor Web ou do aplicativo, a implantação do aplicativo no Serviço de Aplicativo é uma questão de implantação do código, dos binários, dos arquivos de conteúdo e suas respectivas estruturas de diretórios, no diretório [**/site/wwwroot** no Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (ou no diretório **/Data/Jobs** para Trabalhos Web). O Serviço de Aplicativo oferece suporte aos três processos de implantação a seguir.

- [FTP ou FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): use sua ferramenta compatível com FTP ou FTPS favorita para mover seus arquivos para o Azure, do [FileZilla](https://filezilla-project.org) para IDEs completos como o [NetBeans](https://netbeans.org). Esse é estritamente um processo de carregamento de arquivo. Nenhum outro serviço é fornecido pelo Serviço de Aplicativo, por exemplo, controle de versão, gerenciamento de estrutura de arquivos, etc. 
- [Kudu (Git/Mercurial)](https://github.com/projectkudu/kudu/wiki/Deployment): o [mecanismo de implantação](https://github.com/projectkudu/kudu/wiki) no Serviço de Aplicativo. Envie diretamente seu código para o Kudu a partir de qualquer repositório. O Kudu também fornece outros serviços sempre que o código é enviado para ele, incluindo controle de versão, restauração de pacote, MSBuild e [ganchos da Web](https://github.com/projectkudu/kudu/wiki/Web-hooks) para implantação contínua e outras tarefas de automação. Todos esses serviços podem ser personalizados e acionados 
    - sempre que **git push** for executado a partir de um repositório Git configurado,
	- sempre que **hg push** for executado a partir de um repositório Mercurial configurado, ou 
    - sempre que um armazenamento em nuvem vinculado, como o Dropbox ou o OneDrive, for sincronizado com o Serviço de Aplicativo. 
- [Implantação da Web](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): as mesmas ferramentas que automatizam a implantação em servidores IIS. Implante o código no Serviço de Aplicativo diretamente de suas ferramentas favoritas da Microsoft, como o Visual Studio, o WebMatrix e o Visual Studio Team Services. Essa ferramenta oferece suporte à implantação apenas da diferença, criação de banco de dados, transformações de cadeias de conexão, etc. A Implantação da Web é diferente do Kudu no sentido de que os binários de aplicativo são criados antes de serem implantados no Azure. Como acontece com o FTP, nenhum serviço adicional é fornecido pelo Serviço de Aplicativo.

Populares ferramentas de desenvolvimento da Web oferecem suporte a um ou mais desses processos de implantação. Embora sua ferramenta escolhida determine os processos de implantação que podem ser aproveitados, a funcionalidade real do DevOps à sua disposição depende da combinação do processo de implantação e de ferramentas específicas escolhidas por você. Por exemplo, se você executar a Implantação da Web do [Visual Studio com o SDK do Azure](#vspros), apesar de não contar com a automação do Kudu, você obtém a restauração do pacote e a automação do MSBuild no Visual Studio. O SDK do Azure também fornece um assistente simples para ajudar você a criar os recursos necessários do Azure diretamente da interface do Visual Studio.

>[AZURE.NOTE] Na verdade, esses processos de implantação não [provisionam os recursos do Azure](resource-group-portal) que seu aplicativo pode precisar, como o plano do Serviço de Aplicativo, o aplicativo do Serviço de Aplicativo e o Banco de Dados SQL. No entanto, a maioria dos artigos de instruções vinculados mostra como provisionar o aplicativo E implantar seu código no aplicativo, de ponta a ponta. Você também pode encontrar outras opções para o provisionamento de recursos do Azure na seção [Automatizar a implantação usando as ferramentas de linha de comando](#automate).

## <a name="ftp"></a>Implantar copiando arquivos manualmente para o Azure
Se estiver acostumado a copiar manualmente o conteúdo da Web para hosters da Web, um fluxo de trabalho comum para desenvolvedores em PHP, você poderá usar um utilitário [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para copiar arquivos, como o Windows Explorer ou o [FileZilla](https://filezilla-project.org/).

A cópia manual de arquivos utiliza o protocolo FTP para a implantação (confira [Visão geral dos processos de implantação](#overview)).

Os prós de copiar manualmente os arquivos são:

- Familiaridade com as ferramentas de FTP. 
- Você sabe exatamente para onde vão os arquivos.
- Maior segurança com FTPS.
- Boa solução de implantação se você quiser o mínimo de ferramentas para o desenvolvimento da Web (por exemplo, desenvolver aplicativos Web usando o Bloco de Notas).

Os contras de copiar manualmente os arquivos são:

- Você é responsável pela implantação dos arquivos nos diretórios corretos no Serviço de Aplicativo.
- Se falhas ocorrerem, não haverá um controle de versão para reversão.
- Muitas ferramentas FTP não fornecem a cópia apenas da diferença e simplesmente copiam todos os arquivos. Para aplicativos de grande porte, isso gera tempos de implantação maiores até mesmo para atualizações pequenas.

### <a name="howtoftp"></a>Como implantar copiando arquivos manualmente no Azure
A cópia de arquivos para o Azure envolve algumas etapas simples:

1. Criar credenciais de implantação para seu aplicativo no [Portal do Azure](https://portal.azure.com). Para fazer isso, na folha de seu aplicativo, clique em **Configurações** > **Credenciais de Implantação**.
2. Depois de configurar as credenciais de implantação, obtenha as informações de conexão do FTP acessando **Configurações** > **Propriedades** e, em seguida, copie os valores de **FTP/Usuário de Desenvolvimento**, **Nome do Host FTP** e **Nome do Host FTPS**.
3. De seu cliente FTP, use as informações de conexão coletadas para conectar-se ao seu aplicativo.
4. Copie seus arquivos e a respectiva estrutura de diretórios para o diretório [**/site/wwwroot** no Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (ou o diretório **/Data/Jobs** para Trabalhos Web).
5. Navegue até a URL do aplicativo para verificar se ele está sendo executado corretamente. 

Para obter mais informações, consulte os recursos a seguir:

* [Criar um aplicativo Web do PHP-MySQL e implantá-lo usando o FTP](web-sites-php-mysql-deploy-use-ftp.md).
* [Usando scripts em lotes FTP](http://support.microsoft.com/kb/96269).

## <a name="dropbox"></a>Implantar sincronizando com uma pasta de nuvem
Uma boa alternativa à [cópia manual dos arquivos](#ftp) é sincronizar os arquivos e pastas no Serviço de Aplicativo de um serviço de armazenamento em nuvem, como o OneDrive e o Dropbox. No [Portal do Azure](https://portal.azure.com), você pode configurar uma pasta especial em seu armazenamento em nuvem, trabalhar com o código e o conteúdo do aplicativo nessa pasta e sincronizar com o Serviço de Aplicativo com apenas um clique.

A sincronização com uma pasta de nuvem utiliza o processo Kudu para implantação (confira [Visão geral dos processos de implantação](#overview)).

Os prós da sincronização com uma pasta de nuvem são:

- Simplicidade de implantação. Serviços como o OneDrive e o Dropbox fornecem clientes de sincronização de área de trabalho, assim o diretório de trabalho local também é o diretório de implantação.
- Implantação com um único clique.
- Todas as funcionalidades do Kudu estão disponíveis (por exemplo, controle de versão de implantação, reversão, restauração de pacote, automação).
- Boa solução de implantação se você quiser o mínimo de ferramentas para desenvolvimento da Web.

Os contras da sincronização com uma pasta de nuvem são:

- Não é uma boa solução para um projeto em equipe.

### <a name="howtodropbox"></a>Como implantar sincronizando com uma pasta de nuvem
 
* [Implantar em Aplicativos Web por meio do Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Como usar o [Portal do Azure](https://portal.azure.com) para configurar a implantação do Dropbox.
* [Implantação do Dropbox em Aplicativos Web](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). Este vídeo o orientará pelo processo de conexão de uma pasta DropBox a um aplicativo Web do Azure e mostrará com que rapidez você pode colocar um aplicativo Web em funcionamento ou mantê-lo, usando a implantação simples do tipo "arrastar e soltar".
* [Fórum do Azure para Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

## Implantar usando o IDE
Se já estiver usando o [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) com um [SDK do Azure](https://azure.microsoft.com/downloads/) ou o WebMatrix, ou outros pacotes de IDE como [Xcode](https://developer.apple.com/xcode/) e [Eclipse](https://www.eclipse.org), você poderá implantar no Azure diretamente de seu IDE. Essa opção é ideal para um desenvolvedor individual.

O Visual Studio oferece suporte a todos os três processos de implantação (FTP, Git e Implantação da Web), dependendo de sua preferência, enquanto outros IDEs podem implantar no Serviço de Aplicativo se tiverem integração com FTP ou Git (confira [Visão geral dos processos de implantação](#overview)).

Os prós da implantação usando um IDE são:

- Possibilidade de minimizar as ferramentas para o ciclo de vida completo do aplicativo. Desenvolver, depurar, acompanhar e implantar seu aplicativo no Azure sem sair do IDE. 

Os contras da implantação usando um IDE são:

- Complexidade das ferramentas.
- Ainda exige um sistema de controle de código-fonte para um projeto em equipe.

<a name="vspros"></a> Os outros prós da implantação usando o Visual Studio com o SDK do Azure são:

- O SDK do Azure torna os recursos do Azure cidadãos de primeira classe no Visual Studio. Crie, exclua, edite, inicie e interrompa aplicativos, consulte o Banco de Dados SQL de back-end, depure em tempo real o aplicativo do Azure e muito mais. 
- Edição em tempo real dos arquivos de código no Azure.
- Depuração em tempo real de aplicativos no Azure.
- Gerenciador integrado do Azure.
- Implantação apenas da diferença. 

###<a name="vs"></a>Como implantar diretamente do Visual Studio

* [Introdução ao Microsoft Azure e ao ASP.NET](web-sites-dotnet-get-started.md). Como criar e implantar um projeto da web ASP.NET MVC simples usando o Visual Studio e a Implantação da Web.
* [Como implantar Trabalhos Web do Azure usando o Visual Studio](websites-dotnet-deploy-webjobs.md). Como configurar projetos de Aplicativo de console para que sejam implantados como Trabalhos Web.  
* [Implantar um aplicativo ASP.NET MVC 5 Seguro com Associação, OAuth e Banco de Dados SQL em Aplicativos Web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Como criar e implantar um projeto da web ASP.NET MVC com um banco de dados SQL, usando o Visual Studio, a Implantação da Web e as Migrações do Entity Framework Code First.
* [Visão geral sobre a implantação da Web para o Visual Studio e o ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Uma introdução básica à implantação da web usando o Visual Studio. Desatualizado, mas inclui informações que ainda são relevantes, incluindo uma visão geral das opções para a implantação de um banco de dados junto com o aplicativo da web e uma lista de tarefas de implantação adicionais que você talvez tenha que executar ou configurar o Visual Studio manualmente para executá-las para você. Este tópico é sobre implantação em geral, e não apenas sobre a implantação em aplicativos Web.
* [Implantação da Web do ASP.NET usando o Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Uma série de tutoriais de 12 partes que abrange uma gama mais completa de tarefas de implantação do que os outros nessa lista. Alguns recursos de implantação do Azure foram adicionados desde que o tutorial foi escrito, mas anotações adicionadas posteriormente explicam o que está faltando.
* [Implantação direta de um site do ASP.NET no Azure no Visual Studio 2012 a partir de um repositório Git](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Explica como implantar um projeto da web ASP.NET no Visual Studio, usando o plug-in do Git para confirmar o código para o Git e conectar o Azure ao repositório Git. A partir do Visual Studio 2013, o suporte ao Git é integrado e não requer a instalação de um plug-in.

###<a name="webmatrix"></a>Como implantar diretamente do WebMatrix

* [Compilar e implantar um site do node.js no Azure usando o WebMatrix](web-sites-nodejs-use-webmatrix.md).
* [WebMatrix 3: Git Integrado e Implantação no Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Como usar o WebMatrix para implantar de um repositório de controle do código-fonte do Git.

## <a name="onprem"></a>Implantar de um sistema de controle do código-fonte local

Se você trabalha em uma equipe de desenvolvimento de qualquer tamanho e usa um sistema SCM (gerenciamento de código-fonte local) como o [Team Foundation Server](https://www.visualstudio.com/products/tfs-overview-vs.aspx) (TFS), [Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) ou [Mercurial](http://mercurial.selenic.com/), pode configurar o Serviço de Aplicativo para integração com seu repositório e implantar diretamente no Serviço de Aplicativo em seu fluxo de trabalho de controle do código-fonte. Se você usar o TFS, também poderá configurá-lo para implantar continuamente no Serviço de Aplicativo.

O TFS usa a Implantação da Web para implantar no Serviço de Aplicativo, enquanto a implantação de repositórios Git/Mercurial usa Kudu (confira [Visão geral dos processos de implantação](#overview)).

Os prós da implantação de um sistema de controle do código-fonte local são:

- Suporte para implantação de qualquer estrutura de linguagem ou cliente Git ou Mercurial, incluindo [Xcode](https://developer.apple.com/xcode/) e [Eclipse](https://www.eclipse.org).
- Implantação específica à ramificação, é possível implantar diferentes versões em [slots](web-sites-staged-publishing) separados.
- Boa para equipes de desenvolvimento de qualquer tamanho.

Os contras da implantação de um sistema de controle do código-fonte local são:

- É necessário ter conhecimento do sistema SCM escolhido.
- Pode fornecer mais funcionalidade e recursos do que você precisa.
- Falta de soluções completas para implantação contínua e implantação específica à ramificação das ferramentas de cliente Git e Mercurial. 

Outros prós da implantação usando o TFS são:

- CI (integração contínua) para compilações, testes e implantação.
- Ferramentas de colaboração internas que funcionam com seu IDE ou editor existente.
- Suporte para Git para controle de versão distribuído ou o controle de versão do Team Foundation para um controle de versão mais centralizado. 
- Ferramentas avançadas para uma implantação ágil.
- Integrações prontas para [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [Barramento de Serviço do Azure](/services/service-bus/) e muito mais. 
- O [Team Foundation Server Express](https://www.microsoft.com/download/details.aspx?id=48259) é gratuito para uma equipe de até cinco desenvolvedores.

###<a name="tfs"></a>Como implantar continuamente com o TFS

* [Entrega contínua de serviços de nuvem no Azure](../cloud-services/cloud-services-dotnet-continuous-delivery.md). Este documento é destinado a um serviço de nuvem do Azure, mas parte do conteúdo é relevante a aplicativos Web.

###<a name="gitmercurial"></a>Como implantar de um repositório Git ou Mercurial local

* [Publicando por meio do Controle do Código-fonte em Aplicativos Web com Git](web-sites-publish-source-control.md). Como usar o Git para publicar diretamente do computador local em um aplicativo Web (no Azure, esse método de publicação é denominado Git local). Ele também mostra como habilitar a implantação contínua de repositórios Git a partir do GitHub, do CodePlex ou do BitBucket.
* [Publicando em Aplicativos Web por meio de qualquer repositório git/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog que explica o recurso "Repositório Externo" em Aplicativos Web.
* [Fórum do Azure para Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).
* [Implantando DOIS sites no Azure de um repositório Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx) Postagem de blog de Scott Hanselman.

## Implantar de um sistema de controle do código-fonte baseado em nuvem
Se você trabalha em uma equipe de desenvolvimento de qualquer tamanho e usa um serviço SCM (gerenciamento de código-fonte) baseado em nuvem como o [Visual Studio Team Services](http://www.visualstudio.com/) (antigo Visual Studio Online), [GitHub](https://www.github.com), [GitLab](https://gitlab.com), [BitBucket](https://bitbucket.org/), [CodePlex](https://www.codeplex.com/), [Codebase](https://www.codebasehq.com) e [Kiln](https://www.fogcreek.com/kiln/), pode configurar o Serviço de Aplicativo para integrar com seu repositório e implantar continuamente.

O Visual Studio Team Services usa a Implantação da Web para implantar no Serviço de Aplicativo, enquanto a implantação de repositórios online usa Kudu (confira [Visão geral dos processos de implantação](#overview)).

Os prós de implantar de um serviço de controle de código-fonte baseado em nuvem são:

- Suporte para qualquer estrutura de linguagem.
- Implantação contínua com menos configuração para repositórios Git e Mercurial. 
- Implantação específica à ramificação, é possível implantar diferentes ramificações em [slots](web-sites-staged-publishing) diferentes.
- Boa para equipes de desenvolvimento de qualquer tamanho.

Os contras de implantar de um serviço de controle de código-fonte baseado em nuvem são:

- É necessário ter conhecimento do serviço SCM escolhido.
- Pode fornecer mais funcionalidade e recursos do que você precisa.

Os outros prós da implantação usando o Visual Studio Team Services são:

- Gratuito para uma equipe de até cinco desenvolvedores
- CI (integração contínua) para compilações, testes e implantação.
- Ferramentas de colaboração internas que funcionam com seu IDE ou editor existente.
- Suporte para Git para controle de versão distribuído ou o controle de versão do Team Foundation para um controle de versão mais centralizado. 
- Ferramentas avançadas para uma implantação ágil.
- Integrações prontas para [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [Barramento de Serviço do Azure](/services/service-bus/) e muito mais. 
- Painéis avançados para produção facilitada de relatórios com [conexão com o Power BI](https://www.visualstudio.com/get-started/report/report-on-vso-with-power-bi-vs).

###<a name="vsts"></a>Como implantar continuamente com o Visual Studio Team Services

- [Entrega contínua no Azure usando Visual Studio Team Services e TFVC](../cloud-services/cloud-services-continuous-delivery-use-vso.md). Tutorial passo a passo que mostra como configurar o fornecimento contínuo por meio do Visual Studio Team Services em um aplicativo Web, usando o TFVC. 
- [Entrega contínua no Azure usando o Visual Studio Team Services e Git](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md). Semelhante ao tutorial anterior, porém usando o Git em vez do TFVC.

###<a name="cloudgitmercurial"></a>Como implantar de um repositório Git ou Mercurial baseado em nuvem

- [Publicando por meio do Controle do Código-fonte em Aplicativos Web com Git](web-sites-publish-source-control.md). Como habilitar a implantação contínua de repositórios a partir do GitHub, do CodePlex ou do BitBucket. Embora este tutorial mostre como publicar um repositório Git, o processo para repositórios Mercurial hospedados no CodePlex ou nonBitBucket é semelhante.
- [Implantar em Aplicativos Web com o GitHub usando o Kudu](https://azure.microsoft.com/documentation/videos/deploying-to-azure-from-github/). Vídeo de Scott Hanselman e David Ebbo que mostra como implantar um aplicativo Web diretamente do GitHub no Serviço de Aplicativo.
- [Implantar em um botão do Azure para Aplicativos Web](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Blog sobre um método para acionar a implantação por meio de um repositório Git.
- [Fórum do Azure para Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

Para obter mais informações, consulte os recursos a seguir:

- [Publicando por meio do Controle do Código-fonte em Aplicativos Web com Git](web-sites-publish-source-control.md). Como usar o Git para publicar diretamente do computador local em aplicativos Web (no Azure, esse método de publicação é denominado Git local). 

## <a name="automate"></a>Automatizar a implantação usando ferramentas de linha de comando

* [Automatizar a implantação com o MSBuild](#msbuild)
* [Copiar arquivos com scripts e ferramentas de FTP](#ftp)
* [Automatizar a implantação com o Windows PowerShell](#powershell)
* [Automatizar a implantação com a API de gerenciamento do .NET](#api)
* [Implantar por meio da CLI (Interface de Linha de Comando) do Azure](#cli)
* [Implantar por meio da linha de comando de Implantação da Web](#webdeploy)
* [Usando scripts em lotes FTP](http://support.microsoft.com/kb/96269).
 
Outra opção de implantação é usar um serviço baseado em nuvem, como o [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy). Para obter mais informações, consulte [Implantar aplicativos ASP.NET em Sites do Azure](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

###<a name="msbuild"></a>Automatizar a implantação com o MSBuild

Se você usar o [IDE do Visual Studio](#vs) para desenvolvimento, poderá usar o [MSBuild](http://msbuildbook.com/) para automatizar qualquer tarefa que possa ser executada no seu IDE. Você pode configurar o MSBuild para usar a [Implantação da Web](#webdeploy) ou o [FTP/FTPS](#ftp) para copiar arquivos. A Implantação da Web também pode automatizar várias outras tarefas relacionadas à implantação tarefas, como a implantação de bancos de dados.

Para obter mais informações sobre a implantação de linha de comando usando o MSBuild, consulte os recursos a seguir:

* [Implantação da Web do ASP.NET usando o Visual Studio: implantação de linha de comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). O décimo de uma série de tutoriais sobre implantação no Azure usando o Visual Studio. Mostra como usar a linha de comando para implantar após a configuração de perfis de publicação no Visual Studio.
* [Por dentro do Microsoft Build Engine: usando o MSBuild e o Team Foundation Build](http://msbuildbook.com/). Livro impresso que contém capítulos sobre como usar o MSBuild para implantação.

###<a name="powershell"></a>Automatizar a implantação com o Windows PowerShell

Você pode executar funções de implantação do MSBuild ou de FTP a partir do [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Se fizer isso, você também poderá usar uma coleção de cmdlets do Windows PowerShell que facilitam chamar a API de gerenciamento do REST do Azure.

Para saber mais, consulte os recursos a seguir:

* [Implantar um aplicativo Web vinculado a um repositório GitHub](app-service-web-arm-from-github-provision.md)
* [Provisionar um aplicativo Web com um banco de dados SQL](app-service-web-arm-with-sql-database-provision.md)
* [Provisionar e implantar microsserviços previsíveis no Azure](app-service-deploy-complex-application-predictably.md)
* [Compilando aplicativos de nuvem do mundo real com o Azure - automatizar tudo](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Capítulo de livro eletrônico que explica como o aplicativo de amostra mostrado no livro eletrônico usa scripts do Windows PowerShell para criar um ambiente de teste do Azure e implantá-lo. Consulte a seção [Recursos](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) para obter links para a documentação adicional do PowerShell do Azure.
* [Usando scripts do Windows PowerShell para publicar em ambientes de desenvolvimento e de teste](http://msdn.microsoft.com/library/dn642480.aspx). Como usar os scripts de implantação do Windows PowerShell gerados pelo Visual Studio.

###<a name="api"></a>Automatizar a implantação com a API de gerenciamento do .NET

Você pode escrever um código C# para executar as funções do MSBuild ou do FTP para implantação. Se fizer isso, você poderá acessar a API de gerenciamento do REST do Azure para executar as funções de gerenciamento de site.

Para obter mais informações, consulte o recurso a seguir:

* [Automatizando tudo com as bibliotecas de gerenciamento do Azure e o .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx) Introdução à API de gerenciamento do .NET e links para mais documentação.

###<a name="cli"></a>Implantar da CLI (Interface de Linha de Comando) do Azure

É possível utilizar a linha de comando em máquinas Windows, Mac ou Linux para implantar utilizando o FTP. Se fizer isso, você também poderá acessar a API de gerenciamento do REST do Azure usando a CLI do Azure.

Para obter mais informações, consulte o recurso a seguir:

* [Ferramentas de linha de comando do Azure.](/downloads/#cmd-line-tools). Página de portal em Azure.com que fornece informações da ferramenta de linha de comando.

###<a name="webdeploy"></a>Implantar da linha de comando da Implantação na Web

A [Implantação da Web](http://www.iis.net/downloads/microsoft/web-deploy) é o software da Microsoft para a implantação do IIS que não só fornece recursos de sincronização de arquivos inteligentes, mas também pode executar ou coordenar várias outras tarefas relacionadas à implantação que não poderão ser automatizadas quando você usar o FTP. Por exemplo, a Implantação da Web pode implantar um novo banco de dados ou atualizações de banco de dados junto com seu aplicativo Web. A Implantação da Web também pode minimizar o tempo necessário para atualizar um site existente, pois ele pode copiar apenas os arquivos alterados de forma inteligente. O Microsoft WebMatrix, o Visual Studio, o Visual Studio Team Services e o Team Foundation Server têm suporte para a Implantação da Web interna, mas você também pode usar a Implantação da Web diretamente da linha de comando para automatizar a implantação. Os comandos de Implantação da Web são muito avançados, mas a curva de aprendizado pode ser acentuada.

Para obter mais informações, consulte o recurso a seguir:

* [Aplicativos Web simples: implantação](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog de David Ebbo sobre uma ferramenta que ele escreveu para facilitar o uso da Implantação da Web.
* [Ferramenta de Implantação de Web](http://technet.microsoft.com/library/dd568996). Documentação oficial no site do Microsoft TechNet. Desatualizada, mas ainda um bom ponto de partida.
* [Usando a Implantação da Web](http://www.iis.net/learn/publish/using-web-deploy). Documentação oficial no site do Microsoft IIS.NET. Também desatualizada, mas ainda um bom ponto de partida.
* [StackOverflow](http://www.stackoverflow.com). O melhor lugar para obter informações mais atuais sobre como usar a Implantação da Web da linha de comando.
* [Implantação da Web do ASP.NET usando o Visual Studio: implantação de linha de comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). O MSBuild é o mecanismo de compilação usado pelo Visual Studio e também pode ser usado na linha de comando para implantar aplicativos Web em "Aplicativos Web". Este tutorial faz parte de uma série que trata principalmente da implantação do Visual Studio.

##<a name="nextsteps"></a>Próximas etapas

Em algumas situações, você provavelmente desejará ser capaz de alternar facilmente entre a versão de preparo e a versão de produção do seu aplicativo Web. Para obter mais informações, consulte [Implantação preparada em Aplicativos Web](web-sites-staged-publishing.md).

Uma parte importante de qualquer fluxo de trabalho de implantação é ter um plano de backup e restauração. Para obter informações sobre o recurso de backup e restauração dos Aplicativos Web, consulte [Backups de Aplicativos Web](web-sites-backup.md).

Para obter informações sobre como usar o Controle de Acesso Baseado em Função do Azure para gerenciar o acesso à implantação de Aplicativos Web, consulte [RBAC e a publicação de Aplicativos Web](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/).

Para obter mais informações sobre outros tópicos de implantação, consulte a seção Implantar na [Documentação de Aplicativos Web](/documentation/services/web-sites/).

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
 

<!---HONumber=AcomDC_0211_2016-->