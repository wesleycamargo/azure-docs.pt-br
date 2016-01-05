<properties 
	pageTitle="Como configurar o computador para desenvolvimento dos serviços de mídia com o .NET" 
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
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/05/2015"
	ms.author="juliako"/>

#Desenvolvimento de serviços de mídia com o .NET

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Este tópico discute como começar a desenvolver aplicativos de serviços de mídia usando o .NET.

A biblioteca do **SDK do .NET dos Serviços de Mídia** permite que você programe em relação aos serviços de mídia usando o .NET. Para facilitar ainda mais o desenvolvimento com o .NET, a biblioteca de **Extensões do SDK do .NET dos Serviços de Mídia** é fornecida. Esta biblioteca contém um conjunto de métodos de extensão e funções auxiliares que simplificarão seu código .NET. As duas bibliotecas estão disponíveis por meio de **NuGet** e **GitHub**.


##Pré-requisitos

-   Uma conta dos Serviços de Mídia em uma assinatura nova ou existente do Azure. Consulte o tópico [Como criar uma conta de Serviços de Mídia](media-services-create-account.md).
-   Sistemas operacionais: Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
-   .NET Framework 4.5.
-    Visual Studio 2015, Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express).


##Criar e configurar um projeto do Visual Studio

Esta seção mostra como criar um projeto no Visual Studio e configurá-lo para o desenvolvimento de Serviços de Mídia. Nesse caso, o projeto é um aplicativo de console C# do Windows, mas as mesmas etapas de configuração mostradas aqui se aplicam a outros tipos de projetos que podem ser criados para aplicativos de Serviços de Mídia (por exemplo, um aplicativo do Windows Forms ou um aplicativo Web ASP .NET).

Esta seção mostra como usar **NuGet** para adicionar o SDK do .NET dos Serviços de Mídia e outras bibliotecas dependentes.

Como alternativa, você pode obter os bits mais recentes do SDK do .NET dos Serviços de Mídia do GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) e [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), criar a solução e adicionar as referências ao projeto do cliente. Observe que todas as dependências necessárias obtém por download e são extraídas automaticamente.

1. Crie um novo Aplicativo de Console C# no Visual Studio 2013, no Visual Studio 2012 ou no Visual Studio 2010 SP1. Digite o **nome**, o **local** e o **Nome da solução** e OK.

2. Compilar a solução.

2. Use o **NuGet** para instalar e adicionar **extensões do SDK do .NET dos Serviços de Mídia do Azure**. Instalar esse pacote também instala o **SDK do .NET dos Serviços de Mídia** e adiciona todas as outras dependências necessárias.
1. Certifique-se de que você tenha a versão mais recente do NuGet instalada. Para obter mais informações e instruções de instalação, consulte [NuGet](http://nuget.codeplex.com/).

2. No Gerenciador de Solução, clique o nome do projeto e escolha gerenciar pacotes NuGet...

A caixa de diálogo Gerenciar Pacotes NuGet será exibida.

3. Na Galeria Online, procure por extensões de serviços de mídia do Azure, escolha extensões do SDK do .NET dos Serviços de Mídia e, em seguida, clique no botão Instalar.

O projeto é modificado e faz referência para as extensões do SDK do .NET dos Serviços de Mídia, o SDK do .NET dos Serviços de Mídia, e outros assemblies dependentes são adicionados.

4. Para promover um ambiente de desenvolvimento mais limpo, considere a ativação da restauração de pacote do NuGet. Para obter mais informações, consulte [Restauração do pacote NuGet"](http://docs.nuget.org/consume/package-restore).

3. Adicione uma referência ao assembly do **System.Configuration**. Este assembly contém a classe System.Configuration.**ConfigurationManager** que é utilizada para acessar arquivos de configuração, como App.config.

Para adicionar referências usando a caixa de diálogo Gerenciar Referências, proceda da maneira a seguir:

1. No Gerenciador de Solução, clique no nome do projeto. Em seguida, selecione Adicionar e Referências.

Aparece a caixa de diálogo Gerenciar referências.

2. Em assemblies do .NET Framework, localize e selecione o assembly System. Configuration.
3. Pressione OK.


4. Abra o arquivo App.config (adicione o arquivo ao seu projeto se ele não foi adicionado por padrão) e adicione uma seção *appSettings* ao arquivo. Defina os valores para o nome e chave de conta de seus Serviços de Mídia do Azure, conforme mostrado no exemplo a seguir.

Para obter as informações de **nome da conta** e **chave de conta**, abra o **Portal Clássico do Azure**, selecione sua conta de serviços de mídia e clique no botão **GERENCIAR CHAVES**.


<configuration> ... <appSettings> <add key="MediaServicesAccountName" value="Media-Services-Account-Name" /> <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" /> </appSettings>
	  
	</configuration>

5. Substitua a instruções using existentes no início do arquivo Program.cs pelo código a seguir.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;

Neste ponto, você está pronto para começar a desenvolver um aplicativo de Serviços de Mídia.



##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_1203_2015-->