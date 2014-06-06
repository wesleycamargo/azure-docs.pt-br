<properties linkid="develop-media-services-how-to-guides-set-up-computer" urlDisplayName="Configurar o computador para os Serviços de Mídia" pageTitle="Como configurar o computador para Serviços de Mídia - Azure" metaKeywords="" description="Saiba quais são os pré-requisitos dos Serviços de Mídia ao usar o SDK dos Serviços de Mídia para .NET. Saiba também como criar um aplicativo do Visual Studio" metaCanonical="" services="media-services" documentationCenter="" title="Configurando seu computador para desenvolvimento de Serviços de Mídia" authors=""  solutions="" writer="migree" manager="" editor=""  />





<h1><a name="setup-dev"> </a><span class="short header">Configurando seu computador para desenvolvimento de Serviços de Mídia</span></h1> 

Esta seção contém os pré-requisitos gerais para desenvolvimento de Serviços de Mídia usando o SDK dos Serviços de Mídia para .NET. Também mostra aos desenvolvedores como criar um aplicativo do Visual Studio para desenvolvimento do SDK dos Serviços de Mídia. 

###Pré-requisitos

-   Uma conta dos Serviços de Mídia em uma assinatura nova ou existente do Azure. Consulte o tópico [Como criar uma conta de Serviços de Mídia (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/manage/services/media-services/how-to-create-a-media-services-account/).
-   Sistemas operacionais: Windows 7, Windows 2008 R2 ou Windows 8.
-   .NET Framework 4.5 ou .NET Framework 4.
-   Visual Studio 2012 ou Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express). 
-   Use o pacote [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices) para instalar o SDK do Azure para .NET. A seção a seguir mostra como usar o **NuGet** para instalar o SDK do Azure.
  
<h2><a name="setup-account"></a><span class="short header">Configurar uma conta do Azure para Serviços de Mídia</span></h2>

Para configurar sua conta de Serviços de Mídia, use o Portal de Gerenciamento do Azure (recomendado). Consulte o tópico [Como criar uma conta de Serviços de Mídia][]. Depois de criar sua conta no Portal de Gerenciamento, você estará pronto para configurar seu computador para desenvolvimento de Serviços de Mídia. 

###Criando um aplicativo no Visual Studio

Esta seção mostra como criar um projeto no Visual Studio e configurá-lo para o desenvolvimento de Serviços de Mídia.  Nesse caso, o projeto é um aplicativo de console C# do Windows, mas as mesmas etapas de instalação mostradas aqui se aplicam a outros tipos de projetos que você pode criar para aplicativos de Serviços de Mídia (por exemplo, um aplicativo do Windows Forms ou um aplicativo Web ASP.NET).

   1. Criar um novo **Aplicativo de Console** C# no Visual Studio 2012 ou no Visual Studio 2010 SP1. Digite o **nome**, o **Local** e o **Nome da solução** e  **OK**.
   2. Adicionar uma referência ao assembly **System.Configuration**. Para adicionar uma referência ao **System. Configuration**, no **Gerenciador de Soluções**, clique com o botão direito do mouse no nó **Referências** e selecione **Adicionar Referência...** Na caixa de diálogo **Gerenciar Referências**, selecione **System.Configuration** e clique em **OK**.
   3. Adicionar referências ao **SDK do Azure para .NET.** (Microsoft.WindowsAzure.StorageClient.dll), **SDK dos Serviços de Mídia do Azure para .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) e bibliotecas do **WCF Data Services 5.0 para OData V3** (Microsoft.Data.OData.dll) usando o pacote [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices). 

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
		using Microsoft.WindowsAzure;
		using Microsoft.WindowsAzure.MediaServices.Client;

Neste ponto, você está pronto para começar a desenvolver um aplicativo de Serviços de Mídia.
   
<h2><a name="setup-account"></a><span class="short header">Conectar-se aos Serviços de Mídia</span></h2> 

Quase tudo o que é feito na programação dos Serviços de Mídia requer uma referência ao objeto de contexto do servidor. O contexto do servidor fornece acesso programático a todos os objetos de programação dos Serviços de Mídia.

Para obter uma referência ao contexto do servidor, retorne uma nova instância do tipo de contexto, como no exemplo de código a seguir. Passe o nome e a chave da conta dos Serviços de Mídia (que você obteve durante o processo de configuração da conta) para o construtor. 

	static CloudMediaContext GetContext()
	{
	    // Gets the service context. 
	    return new CloudMediaContext(_accountName, _accountKey);
	} 

Sempre é útil definir uma variável em nível de módulo do tipo **CloudMediaContext** para manter uma referência ao contexto do servidor retornado por um método, como **GetContext**. O restante dos exemplos de código deste tópico usa uma variável chamada **_context** para fazer referência ao contexto do servidor. 

<h2>Próximas etapas</h2>
Agora que você configurou seu computador e criou uma solução do Visual Studio para programação de Serviços de Mídia, vá para o tópico [Como criar um ativo criptografado e carregar no armazenamento][].
[Como criar uma conta de Serviços de Mídia]: http://go.microsoft.com/fwlink/?linkid=256662
[Como criar um ativo criptografado e carregar no armazenamento]:http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409

