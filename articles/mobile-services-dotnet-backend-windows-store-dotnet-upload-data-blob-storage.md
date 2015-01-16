<properties pageTitle="Use Serviços Móveis para carregar imagens para o armazenamento de blob (Windows Store) | Serviços Móveis" metaKeywords="" description="Aprenda a usar os serviços móveis para carregar imagens para armazenamento de Blob do Azure." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Fazer upload de imagens no Armazenamento do Azure usando Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para habilitar seu aplicativo para carregar e armazenar as imagens geradas pelo usuário no Armazenamento do Azure. Os Serviços Móveis usam um banco de dados SQL para armazenar dados. No entanto, os dados de BLOB (objeto binário grande) são armazenados com mais eficiência no serviço de armazenamento de Blob do Azure. 

Você não pode distribuir as credenciais necessárias de maneira segura usando o aplicativo cliente para carregar dados com segurança no serviço de Armazenamento de Blob. Nesse caso, você deve armazenar essas credenciais em seu serviço móvel e usá-las para gerar uma SAS (assinatura de acesso compartilhado) usada para carregar uma nova imagem. A SAS, uma credencial com expiração curta - nesse caso, 5 minutos será retornado com segurança pelos Serviços Móveis para o aplicativo cliente. O aplicativo usará, então, essa credencial temporária para carregar a imagem. Neste exemplo, os downloads do serviço Blob são públicos.

Neste tutorial, você adicionará funcionalidade ao aplicativo de início rápido de Serviços Móveis para tirar fotos e carregar as imagens no Azure usando uma SAS gerada pelos Serviços Móveis. Este tutorial o orientará através das etapas básicas a seguir para atualizar o início rápido de Serviços Móveis para carregar imagens no serviço de Armazenamento de Blob:

1. [Instalar a biblioteca do Cliente de Armazenamento]
2. [Atualizar o aplicativo cliente para capturar imagens]
3. [Instalar o cliente de armazenamento no projeto de serviço móvel]
4. [Atualizar a definição de TodoItem no modelo de dados]
5. [Atualizar o controlador de tabela para gerar uma SAS]
6. [Carregar imagens para testar o aplicativo]

Este tutorial exige o seguinte:

+ Microsoft Visual Studio 2013 ou uma versão posterior.
+ Gerenciador de Pacotes Nuget instalado para o Microsoft Visual Studio.
+ [Conta de Armazenamento do Azure][como criar uma conta de armazenamento]

Este tutorial baseia-se no início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. 

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Instalar o cliente de Armazenamento para aplicativos da Windows Store

Para poder usar um SAS para fazer upload de imagens de seu aplicativo no armazenamento de Blob, você deve primeiro incluir o pacote NuGet que instala a biblioteca do cliente de Armazenamento para aplicativos da Windows Store.

1. No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo cliente e selecione **Gerenciar Pacotes NuGet**.

2. No painel esquerdo, selecione a categoria **Online**, selecione **Incluir Pré-lançamento**, pesquise por **WindowsAzure.Storage-Preview**, clique em **Instalar** no pacote **Armazenamento do Azure** e aceite os contratos de licença. 

  	![][2]

  	Isso adicionará a biblioteca de cliente para os serviços de armazenamento do Azure ao projeto.

Em seguida, você atualizará o aplicativo de início rápido para capturar e carregar imagens.

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

 
<!-- Anchors. -->
[Instalar a biblioteca do Cliente de Armazenamento]: #install-storage-client
[Atualizar o aplicativo cliente para capturar imagens]: #add-select-images
[Instalar o cliente de armazenamento no projeto de serviço móvel]: #storage-client-server
[Atualizar a definição de TodoItem no modelo de dados]: #update-data-model
[Atualizar o controlador de tabela para gerar uma SAS]: #update-scripts
[Carregar imagens para testar o aplicativo]: #test
[Próximas etapas]:#next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Enviar email dos Serviços Móveis com SendGrid]: /pt-br/documentation/articles/store-sendgrid-mobile-services-send-email-scripts/
[Agendar trabalhos de back-end nos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Como criar uma conta de armazenamento]: /pt-br/documentation/articles/storage-create-storage-account/
[Biblioteca do cliente de Armazenamento do Azure para aplicativos da Store]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[SDK do Windows Phone 8.0]: http://www.microsoft.com/pt-br/download/details.aspx?id=35471


