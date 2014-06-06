<properties pageTitle="Usar os Serviços Móveis para carregar imagens para o armazenamento de blob (Windows Phone) | Serviços Móveis" metaKeywords="" description="Saiba como usar os Serviços Móveis para carregar imagens para o Armazenamento de Blob do Azure." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Carregar imagens no Armazenamento do Azure usando os Serviços Móveis" authors="glenga" writer="glenga" />

# Carregar imagens no Armazenamento do Azure usando Serviços Móveis
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage" title="Windows Phone" class="current">Windows Phone</a>
</div>

Este tópico mostra como usar os Serviços Móveis do Azure para habilitar seu aplicativo para carregar e armazenar as imagens geradas pelo usuário no Armazenamento do Azure. Os Serviços Móveis usam um banco de dados SQL para armazenar dados. No entanto, os dados de BLOB (objeto binário grande) são armazenados com mais eficiência no serviço de armazenamento de Blob do Azure. 

Você não pode distribuir as credenciais necessárias de maneira segura usando o aplicativo cliente para carregar dados com segurança para o serviço de Armazenamento de Blob. Nesse caso, você deve armazenar essas credenciais em seu serviço móvel e usá-las para gerar uma SAS (assinatura de acesso compartilhado) usada para carregar uma nova imagem. A SAS, uma credencial com expiração curta – nesse caso, 5 minutos será retornado com segurança pelos Serviços Móveis para o aplicativo cliente. O aplicativo usará, então, essa credencial temporária para carregar a imagem. Neste exemplo, os downloads do serviço Blob são públicos.

Neste tutorial, você adicionará funcionalidade ao aplicativo quickstart de Serviços Móveis para tirar fotos e carregar as imagens no Azure usando uma SAS gerada pelos Serviços Móveis. Este tutorial o orientará através das etapas básicas a seguir para atualizar o quickstart de Serviços Móveis para carregar imagens para o serviço de Armazenamento de Blob:

1. [Instalar a biblioteca do Cliente de Armazenamento]
2. [Atualizar o script de inserção para gerar uma SAS]
3. [Atualizar o aplicativo cliente para capturar imagens]
4. [Carregar imagens para testar o aplicativo]

Este tutorial exige o seguinte:

+ Microsoft Visual Studio 2012 Express para Windows 8 ou uma versão posterior
+ [SDK do Windows Phone 8.0] ou superior
+ Gerenciador de Pacotes NuGet instalado para Microsoft Visual Studio.
+ [Conta de Armazenamento do Azure][How To Create a Storage Account]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. 

##<a name="install-storage-client"></a>Instalar o cliente de Armazenamento para aplicativos da Windows Store

Para poder usar uma SAS para carregar imagens para o armazenamento de Blob, você deve primeiro adicionar o pacote NuGet que instala a biblioteca do cliente de armazenamento para aplicativos da Windows Store.

1. No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no nome do projeto e selecione **Gerenciar Pacotes NuGet**.

2. No painel esquerdo, selecione a categoria **Online**, selecione **Incluir Pré-lançamento**, pesquise por **WindowsAzure..Storage-Preview**, clique em **Instalar** no pacote de **Armazenamento do Azure** e aceite os contratos de licença. 

  	![][2]

  	Isso adicionará a biblioteca de cliente para os serviços de armazenamento do Azure ao projeto.

Em seguida, você atualizará o aplicativo quickstart para capturar e carregar imagens.

##<a name="update-scripts"></a>Atualizar o script de inserção registrado no Portal de Gerenciamento

[WACOM.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[WACOM.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]


## <a name="next-steps"> </a>Próximas etapas

Agora que você conseguiu carregar as imagens com segurança, integrando seu serviço móvel com o serviço Blob, confira alguns dos outros tópicos de serviço de back-end e integração:

+ [Enviar email dos Serviços Móveis com SendGrid]
 
  Saiba como adicionar funcionalidade de email ao Serviço Móvel usando o serviço de email SendGrid. Este tópico demonstra como adicionar scripts do lado do servidor para enviar email usando o SendGrid.

+ [Agendar trabalhos de back-end nos Serviços Móveis]

  Saiba como usar a funcionalidade de agendador de trabalhos dos Serviços Móveis para definir o código de script de servidor executado em um agendamento definido por você.

+ [Referência de script de servidor dos Serviços Móveis]

  Tópicos de referência para o uso de scripts de servidor para executar tarefas no lado do servidor e integração com outros componentes do Azure e recursos externos.
 
+ [Referência conceitual do tutorial do .NET dos Serviços Móveis]

  Saiba mais sobre como usar Serviços Móveis com o .NET
  
 
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
[Enviar email dos Serviços Móveis com SendGrid]: /pt-br/develop/mobile/tutorials/send-email-with-sendgrid/
[Agendar trabalhos de back-end nos Serviços Móveis]: /pt-br/develop/mobile/tutorials/schedule-backend-tasks/
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Como criar uma conta de armazenamento]: /pt-br/manage/services/storage/how-to-create-a-storage-account
[Biblioteca do cliente de Armazenamento do Azure para aplicativos da Store]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Referência conceitual do tutorial do .NET dos Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library
[SDK do Windows Phone 8.0]: http://www.microsoft.com/pt-br/download/details.aspx?id=35471



