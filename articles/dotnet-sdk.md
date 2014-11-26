<properties pageTitle="What is the Azure .NET SDK" metaKeywords="azure .net sdk" description="Learn what is included in the Azure .NET SDK." documentationCenter=".NET" title="What is the Azure .NET SDK" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra" />

# O que é o SDK do Azure para .NET?

O SDK do Azure para .NET é um conjunto de ferramentas do Visual Studio, ferramentas de linha de comando, binários de tempo de execução e bibliotecas de cliente que ajudam a desenvolver, testar e implantar aplicativos que são executados no Azure. Este artigo detalha o que você obtém quando instala o SDK. Você pode baixar o SDK na [página de downloads do Azure][página de downloads do Azure].

O SDK do Azure para .NET inclui também bibliotecas de cliente para serviços de consumo do Azure. Essas bibliotecas são instaladas separadamente utilizando o [NuGet][NuGet].

## Sumário

-   [O que está incluído quando você instala o SDK do Azure para .NET][O que está incluído quando você instala o SDK do Azure para .NET]
-   [O que não está incluído quando você instala o SDK do Azure para .NET][O que não está incluído quando você instala o SDK do Azure para .NET]
-   [Perguntas frequentes][Perguntas frequentes]
-   [Recursos][Recursos]

## <span id="included"></span></a>O que está incluído no SDK do Azure para .NET

O SDK do Azure para .NET instala os seguintes produtos:

-   [Visual Studio Express para Web][Visual Studio Express para Web]
-   [Microsoft ASP.NET e ferramentas da Web para Visual Studio][Microsoft ASP.NET e ferramentas da Web para Visual Studio]
-   [Ferramentas do Microsoft Azure para Microsoft Visual Studio][Ferramentas do Microsoft Azure para Microsoft Visual Studio]
-   [Ferramentas de criação do Microsoft Azure][Ferramentas de criação do Microsoft Azure]
-   [Emulador do Microsoft Azure][Emulador do Microsoft Azure]
-   [Emulador de armazenamento do Microsoft Azure][Emulador de armazenamento do Microsoft Azure]
-   [Ferramentas de armazenamento do Microsoft Azure][Ferramentas de armazenamento do Microsoft Azure]
-   [Bibliotecas do Microsoft Azure para .NET][Bibliotecas do Microsoft Azure para .NET]
-   [Complemento de publicação LightSwitch do Azure para Visual Studio][Complemento de publicação LightSwitch do Azure para Visual Studio]

### <span id="vwd"></span></a>Visual Studio Express para Web

Se você não tiver o Visual Studio em seu computador, o SDK instalará o [Visual Studio Express para Web][1].

### <span id="wte"></span></a>Microsoft ASP.NET e ferramentas da Web para Visual Studio

Isto permite que você trabalhe com sites do Azure:

-   [Publicar projetos da Web em sites do Azure][Publicar projetos da Web em sites do Azure].
-   [Publicar projetos de aplicativos de console no WebJobs do Azure][Publicar projetos de aplicativos de console no WebJobs do Azure].
-   [Criar recursos de sites do Azure e de bancos de dados SQL ao criar um novo projeto da Web ou ao publicar um projeto da Web][Criar recursos de sites do Azure e de bancos de dados SQL ao criar um novo projeto da Web ou ao publicar um projeto da Web].
-   [Criar scripts de implantação do PowerShell ao criar novos sites][Criar scripts de implantação do PowerShell ao criar novos sites].
-   [Gerenciar e solucionar problemas de sites do Azure no Gerenciador de Servidores][Gerenciar e solucionar problemas de sites do Azure no Gerenciador de Servidores].
-   [Executar no modo de depuração remotamente para sites e WebJobs][Executar no modo de depuração remotamente para sites e WebJobs].

> [WACOM.NOTE] Você não precisa instalar o SDK do Azure para .NET para utilizar esses recursos, eles já são incluídos nas atualizações do Visual Studio.

### <span id="tools"></span></a>Ferramentas do Microsoft Azure para Microsoft Visual Studio

Isso permite que você trabalhe com os Serviços de Nuvem do Azure e Máquinas Virtuais:

-   [Criar, abrir e publicar projetos de serviço de nuvem][Criar, abrir e publicar projetos de serviço de nuvem].
-   [Criar pacotes de implantação para projetos de serviço de nuvem][Criar pacotes de implantação para projetos de serviço de nuvem].
-   [Criar Máquinas Virtuais do Azure ao criar novos projetos da Web][Criar Máquinas Virtuais do Azure ao criar novos projetos da Web].
-   [Criar novos scripts do PowerShell ao criar novas máquina virtuais][Criar scripts de implantação do PowerShell ao criar novos sites].
-   [Visualizar e gerenciar configurações de projeto de serviços em nuvem em janelas de Propriedades de Projeto no Visual Studio][Visualizar e gerenciar configurações de projeto de serviços em nuvem em janelas de Propriedades de Projeto no Visual Studio].
-   Visualizar e gerenciar [serviços de nuvem][serviços de nuvem], [máquinas virtuais][máquinas virtuais] e [Barramento de Serviço][Barramento de Serviço] no Gerenciador de Servidores.
-   [Executar em modo de depuração remotamente para serviços de nuvem e máquinas virtuais][Executar em modo de depuração remotamente para serviços de nuvem e máquinas virtuais].

