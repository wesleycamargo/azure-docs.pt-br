<properties 
	pageTitle="Use Serviços Móveis para carregar imagens no armazenamento de blob (Windows Phone) | Serviços Móveis" 
	description="Aprenda a usar os Serviços Móveis para carregar imagens no Armazenamento de blobs do Azure." 
	documentationCenter="windows" 
	authors="ggailey777" 
	writer="glenga" 
	services="mobile-services,storage" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="glenga"/>

# Fazer upload de imagens no Armazenamento do Azure usando Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para habilitar seu aplicativo Windows Phone 8 ou Windows Phone 8.1 Silverlight para carregar e armazenar as imagens geradas pelo usuário no Armazenamento do Azure. Os Serviços Móveis usam um banco de dados SQL para armazenar dados. No entanto, os dados de BLOB (objeto binário grande) são armazenados com mais eficiência no serviço de armazenamento de Blob do Azure.

Você não pode distribuir as credenciais necessárias de maneira segura usando o aplicativo cliente para carregar dados com segurança para o serviço de Armazenamento de Blob. Nesse caso, você deve armazenar essas credenciais em seu serviço móvel e usá-las para gerar uma SAS (assinatura de acesso compartilhado) usada para carregar uma nova imagem. A SAS, uma credencial com expiração curta – nesse caso, 5 minutos – será retornada com segurança pelos Serviços Móveis para o aplicativo cliente. O aplicativo usará, então, essa credencial temporária para carregar a imagem. Neste exemplo, os downloads do serviço Blob são públicos.

Neste tutorial, você adicionará funcionalidade ao [projeto de aplicativo de exemplo GetStartedWithData](mobile-services-dotnet-backend-windows-phone-get-started-data.md) para tirar fotos e carregar as imagens no Azure usando uma SAS gerada pelos Serviços Móveis. Este tutorial o orientará através das etapas básicas a seguir para atualizar o aplicativo TodoList simples a fim de carregar imagens no serviço de Armazenamento de Blob:

1. [Instalar a biblioteca do Cliente de Armazenamento]
2. [Atualizar o aplicativo cliente para capturar imagens]
3. [Instalar o cliente de armazenamento do projeto de serviço móvel]
4. [Atualizar a definição de TodoItem no modelo de dados]
5. [Atualizar o controlador de tabela para gerar uma SAS]
6. [Carregar imagens para testar o aplicativo]

Este tutorial exige o seguinte:

+ Microsoft Visual Studio 2013 ou uma versão posterior
+ [SDK do Windows Phone 8.0] ou superior
+ Gerenciador de Pacotes Nuget instalado para o Microsoft Visual Studio.
+ [Conta de Armazenamento do Azure][How To Create a Storage Account]
+ Conclua o tutorial [Adicionar Serviços Móveis a um aplicativo existente ](mobile-services-dotnet-backend-windows-phone-get-started-data.md)  

>[AZURE.NOTE]Este tutorial dá suporte a aplicativos Windows Phone 8 e Windows Phone 8.1 Silverlight. Ele não oferece suporte a aplicativos Windows Phone Store 8.1 ou aplicativos universais do Windows 8.1.

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Instalar o cliente de Armazenamento para aplicativos da Windows Store

Para poder usar um SAS para fazer upload de imagens de seu aplicativo no armazenamento de Blob, você deve primeiro incluir o pacote NuGet que instala a biblioteca do cliente de Armazenamento para aplicativos da Windows Store.

1. Em **Gerenciador de Soluções** no Visual Studio, clique com o botão direito no projeto do aplicativo do cliente e selecione **Gerenciar Pacotes NuGet**.

2. No painel esquerdo, selecione a categoria **Online**, selecione **Incluir Pré-liberação**, pesquise **WindowsAzure.Storage-Preview**, clique em **Instalar** no pacote **Armazenamento do Azure**, em seguida, aceite os acordos de licença.

  	![][2]

  	Isso adicionará a biblioteca de cliente para os serviços de armazenamento do Azure ao projeto.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]
 
<!-- Anchors. -->
[Instalar a biblioteca do Cliente de Armazenamento]: #install-storage-client
[Atualizar o aplicativo cliente para capturar imagens]: #add-select-images
[Instalar o cliente de armazenamento do projeto de serviço móvel]: #storage-client-server
[Atualizar a definição de TodoItem no modelo de dados]: #update-data-model
[Atualizar o controlador de tabela para gerar uma SAS]: #update-scripts
[Carregar imagens para testar o aplicativo]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Schedule backend jobs in Mobile Services]: mobile-services-dotnet-backend-schedule-recurring-tasks.md
[Get started with Mobile Services]: mobile-services-windows-phone-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[SDK do Windows Phone 8.0]: http://www.microsoft.com/download/details.aspx?id=35471



<!--HONumber=54-->