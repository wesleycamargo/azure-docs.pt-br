<properties 
	pageTitle="Use Serviços Móveis para carregar imagens para o armazenamento de blob (Windows Store) | Serviços Móveis" 
	description="Aprenda a usar os serviços móveis para carregar imagens no armazenamento de Blob do Azure e acessar as imagens do seu aplicativo da Windows Store." 
	services="mobile-services, storage" 
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
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Fazer upload de imagens no Armazenamento do Azure usando Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para habilitar seu aplicativo para carregar e armazenar as imagens geradas pelo usuário no Armazenamento do Azure. Os Serviços Móveis usam um banco de dados SQL para armazenar dados. No entanto, os dados de BLOB (objeto binário grande) são armazenados com mais eficiência no serviço de armazenamento de Blob do Azure. 

Você não pode distribuir as credenciais necessárias de maneira segura usando o aplicativo cliente para carregar dados com segurança para o serviço de Armazenamento de Blob. Em vez disso, você deve armazenar essas credenciais em seu serviço móvel e usá-las para gerar uma SAS (assinatura de acesso compartilhado) usada para carregar uma nova imagem. A SAS, uma credencial com expiração curta nesse caso, 5 minutos será retornada com segurança pelos Serviços Móveis para o aplicativo cliente. O aplicativo usará, então, essa credencial temporária para carregar a imagem. Neste exemplo, os downloads do serviço Blob são públicos.

Neste tutorial, você adicionará funcionalidade ao aplicativo de início rápido de Serviços Móveis para tirar fotos e carregar as imagens no Azure usando uma SAS gerada pelos Serviços Móveis. Este tutorial o orientará através das etapas básicas a seguir para atualizar o início rápido de Serviços Móveis para carregar imagens para o serviço de Armazenamento de Blob:

1. [Instalar a biblioteca do Cliente de Armazenamento]
2. [Atualizar o script de inserção para gerar uma SAS]
3. [Atualizar o aplicativo cliente para capturar imagens]
4. [Carregar imagens para testar o aplicativo]

Este tutorial exige o seguinte:

+ Microsoft Visual Studio 2012 Express para Windows 8 ou uma versão posterior
+ [Conta de Armazenamento do Azure][Como criar uma conta de armazenamento]
+ Uma câmera ou outro dispositivo de captura de imagem conectado ao seu computador.

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. 

##<a name="install-storage-client"></a>Instalar o cliente de armazenamento para aplicativos da Windows Store

Para poder usar uma SAS para carregar imagens para o armazenamento de Blob, você deve primeiro adicionar o pacote NuGet que instala a biblioteca do cliente de armazenamento para aplicativos da Windows Store.

1. No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no nome do projeto e selecione **Gerenciar Pacotes NuGet**.

2. No painel esquerdo, selecione a categoria **Online**, procure `WindowsAzure.Storage`, clique em **Instalar** no pacote **Armazenamento do Azure** e aceite os contratos de licença. 

  	![][2]

  	Isso adicionará a biblioteca de cliente para os serviços de armazenamento do Azure ao projeto.

Em seguida, você atualizará o aplicativo de início rápido para capturar e carregar imagens.

##<a name="update-scripts"></a>Atualizar o script de inserção registrado no Portal de Gerenciamento

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você conseguiu carregar as imagens com segurança, integrando seu serviço móvel com o serviço de Blob, confira alguns dos outros tópicos de serviço de back-end e integração:

+ [ Enviar e-mails dos Serviços Móveis com o SendGrid]
 
  Saiba como adicionar funcionalidade de email ao Serviço Móvel usando o serviço de email SendGrid. Este tópico demonstra como adicionar scripts do lado do servidor para enviar email usando o SendGrid.

+ [Agendar trabalhos de back-end nos serviços móveis]

  Saiba como usar a funcionalidade de agendador de trabalhos dos Serviços Móveis para definir o código de script de servidor executado em um agendamento definido por você.

+ [Referência de script de servidor dos Serviços Móveis]

  Tópicos de referência para o uso de scripts de servidor para executar tarefas no lado do servidor e integração com outros componentes do Azure e recursos externos.
 
+ [Referência conceitual do tutorial do .NET de Serviços Móveis]

  Saiba mais sobre como usar os Serviços Móveis com o .NET
  
 
<!-- Anchors. -->
[Instalar a biblioteca do Cliente de Armazenamento]: #install-storage-client
[Atualizar o aplicativo cliente para capturar imagens]: #add-select-images
[Atualizar o script de inserção para gerar uma SAS]: #update-scripts
[Carregar imagens para testar o aplicativo]: #test
[Próximas etapas]:#next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[ Enviar e-mails dos Serviços Móveis com o SendGrid]: /en-us/develop/mobile/tutorials/send-email-with-sendgrid/
[Agendar trabalhos de back-end nos serviços móveis]: /en-us/documentation/articles/mobile-services-schedule-recurring-tasks
[Enviar notificações por push para aplicativos da Windows Store usando o Barramento de Serviço de um back-end do .NET]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-windows-store-get-started

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Como criar uma conta de armazenamento]: /en-us/manage/services/storage/how-to-create-a-storage-account
[Biblioteca do cliente de Armazenamento do Azure para a Loja de aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
[Configurações do aplicativo]: http://msdn.microsoft.com/en-us/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
\n<!--HONumber=42-->