### <span id="auth"></span></a>Ferramentas de criação do Microsoft Azure

Incluem os itens a seguir:

-   A [ferramenta de linha de comando CSPack][ferramenta de linha de comando CSPack] para criar pacotes de implantação.
-   A [ferramenta de linha de comando CSEncrypt][ferramenta de linha de comando CSEncrypt] para criptografar senhas que são utilizadas para acessar instâncias de função de serviço de nuvem por meio de uma conexão por área de trabalho remota.
-   OS binários de tempo de execução de que os projetos de serviço de nuvem precisam para se comunicar com seu ambiente de tempo de execução e para diagnósticos. Esses binários não estão disponíveis em pacotes NuGet.

### <span id="emulator"></span></a>Emulador do Microsoft Azure

O [Emulador do Azure][Emulador do Azure] simula o ambiente do serviço de nuvem para que você possa testar serviços de nuvem localmente em seu computador antes de implantá-los no Azure.

### <span id="stgemulator"></span></a>Emulador de armazenamento do Microsoft Azure

O [Emulador de Armazenamento do Azure][Emulador de Armazenamento do Azure] utiliza uma instância do SQL Server e o sistema de arquivo local para simular o Armazenamento do Azure (filas, tabelas, blobs), para que seja possível testar localmente.

### <span id="stgtools"></span></a>Ferramentas de armazenamento do Microsoft Azure

Instala o [AzCopy][AzCopy], uma ferramenta de linha de comando utilizada para transferir dados de e para uma conta de Armazenamento do Azure.

### <span id="libraries"></span></a>Bibliotecas do Microsoft Azure para .NET

Incluem os itens a seguir:

-   Pacotes NuGet para Caching, Barramento de Serviço e Armazenamento do Azure, que são armazenados em seu computador de modo que o Visual Studio possa criar novos projetos de serviço de nuvem enquanto estiver offline.
-   Um plug-in do Visual Studio que permite que projetos de [Cache na Função][Cache na Função] sejam executados localmente no Visual Studio.

### <span id="ls"></span></a>Complemento de publicação LightSwitch do Azure para Visual Studio

Permite que você [publique projetos do LightSwitch em sites do Azure][publique projetos do LightSwitch em sites do Azure]. O complemento do LightSwitch é incluído nas atualizações do Visual Studio, bem como no SDK do Azure para .NET. Instalar o SDK garante que você tenha a versão mais recente do complemento.

## <span id="notincluded"></span></a>O que não está incluído quando você instala o SDK do Azure para .NET

Há algumas coisas que você poderia desejar para o desenvolvimento no Azure que não estão inclusas quando o SDK é instalado. Entre elas, as mais importantes são:

-   [Bibliotecas de cliente][NuGet].

    O SDK do Azure inclui bibliotecas de cliente para serviços de consumo do Azure, mas nem todas elas são instaladas quando você instala o SDK. Se o seu aplicativo precisar de uma biblioteca cliente que o SDK não instala, você poderá obtê-la no [NuGet.org][NuGet]. Se o seu aplicativo usar uma biblioteca de cliente que o SDK instala, é uma boa prática atualizá-la com a versão atual no NuGet.org.

    **Cópias locais de bibliotecas de clientes.** O SDK do Azure para .NET copia para seu computador os pacotes do NuGet relativos a algumas bibliotecas de cliente do Azure, como Armazenamento, Barramento de Serviço e Caching. Essas bibliotecas clientes são incluídas automaticamente em novos projetos de serviço de nuvem, de modo que os pacotes locais do NuGet permitem que o Visual Studio crie projetos mesmo que você não esteja conectado à Internet. Normalmente, as bibliotecas de clientes são atualizadas com mais frequência do que as versões do SDK são lançadas, de modo que as bibliotecas no NuGet.org frequentemente são mais atuais do que as obtidas com o SDK.

    **Modelos de projeto que incluem bibliotecas de clientes.** Somente modelos de projeto do [Serviço de Nuvem do Azure][Criar, abrir e publicar projetos de serviço de nuvem] e do [Serviço Móvel do Azure][Serviço Móvel do Azure] incluem automaticamente algumas bibliotecas de clientes. Para outras bibliotecas ou outros modelos, instale os [pacotes NuGet de bibliotecas de clientes][NuGet] de que você precisa.

