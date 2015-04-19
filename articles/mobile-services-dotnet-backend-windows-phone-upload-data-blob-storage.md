<properties 
	pageTitle="Use Serviços Móveis para carregar imagens no armazenamento de blob (Windows Phone) | Serviços Móveis" 
	description="Aprenda a usar os serviços móveis para carregar imagens para armazenamento de Blob do Azure." 
	documentationCenter="windows" 
	authors="ggailey777" 
	writer="glenga" 
	services="mobile-services" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="glenga"/>

# Carregar imagens no Armazenamento do Azure usando Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para habilitar seu aplicativo para carregar e armazenar as imagens geradas pelo usuário no Armazenamento do Azure. Os Serviços Móveis usam um banco de dados SQL para armazenar dados. No entanto, os dados de BLOB (objeto binário grande) são armazenados com mais eficiência no serviço de armazenamento de Blob do Azure. 

Você não pode distribuir as credenciais necessárias de maneira segura usando o aplicativo cliente para carregar dados com segurança para o serviço de Armazenamento de Blob. Nesse caso, você deve armazenar essas credenciais em seu serviço móvel e usá-las para gerar uma SAS (assinatura de acesso compartilhado) usada para carregar uma nova imagem. A SAS, uma credencial com expiração curta&mdash;nesse caso, 5 minutos será retornado com segurança pelos Serviços Móveis para o aplicativo cliente. O aplicativo usará, então, essa credencial temporária para carregar a imagem. Neste exemplo, os downloads do serviço Blob são públicos.

Neste tutorial, você adicionará funcionalidade ao [projeto de aplicativo de exemplo GetStartedWithData](mobile-services-dotnet-backend-windows-phone-get-started-data.md) para tirar fotos e carregar as imagens no Azure usando uma SAS gerada pelos Serviços Móveis. Este tutorial o orientará através das etapas básicas a seguir para atualizar o aplicativo TodoList simples a fim de carregar imagens no serviço de Armazenamento de Blob:

1. [Instalar a biblioteca do Cliente de Armazenamento]
2. [Atualizar o aplicativo cliente para capturar imagens]
3. [Instalar o cliente de armazenamento no projeto de serviço móvel]
4. [Atualizar a definição de TodoItem no modelo de dados]
5. [Atualizar o controlador de tabela para gerar uma SAS]
6. [Carregar imagens para testar o aplicativo]

Este tutorial exige o seguinte:

+ Microsoft Visual Studio 2013 ou uma versão posterior
+ [Windows Phone SDK 8.0] ou superior
+ Gerenciador de Pacotes Nuget instalado para o Microsoft Visual Studio.
+ [Conta de Armazenamento do Azure][Como criar uma conta de armazenamento]
+ Conclua o tutorial [Adicionar os Serviços Móveis a um aplicativo existente](mobile-services-dotnet-backend-windows-phone-get-started-data.md)  

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Instalar o cliente de Armazenamento para aplicativos da Windows Store

Para usar um SAS para carregar imagens de seu aplicativo no armazenamento de Blob, você deve primeiro incluir o pacote NuGet que instala a biblioteca do cliente de Armazenamento para aplicativos da Windows Store.

1. No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo cliente e selecione **Gerenciar Pacotes NuGet**.

2. No painel esquerdo, selecione a categoria **Online**, selecione **Incluir Pré-lançamento**, pesquise por **WindowsAzure.storage-preview**, clique em **Instalar** no pacote de **Armazenamento do Azure** e aceite os contratos de licença. 

  	![][2]

  	Isso adicionará a biblioteca de cliente para os serviços de armazenamento do Azure ao projeto.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]
 
<!-- Anchors. -->
[Instalar a biblioteca do Cliente de Armazenamento]: #install-storage-client
[Atualizar o aplicativo cliente para capturar imagens]: #add-select-images
[Instalar o cliente de armazenamento no projeto de serviço móvel]: #storage-client-server
[Atualizar a definição de TodoItem no modelo de dados]: #update-data-model
[Atualizar o controlador de tabela para gerar uma SAS]: #update-scripts
[Carregar imagens para testar o aplicativo]: #test
[Próximas etapas]:#next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[ Enviar e-mails dos Serviços Móveis com o SendGrid]: /pt-br/documentation/articles/store-sendgrid-mobile-services-send-email-scripts/
[Agendar trabalhos de back-end nos serviços móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Como criar uma conta de armazenamento]: /pt-br/documentation/articles/storage-create-storage-account/
[Biblioteca do cliente de Armazenamento do Azure para a Loja de aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/pt-br/download/details.aspx?id=35471



\<!--HONumber=42-->
