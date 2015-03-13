<properties 
	pageTitle="O que é o SDK .NET do Azure?" 
	description="Saiba o que está incluído no SDK .NET do Azure." 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="tdykstra"/>

# O que é o SDK do Azure para .NET?

O SDK do Azure para .NET é um conjunto de ferramentas do Visual Studio, ferramentas de linha de comando, binários de tempo de execução e bibliotecas de cliente que ajudam a desenvolver, testar e implantar aplicativos que são executados no Azure. Este artigo detalha o que você obtém quando instala o SDK. Você pode baixar o SDK na [página de downloads do Azure](/pt-br/downloads/). 

O SDK do Azure para .NET inclui também bibliotecas de cliente para serviços de consumo do Azure. Essas bibliotecas são instaladas separadamente utilizando o [NuGet](http://go.microsoft.com/fwlink/?LinkId=510472).

## Sumário

- [O que está incluído quando você instala o SDK do Azure para .NET](#included)
- [O que não está incluído quando você instala o SDK do Azure para .NET](#notincluded)
- [Perguntas frequentes](#faq)
- [Recursos](#resources)

##<a id="included"></a>O que está incluído no SDK do Azure para .NET

O SDK do Azure para .NET instala os seguintes produtos:

- [Visual Studio Express para Web](#vwd)
- [Microsoft ASP.NET e ferramentas da Web para Visual Studio](#wte)
- [Ferramentas do Microsoft Azure para Microsoft Visual Studio](#tools)
- [Ferramentas de criação do Microsoft Azure](#auth)
- [Emulador do Microsoft Azure](#emulator)
- [Emulador do Armazenamento do Microsoft Azure](#stgemulator)
- [Ferramentas do Armazenamento do Microsoft Azure](#stgtools)
- [Bibliotecas do Microsoft Azure para .NET](#libraries)
- [Complemento de publicação LightSwitch do Azure para Visual Studio](#ls)

###<a id="vwd"></a>Visual Studio Express para Web

Se você não tiver o Visual Studio em seu computador, o SDK instalará o [Visual Studio Express para Web](http://www.visualstudio.com/pt-br/products/visual-studio-express-vs.aspx). 
 
###<a id="wte"></a>Microsoft ASP.NET e ferramentas da Web para Visual Studio

Isto permite que você trabalhe com sites do Azure:

* [Publicar projetos da Web em sites do Azure](../web-sites-dotnet-get-started/).
* [Publicar projetos de aplicativos de console no WebJobs do Azure](../websites-dotnet-deploy-webjobs/).
* [Criar recursos de sites do Azure e do banco de dados SQL ao criar um novo projeto Web ou ao publicar um projeto Web](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/).
* [Criar scripts de implantação do PowerShell ao criar novos sites](http://msdn.microsoft.com/library/dn642480.aspx).
* [Gerenciar e solucionar problemas de sites do Azure no Gerenciador de Servidores](../web-sites-dotnet-troubleshoot-visual-studio/#sitemanagement).
* [Executar no modo de depuração remotamente para sites e WebJobs](../web-sites-dotnet-troubleshoot-visual-studio/#remotedebug). 

>[WACOM.NOTE] Você não precisa instalar o SDK do Azure para .NET para utilizar esses recursos, eles já são incluídos nas atualizações do Visual Studio. 

###<a id="tools"></a>Ferramentas do Microsoft Azure para Microsoft Visual Studio

Isso permite que você trabalhe com os Serviços de Nuvem do Azure e Máquinas Virtuais:

* [Criar, abrir e publicar projetos de serviço de nuvem](../cloud-services-dotnet-get-started/).
* [Criar pacotes de implantação para projetos de serviço de nuvem](http://msdn.microsoft.com/library/ff683672.aspx).
* [Criar Máquinas Virtuais do Azure ao criar novos projetos da Web](../virtual-machines-dotnet-create-visual-studio-powershell/).
* [Criar novos scripts do PowerShell ao criar novas máquinas virtuais](http://msdn.microsoft.com/library/dn642480.aspx).
* [Exibir e gerenciar configurações de projeto de serviço de nuvem em janelas de Propriedades de Projeto no Visual Studio](http://msdn.microsoft.com/library/ee405486.aspx).
* Exibir e gerenciar [serviços de nuvem](http://msdn.microsoft.com/library/ff683675.aspx), [máquinas virtuais](http://msdn.microsoft.com/library/jj131259.aspx) e [Barramento de Serviço](http://msdn.microsoft.com/library/jj149828.aspx) no Gerenciador de Servidores. 
* [Executar em modo de depuração remotamente para serviços de nuvem e máquinas virtuais](http://msdn.microsoft.com/library/ff683670.aspx).

###<a id="auth"></a>Ferramentas de criação do Microsoft Azure

Incluem os itens a seguir:

* A [ferramenta de linha de comando CSPack](http://msdn.microsoft.com/library/gg432988.aspx) para criar pacotes de implantação.
* A [ferramenta de linha de comando CSEncrypt](http://msdn.microsoft.com/library/hh404001.aspx) para criptografar senhas que são utilizadas para acessar instâncias de função de serviço de nuvem por meio de uma conexão de área de trabalho remota.
* Os binários de tempo de execução de que os projetos de serviço de nuvem precisam para se comunicar com seu ambiente de tempo de execução e para diagnósticos. Esses binários não estão disponíveis em pacotes NuGet.

###<a id="emulator"></a>Emulador do Microsoft Azure

O [Emulador do Azure](http://msdn.microsoft.com/library/dn339018.aspx) simula o ambiente do serviço de nuvem para que você possa testar serviços de nuvem localmente em seu computador antes de implantá-los no Azure.

###<a id="stgemulator"></a>Emulador do Armazenamento do Microsoft Azure

O [Emulador de Armazenamento do Azure](http://msdn.microsoft.com/library/hh403989.aspx) utiliza uma instância do SQL Server e o sistema de arquivos local para simular o Armazenamento do Azure (filas, tabelas, blobs), para que seja possível testar localmente. 

###<a id="stgtools"></a>Ferramentas do Armazenamento do Microsoft Azure

Instala o [AzCopy](http://aka.ms/AzCopy), uma ferramenta de linha de comando utilizada para transferir dados de e para uma conta de Armazenamento do Azure.

###<a id="libraries"></a>Bibliotecas do Microsoft Azure para .NET

Incluem os itens a seguir:

* Pacotes NuGet para Caching, Barramento de Serviço e Armazenamento do Azure, que são armazenados em seu computador de modo que o Visual Studio possa criar novos projetos de serviço de nuvem enquanto estiver offline.
* Um plug-in do Visual Studio que permite que projetos de [Cache na Função](http://msdn.microsoft.com/library/dn386103.aspx) sejam executados localmente no Visual Studio. 

###<a id="ls"></a>Complemento de publicação LightSwitch do Azure para Visual Studio

Permite que você [publique projetos do LightSwitch em sites do Azure](http://msdn.microsoft.com/library/jj131261.aspx). O complemento do LightSwitch é incluído nas atualizações do Visual Studio, bem como no SDK do Azure para .NET. Instalar o SDK garante que você tenha a versão mais recente do complemento. 

##<a id="notincluded"></a>O que não está incluído quando você instala o SDK do Azure para .NET

Há algumas coisas que você poderia desejar para o desenvolvimento no Azure que não estão inclusas quando o SDK é instalado. Entre elas, as mais importantes são:

* [Bibliotecas de cliente](http://go.microsoft.com/fwlink/?LinkId=510472). 

	O SDK do Azure inclui bibliotecas de cliente para serviços de consumo do Azure, mas nem todas elas são instaladas quando você instala o SDK. Se o seu aplicativo precisar de uma biblioteca cliente que o SDK não instala, você poderá obtê-la no [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Se o seu aplicativo usar uma biblioteca de cliente que o SDK instala, é uma boa prática atualizá-la com a versão atual no NuGet.org.

  	**Cópias locais de bibliotecas de clientes.** O SDK do Azure para .NET copia para seu computador os pacotes do NuGet relativos a algumas bibliotecas de cliente do Azure, como Armazenamento, Barramento de Serviço e Caching. Essas bibliotecas clientes são incluídas automaticamente em novos projetos de serviço de nuvem, de modo que os pacotes locais do NuGet permitem que o Visual Studio crie projetos mesmo que você não esteja conectado à Internet. Normalmente, as bibliotecas de clientes são atualizadas com mais frequência do que as versões do SDK são lançadas, de modo que as bibliotecas no NuGet.org frequentemente são mais atuais do que as obtidas com o SDK. 

	**Modelos de projeto que incluem bibliotecas de clientes.** Somente modelos de projeto do [Serviço de Nuvem do Azure](../cloud-services-dotnet-get-started/) e do [Serviço Móvel do Azure](../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/) incluem automaticamente algumas bibliotecas de clientes. Para outras bibliotecas ou outros modelos, instale os [pacotes NuGet de bibliotecas de clientes](http://go.microsoft.com/fwlink/?LinkId=510472) de que você precisa.

* [PowerShell do Azure](../install-configure-powershell/). 

	O PowerShell do Azure permite que você [automatize a criação e implantação de ambiente do Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* [Modelos de projeto do Serviço Móvel do Azure](../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/).

	Os Modelos de Serviço Móvel estão disponíveis apenas na atualização 2 do Visual Studio 2013 e posteriores. Eles não estão disponíveis no Visual Studio 2012 ou versões anteriores nem na atualização 1 do Visual Studio 2013 ou anteriores, mesmo que você instale o SDK do Azure para .NET.

##<a id="faq"></a>Perguntas frequentes

- [Muitos recursos do Azure já estão no Visual Studio. Preciso instalar o SDK do Azure para .NET?](#azinvs)
- [Quero uma biblioteca de cliente. Preciso instalar o SDK do Azure para .NET para obtê-la?](#clientlib)
- [Onde posso encontrar versões mais antigas do SDK do Azure para .NET?](#olderversions)
- [Qual é a política de ciclo de vida para versões do SDK do Azure para .NET?](#lifecycle)
- [Com quais versões de SOs convidados o SDK do Azure para .NET é compatível?](#guestos)

###<a id="azinvs"></a>Muitos recursos do Azure já estão no Visual Studio. Preciso instalar o SDK do Azure para .NET?

É uma boa prática instalar o SDK se você quiser desenvolver para Azure usando as ferramentas mais recentes. Se preferir não instalar o SDK, você pode fazê-lo caso as condições a seguir sejam atendidas:

* Você instalou a mais recente [Atualização do Visual Studio](http://www.visualstudio.com/pt-br/downloads/download-visual-studio-vs#DownloadFamilies_5).
* Você está desenvolvendo apenas para os Sites e Serviços Móveis do Azure, não para os Serviços de Nuvem ou Máquinas Virtuais.
* Seu aplicativo não usa o Armazenamento, ou usa o Armazenamento, mas você não precisa do Emulador de Armazenamento ou da ferramenta AzCopy.

###<a id="clientlib"></a>Quero uma biblioteca de cliente. Preciso instalar o SDK do Azure para .NET para obtê-la?

O SDK instala bibliotecas de cliente somente para que você possa criar projetos de serviços de nuvem sem estar conectado à Internet. Bibliotecas de cliente atualizadas estão disponíveis em pacotes NuGet em [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Para obter mais informações, consulte [O que não está incluído quando você instala o SDK do Azure para .NET](#notincluded) no início deste documento.

###<a id="olderversions"></a>Onde posso encontrar versões mais antigas do SDK do Azure para .NET?

Para obter versões mais antigas, consulte a página de downloads do [SDK do Azure para .NET](/pt-br/downloads/archive-net-downloads/). 

###<a id="lifecycle"></a>Qual é a política de ciclo de vida para versões do SDK do Azure para .NET?

Consulte a [Política de Ciclo de Vida do Suporte aos Serviços de Nuvem do Microsoft Azure](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

###<a id="guestos"></a>Com quais versões de SOs convidados o SDK do Azure para .NET é compatível?

Consulte a [Matriz de Compatibilidade de Versões de SOs Convidados e do SDK do Azure](http://msdn.microsoft.com/library/ee924680.aspx).



##<a id="resources"></a>Recursos

Para baixar o SDK do Azure para .NET ou uma biblioteca de cliente, consulte a [página de Downloads do Azure](/pt-br/downloads/).

Para obter o código-fonte do SDK do Azure para .NET, incluindo as bibliotecas de cliente, consulte [GitHub.com/Azure](https://github.com/azure/).

Para obter a documentação de referência da biblioteca de cliente do Azure, consulte [Referência do .NET do Azure](/pt-br/develop/net/reference/). 

<!--HONumber=46--> 