-   [PowerShell do Azure][PowerShell do Azure].

    O PowerShell do Azure permite que você [automatize a criação e implantação de ambiente do Azure][automatize a criação e implantação de ambiente do Azure].

-   [Modelos de projeto do Serviço Móvel do Azure][Serviço Móvel do Azure].

    Os Modelos de Serviço Móvel estão disponíveis apenas na atualização 2 do Visual Studio 2013 e posteriores. Eles não estão disponíveis no Visual Studio 2012 ou versões anteriores nem na atualização 1 do Visual Studio 2013 ou anteriores, mesmo que você instale o SDK do Azure para .NET.

## <span id="faq"></span></a>Perguntas frequentes

-   [Muitos recursos do Azure já estão no Visual Studio. Preciso instalar o SDK do Azure para .NET?][Muitos recursos do Azure já estão no Visual Studio. Preciso instalar o SDK do Azure para .NET?]
-   [Quero uma biblioteca de cliente. Preciso instalar o SDK do Azure para .NET para obtê-la?][Quero uma biblioteca de cliente. Preciso instalar o SDK do Azure para .NET para obtê-la?]
-   [Onde posso encontrar versões mais antigas do SDK do Azure para .NET?][Onde posso encontrar versões mais antigas do SDK do Azure para .NET?]
-   [Qual é a política de ciclo de vida para versões do SDK do Azure para .NET?][Qual é a política de ciclo de vida para versões do SDK do Azure para .NET?]
-   [Com quais versões de SOs convidados o SDK do Azure para .NET é compatível?][Com quais versões de SOs convidados o SDK do Azure para .NET é compatível?]

### <span id="azinvs"></span></a>Muitos recursos do Azure já estão no Visual Studio. Preciso instalar o SDK do Azure para .NET?

É uma boa prática instalar o SDK se você quiser desenvolver para Azure usando as ferramentas mais recentes. Se preferir não instalar o SDK, você pode fazê-lo caso as condições a seguir sejam atendidas:

-   Você instalou a mais recente [Atualização do Visual Studio][Atualização do Visual Studio].
-   Você está desenvolvendo apenas para os Sites e Serviços Móveis do Azure, não para os Serviços de Nuvem ou Máquinas Virtuais.
-   Seu aplicativo não usa o Armazenamento, ou usa o Armazenamento, mas você não precisa do Emulador de Armazenamento ou da ferramenta AzCopy.

### <span id="clientlib"></span></a>Quero uma biblioteca de cliente. Preciso instalar o SDK do Azure para .NET para obtê-la?

O SDK instala bibliotecas de cliente somente para que você possa criar projetos de serviços em nuvem sem estar conectado à internet. Bibliotecas de cliente atualizadas estão disponíveis em pacotes NuGet em [NuGet.org][NuGet]. Para obter mais informações, consulte [O que não está incluído quando você instala o SDK do Azure para .NET][O que não está incluído quando você instala o SDK do Azure para .NET] no início deste documento.

### <span id="olderversions"></span></a>Onde posso encontrar versões mais antigas do SDK do Azure para .NET?

Para obter versões mais antigas, consulte a página de downloads do [SDK do Azure para .NET][SDK do Azure para .NET].

### <span id="lifecycle"></span></a>Qual é a política de ciclo de vida para versões do SDK do Azure para .NET?

Consulte a [Política de Ciclo de Vida do Suporte aos Serviços de Nuvem do Microsoft Azure][Política de Ciclo de Vida do Suporte aos Serviços de Nuvem do Microsoft Azure].

### <span id="guestos"></span></a>Com quais versões de SOs convidados o SDK do Azure para .NET é compatível?

Consulte a [Matriz de Compatibilidade de Versões de SOs Convidados e do SDK do Azure][Matriz de Compatibilidade de Versões de SOs Convidados e do SDK do Azure].

## <span id="resources"></span></a>Recursos

Para baixar o SDK do Azure para .NET ou uma biblioteca de cliente, consulte a [Página de Downloads do Azure][página de downloads do Azure].

Para obter o código-fonte do SDK do Azure para .NET, incluindo as bibliotecas de cliente, consulte [GitHub.com/Azure][GitHub.com/Azure].

