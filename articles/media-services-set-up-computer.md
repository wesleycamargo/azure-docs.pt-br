<properties 
	pageTitle="Como configurar o computador para Serviços de Mídia - Azure" 
	description="Conheça os pré-requisitos para os Serviços de Mídia usando o SDK dos Serviços de Mídia para .NET. Saiba também como criar um aplicativo do Visual Studio." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>





<h1><a name="setup-dev"> </a><span class="short header">Configurando seu computador para o desenvolvimento de Serviços de Mídia</span></h1> 

Esta seção contém os pré-requisitos gerais para desenvolvimento de Serviços de Mídia usando o SDK dos Serviços de Mídia para .NET. Também mostra aos desenvolvedores como criar um aplicativo do Visual Studio para desenvolvimento do SDK dos Serviços de Mídia. 

### Pré-requisitos

-   Uma conta dos Serviços de Mídia em uma assinatura nova ou existente do Azure. Consulte o tópico [Como criar uma conta de Serviços de Mídia](media-services-create-account.md).
-   Sistemas operacionais: Windows 7, Windows 2008 R2 ou Windows 8.
-   .NET Framework 4.
-   Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express). 
-   Use o pacote [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices) para instalar o SDK do Azure para .NET. A seção a seguir mostra como usar o **Nuget** para instalar o SDK do Azure.
  
<h2><a name="setup-account"></a><span class="short header">Configurando uma conta do Azure para Serviços de Mídia</span></h2>

Para configurar sua conta de Serviços de Mídia, use o Portal de Gerenciamento do Azure (recomendado). Consulte o tópico [Como criar uma conta de Serviços de Mídia][]. Depois de criar sua conta no Portal de Gerenciamento, você estará pronto para configurar seu computador para desenvolvimento de Serviços de Mídia. 

### Criando um aplicativo no Visual Studio

Esta seção mostra como criar um projeto no Visual Studio e configurá-lo para o desenvolvimento de Serviços de Mídia.  Nesse caso, o projeto é um aplicativo de console C# do Windows, mas as mesmas etapas de configuração mostradas aqui se aplicam a outros tipos de projetos que podem ser criados para aplicativos de Serviços de Mídia (por exemplo, um aplicativo do Windows Forms ou um aplicativo Web ASP .NET).

   1. Crie um novo **aplicativo de console** do Visual C# no  Visual Studio 2013, no Visual Studio 2012 ou no Visual Studio 2010 SP1. Digite o **nome**, o **Local** e o **Nome da solução** e **OK**.
   2. Defina a estrutura de destino como .NET Framework 4. Para fazer isso, clique no botão direito do mouse sobre o projeto Visual Studio e selecione Propriedades. Na guia Aplicativo, defina .NET Framework 4 para a estrutura de destino.   
   3. Adicione uma referência ao assembly **System.Configuration**. Para adicionar uma referência ao **System. Configuration**, no **Gerenciador de Soluções**, clique com o botão direito do mouse no nó **Referências** e selecione **Adicionar Referência...**. Na caixa de diálogo **Gerenciar Referências**, selecione **System.Configuration** e clique em **OK**.
   4. Use o pacote [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices) para adicionar referências ao **SDK do Azure para .NET.** (todos os outros conjuntos dependentes serão instalados também).  

	Para adicionar referências usando o NuGet, proceda da seguinte maneira: No **Menu Principal** do Visual Studio, selecione **FERRAMENTAS** -> **Gerenciador de Pacotes da Biblioteca** -> **Console do Gerenciador de Pacotes**. Na janela do console, digite **Install-Package windowsazure.mediaservices** e pressione **Enter**.
   4. Substitua a instruções using existentes no início do arquivo Program.cs pelo código a seguir.

   		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure.MediaServices.Client;

Neste ponto, você está pronto para começar a desenvolver um aplicativo de Serviços de Mídia.    
   
<h2><a name="setup-account"></a><span class="short header">Conectar-se aos Serviços de Mídia</span></h2> 

Quase tudo o que é feito na programação dos Serviços de Mídia requer uma referência ao objeto de contexto do servidor. O contexto do servidor fornece acesso programático a todos os objetos de programação dos Serviços de Mídia.

Para obter uma referência ao contexto do servidor, crie uma nova instância do tipo de contexto, como no exemplo de código a seguir. Passe o nome e a chave da conta dos Serviços de Mídia (que você obteve durante o processo de configuração da conta) para o construtor. 

    // Create and cache the Media Services credentials in a static class variable.
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);

	// Use the cached credentials to create CloudMediaContext.
	_context = new CloudMediaContext(_cachedCredentials);


Sempre é útil definir uma variável em nível de módulo do tipo **CloudMediaContext** para manter uma referência ao contexto do servidor. Para obter mais informações, consulte [Conectando-se aos Serviços de Mídia com o SDK dos Serviços de Mídia para .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

O restante dos exemplos de código deste tópico usa uma variável chamada **_context** para fazer referência ao contexto do servidor. 

<h2>Próximas etapas</h2>
Agora que você configurou seu computador e criou uma solução do Visual Studio para programação de Serviços de Mídia, vá para o tópico [Como criar um ativo criptografado e carregar no armazenamento][].
[Como criar uma conta de Serviços de Mídia]: ../media-services-create-account/
[Como criar um ativo criptografado e carregar no armazenamento]: ../media-services-create-encrypted-asset-upload-storage/



<!--HONumber=42-->
