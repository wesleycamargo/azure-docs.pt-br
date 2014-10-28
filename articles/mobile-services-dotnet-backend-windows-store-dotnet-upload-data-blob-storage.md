<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Store) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Fazer upload de imagens no Armazenamento do Azure usando Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title="C# da Windows Store" class="current">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage" title="Windows Phone">Windows Phone</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title="Back-end do .NET" class="current">Back-end do .NET</a> |  <a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage"  title="Back-end do JavaScript">Back-end do JavaScript</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure para habilitar seu aplicativo para carregar e armazenar as imagens geradas pelo usuário no Armazenamento do Azure. Os Serviços Móveis usam um banco de dados SQL para armazenar dados. No entanto, os dados de BLOB (objeto binário grande) são armazenados com mais eficiência no serviço de armazenamento de Blob do Azure.

Você não pode distribuir as credenciais necessárias de maneira segura usando o aplicativo cliente para carregar dados com segurança para o serviço de Armazenamento de Blob. Nesse caso, você deve armazenar essas credenciais em seu serviço móvel e usá-las para gerar uma SAS (assinatura de acesso compartilhado) usada para carregar uma nova imagem. A SAS, uma credencial com expiração curta – nesse caso, 5 minutos será retornado com segurança pelos Serviços Móveis para o aplicativo cliente. O aplicativo usará, então, essa credencial temporária para carregar a imagem. Neste exemplo, os downloads do serviço Blob são públicos.

Neste tutorial, você adicionará funcionalidade ao aplicativo quickstart de Serviços Móveis para tirar fotos e carregar as imagens no Azure usando uma SAS gerada pelos Serviços Móveis. Este tutorial o orientará através das etapas básicas a seguir para atualizar o quickstart de Serviços Móveis para carregar imagens para o serviço de Armazenamento de Blob:

1.  [Instalar a biblioteca do Cliente de Armazenamento][Instalar a biblioteca do Cliente de Armazenamento]
2.  [Atualizar o aplicativo cliente para capturar imagens][Atualizar o aplicativo cliente para capturar imagens]
3.  [Instalar o cliente de armazenamento no projeto de serviço móvel][Instalar o cliente de armazenamento no projeto de serviço móvel]
4.  [Atualizar a definição de TodoItem no modelo de dados][Atualizar a definição de TodoItem no modelo de dados]
5.  [Atualizar o controlador de tabela para gerar uma SAS][Atualizar o controlador de tabela para gerar uma SAS]
6.  [Carregar imagens para testar o aplicativo][Carregar imagens para testar o aplicativo]

Este tutorial exige o seguinte:

-   Microsoft Visual Studio 2013 ou uma versão posterior.
-   Gerenciador de Pacotes Nuget instalado para o Microsoft Visual Studio.
-   [Conta de Armazenamento do Azure][Conta de Armazenamento do Azure]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage][mobile-services-dotnet-backend-configure-blob-storage]]

## <a name="install-storage-client"></a>Instalar o cliente de Armazenamento para aplicativos da Windows Store

Para poder usar um SAS para fazer upload de imagens de seu aplicativo no armazenamento de Blob, você deve primeiro incluir o pacote NuGet que instala a biblioteca do cliente de Armazenamento para aplicativos da Windows Store.

1.  Em **Gerenciador de Soluções** no Visual Studio, clique com o botão direito no projeto do aplicativo do cliente e selecione **Gerenciar Pacotes NuGet**.

2.  No painel esquerdo, selecione a categoria **Online**, selecione **Incluir Pré-liberação**, pesquise **WindowsAzure.Storage-Preview**, clique em **Instalar** no pacote **Armazenamento do Azure**, em seguida, aceite os acordos de licença.

    ![][]

    Isso adicionará a biblioteca de cliente para os serviços de armazenamento do Azure ao projeto.

Em seguida, você atualizará o aplicativo quickstart para capturar e carregar imagens.

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage][mobile-services-windows-store-dotnet-upload-to-blob-storage]]

<!-- Anchors. --> 

<!-- Images. --> 

<!-- URLs. -->

  [C# da Windows Store]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage "C# da Windows Store"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage "Windows Phone"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage "Back-end do JavaScript"
  [Instalar a biblioteca do Cliente de Armazenamento]: #install-storage-client
  [Atualizar o aplicativo cliente para capturar imagens]: #add-select-images
  [Instalar o cliente de armazenamento no projeto de serviço móvel]: #storage-client-server
  [Atualizar a definição de TodoItem no modelo de dados]: #update-data-model
  [Atualizar o controlador de tabela para gerar uma SAS]: #update-scripts
  [Carregar imagens para testar o aplicativo]: #test
  [Conta de Armazenamento do Azure]: /pt-br/documentation/articles/storage-create-storage-account/
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started
  [mobile-services-dotnet-backend-configure-blob-storage]: ../includes/mobile-services-dotnet-backend-configure-blob-storage.md
  []: ./media/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png
  [mobile-services-windows-store-dotnet-upload-to-blob-storage]: ../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md
