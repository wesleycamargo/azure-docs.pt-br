<properties 
	pageTitle="Use Serviços Móveis para carregar imagens no armazenamento de blob (Windows Phone) | Serviços Móveis" 
	description="Aprenda a usar os serviços móveis para carregar imagens para armazenamento de Blob do Azure." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	writer="wesmc" 
	services="mobile-services, storage" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/06/2014" 
	ms.author="wesmc"/>

# Fazer upload de imagens no Armazenamento do Azure usando Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para habilitar seu aplicativo para carregar e armazenar as imagens geradas pelo usuário no Armazenamento do Azure. Os Serviços Móveis usam um banco de dados SQL para armazenar dados. No entanto, os dados de BLOB (objeto binário grande) são armazenados com mais eficiência no serviço de armazenamento de Blob do Azure. 

Você não pode distribuir as credenciais necessárias de maneira segura usando o aplicativo cliente para carregar dados com segurança para o serviço de Armazenamento de Blob. Nesse caso, você deve armazenar essas credenciais em seu serviço móvel e usá-las para gerar uma SAS (assinatura de acesso compartilhado) usada para carregar uma nova imagem. A SAS, uma credencial com expiração curta&mdash;nesse caso, 5 minutos, será retornada com segurança pelos Serviços Móveis para o aplicativo cliente. O aplicativo usará, então, essa credencial temporária para carregar a imagem. Neste exemplo, os downloads do serviço Blob são públicos.

Neste tutorial, você adicionará funcionalidade ao [projeto de aplicativo de exemplo GetStartedWithData](/en-us/documentation/articles/mobile-services-windows-phone-get-started-data/) para tirar fotos e carregar as imagens no Azure usando uma SAS gerada pelos Serviços Móveis. Este tutorial o orientará através das etapas básicas a seguir para atualizar o aplicativo TodoList simples a fim de carregar imagens no serviço de Armazenamento de Blob:

1. [Instalar a biblioteca do Cliente de Armazenamento]
2. [Atualizar o script de inserção para gerar uma SAS]
3. [Atualizar o aplicativo cliente para capturar imagens]
4. [Carregar imagens para testar o aplicativo]

Este tutorial exige o seguinte:

+ Microsoft Visual Studio 2012 Express para Windows 8 ou uma versão posterior
+ [Windows Phone SDK 8.0] ou superior
+ Gerenciador de Pacotes Nuget instalado para o Microsoft Visual Studio.
+ [Conta de Armazenamento do Azure][Como criar uma conta de armazenamento]
+ Conclua o tutorial [Adicionar os Serviços Móveis a um aplicativo existente](/en-us/documentation/articles/mobile-services-windows-phone-get-started-data/)  


##<a name="install-storage-client"></a>Instalar o cliente de armazenamento para aplicativos do Windows Phone

Para poder usar uma SAS para carregar imagens para o armazenamento de Blob, você deve primeiro adicionar o pacote NuGet que instala a biblioteca do cliente de armazenamento para aplicativos do Windows Phone.

1. No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no nome do projeto e selecione **Gerenciar Pacotes NuGet**.

2. No painel esquerdo, selecione a categoria **Online**, selecione **Incluir Pré-lançamento**, pesquise por **WindowsAzure.storage-preview**, clique em **Instalar** no pacote de **Armazenamento do Azure** e aceite os contratos de licença. 

  	![][2]

  	Isso adicionará a biblioteca de cliente para os serviços de armazenamento do Azure ao projeto.

Em seguida, você atualizará o aplicativo de início rápido para capturar e carregar imagens.

##<a name="update-scripts"></a>Atualizar o script de inserção registrado no Portal de Gerenciamento


[AZURE.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

>[AZURE.NOTE]Para adicionar novas propriedades ao objeto TodoItem, você deve ter o Esquema Dinâmico habilitado no seu serviço móvel. Quando o Esquema Dinâmico estiver habilitado, novas colunas serão automaticamente adicionadas à tabela TodoItem que mapeará para essas novas propriedades.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]


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


[2]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


[5]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png
[6]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-wp8.png
[7]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-wp8.png
[8]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-save-wp8.png
[9]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-final-wp8.png

[11]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png

<!-- URLs. -->
[ Enviar e-mails dos Serviços Móveis com o SendGrid]: /en-us/develop/mobile/tutorials/send-email-with-sendgrid/
[Agendar trabalhos de back-end nos serviços móveis]: /en-us/develop/mobile/tutorials/schedule-backend-tasks/
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-windows-phone-get-started

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Como criar uma conta de armazenamento]: /en-us/manage/services/storage/how-to-create-a-storage-account
[Biblioteca do cliente de Armazenamento do Azure para a Loja de aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/en-us/download/details.aspx?id=35471


\n<!--HONumber=42-->
