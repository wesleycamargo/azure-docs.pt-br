<properties 
	pageTitle="Carregar imagens no armazenamento de blob do Azure de um aplicativo universal do Windows | Microsoft Azure" 
	description="Saiba como usar um serviço móvel de back-end do JavaScript para carregar imagens no armazenamento de blob do Azure e acessar imagens em seu aplicativo Windows universal." 
	services="mobile-services,storage" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="glenga"/>

# Fazer upload de imagens no armazenamento de blob do Azure usando Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##Visão geral 

Este tópico mostra como usar os Serviços Móveis do Azure para habilitar seu aplicativo para carregar e armazenar as imagens geradas pelo usuário no Armazenamento do Azure. Os Serviços Móveis usam um banco de dados SQL para armazenar dados. No entanto, os dados de BLOB (objeto binário grande) são armazenados com mais eficiência no serviço de armazenamento de Blob do Azure.

Você não pode distribuir as credenciais necessárias de maneira segura usando o aplicativo cliente para carregar dados com segurança para o serviço de Armazenamento de Blob. Em vez disso, você deve armazenar essas credenciais em seu serviço móvel e usá-las para gerar uma SAS (assinatura de acesso compartilhado) usada para carregar uma nova imagem. A SAS, uma credencial com expiração curta – nesse caso, 5 minutos – será retornada com segurança pelos Serviços Móveis para o aplicativo cliente. O aplicativo usará, então, essa credencial temporária para carregar a imagem. Neste exemplo, os downloads do serviço Blob são públicos.

Neste tutorial, você adicionará funcionalidade ao aplicativo quickstart de Serviços Móveis para tirar fotos e carregar as imagens no Azure usando uma SAS gerada pelos Serviços Móveis.

##Pré-requisitos

Este tutorial exige o seguinte:

+ Microsoft Visual Studio 2013 Atualização 3 ou uma versão posterior
+ [Conta de Armazenamento do Azure](../storage-create-storage-account.md)
+ Uma câmera ou outro dispositivo de captura de imagem conectado ao seu computador.

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis].

##Atualizar o script de inserção registrados no Portal de Gerenciamento

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-upload-to-blob-storage](../../includes/mobile-services-windows-universal-dotnet-upload-to-blob-storage.md)]

##Próximas etapas

Agora que você conseguiu carregar as imagens com segurança, integrando seu serviço móvel com o serviço de Blob, confira alguns dos outros tópicos de serviço de back-end e integração:

+ [Agendar trabalhos de back-end nos Serviços Móveis]

  	Saiba como usar a funcionalidade de agendador de trabalhos dos Serviços Móveis para definir o código de script de servidor executado em um agendamento definido por você.

+ [Referência de script de servidor dos Serviços Móveis]

    Tópicos de referência para o uso de scripts de servidor para executar tarefas no lado do servidor e integração com outros componentes do Azure e recursos externos.
 
+ [Referência conceitual do tutorial do .NET de Serviços Móveis]

    Saiba mais sobre como usar os Serviços Móveis com o .NET
  
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Agendar trabalhos de back-end nos Serviços Móveis]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Referência de script de servidor dos Serviços Móveis]: mobile-services-how-to-use-server-scripts.md
[Introdução aos Serviços Móveis]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Referência conceitual do tutorial do .NET de Serviços Móveis]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
 

<!---HONumber=August15_HO7-->