Para obter a documentação de referência da biblioteca de cliente do Azure, consulte [Referência do .NET do Azure][Referência do .NET do Azure].

  [página de downloads do Azure]: /pt-br/downloads/
  [NuGet]: http://go.microsoft.com/fwlink/?LinkId=510472
  [O que está incluído quando você instala o SDK do Azure para .NET]: #included
  [O que não está incluído quando você instala o SDK do Azure para .NET]: #notincluded
  [Perguntas frequentes]: #faq
  [Recursos]: #resources
  [Visual Studio Express para Web]: #vwd
  [Microsoft ASP.NET e ferramentas da Web para Visual Studio]: #wte
  [Ferramentas do Microsoft Azure para Microsoft Visual Studio]: #tools
  [Ferramentas de criação do Microsoft Azure]: #auth
  [Emulador do Microsoft Azure]: #emulator
  [Emulador de armazenamento do Microsoft Azure]: #stgemulator
  [Ferramentas de armazenamento do Microsoft Azure]: #stgtools
  [Bibliotecas do Microsoft Azure para .NET]: #libraries
  [Complemento de publicação LightSwitch do Azure para Visual Studio]: #ls
  [1]: http://www.visualstudio.com/pt-br/products/visual-studio-express-vs.aspx
  [Publicar projetos da Web em sites do Azure]: ../web-sites-dotnet-get-started/
  [Publicar projetos de aplicativos de console no WebJobs do Azure]: ../websites-dotnet-deploy-webjobs/
  [Criar recursos de sites do Azure e de bancos de dados SQL ao criar um novo projeto da Web ou ao publicar um projeto da Web]: ../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [Criar scripts de implantação do PowerShell ao criar novos sites]: http://msdn.microsoft.com/pt-br/library/dn642480.aspx
  [Gerenciar e solucionar problemas de sites do Azure no Gerenciador de Servidores]: ../web-sites-dotnet-troubleshoot-visual-studio/#sitemanagement
  [Executar no modo de depuração remotamente para sites e WebJobs]: ../web-sites-dotnet-troubleshoot-visual-studio/#remotedebug
  [Criar, abrir e publicar projetos de serviço de nuvem]: ../cloud-services-dotnet-get-started/
  [Criar pacotes de implantação para projetos de serviço de nuvem]: http://msdn.microsoft.com/pt-br/library/ff683672.aspx
  [Criar Máquinas Virtuais do Azure ao criar novos projetos da Web]: ../virtual-machines-dotnet-create-visual-studio-powershell/
  [Visualizar e gerenciar configurações de projeto de serviços em nuvem em janelas de Propriedades de Projeto no Visual Studio]: http://msdn.microsoft.com/pt-br/library/ee405486.aspx
  [serviços de nuvem]: http://msdn.microsoft.com/pt-br/library/ff683675.aspx
  [máquinas virtuais]: http://msdn.microsoft.com/pt-br/library/jj131259.aspx
  [Barramento de Serviço]: http://msdn.microsoft.com/pt-br/library/jj149828.aspx
  [Executar em modo de depuração remotamente para serviços de nuvem e máquinas virtuais]: http://msdn.microsoft.com/pt-br/library/ff683670.aspx
  [ferramenta de linha de comando CSPack]: http://msdn.microsoft.com/pt-br/library/gg432988.aspx
  [ferramenta de linha de comando CSEncrypt]: http://msdn.microsoft.com/pt-br/library/hh404001.aspx
  [Emulador do Azure]: http://msdn.microsoft.com/pt-br/library/dn339018.aspx
  [Emulador de Armazenamento do Azure]: http://msdn.microsoft.com/pt-br/library/hh403989.aspx
  [AzCopy]: http://aka.ms/AzCopy
  [Cache na Função]: http://msdn.microsoft.com/pt-br/library/dn386103.aspx
  [publique projetos do LightSwitch em sites do Azure]: http://msdn.microsoft.com/pt-br/library/jj131261.aspx
  [Serviço Móvel do Azure]: ../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/
  [PowerShell do Azure]: ../install-configure-powershell/
  [automatize a criação e implantação de ambiente do Azure]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Muitos recursos do Azure já estão no Visual Studio. Preciso instalar o SDK do Azure para .NET?]: #azinvs
  [Quero uma biblioteca de cliente. Preciso instalar o SDK do Azure para .NET para obtê-la?]: #clientlib
  [Onde posso encontrar versões mais antigas do SDK do Azure para .NET?]: #olderversions
  [Qual é a política de ciclo de vida para versões do SDK do Azure para .NET?]: #lifecycle
  [Com quais versões de SOs convidados o SDK do Azure para .NET é compatível?]: #guestos
  [Atualização do Visual Studio]: http://www.visualstudio.com/pt-br/downloads/download-visual-studio-vs#DownloadFamilies_5
  [SDK do Azure para .NET]: /pt-br/downloads/archive-net-downloads/
  [Política de Ciclo de Vida do Suporte aos Serviços de Nuvem do Microsoft Azure]: http://support.microsoft.com/gp/azure-cloud-lifecycle-faq
  [Matriz de Compatibilidade de Versões de SOs Convidados e do SDK do Azure]: http://msdn.microsoft.com/pt-br/library/ee924680.aspx
  [GitHub.com/Azure]: https://github.com/azure/
  [Referência do .NET do Azure]: /pt-br/develop/net/reference/
