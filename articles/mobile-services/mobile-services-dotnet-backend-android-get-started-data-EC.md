<properties 
	pageTitle="Introdução aos dados (Android) | Microsoft Azure" 
	description="Saiba como começar a usar os serviços móveis para utilizar dados em seu aplicativo Android." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="ricksal"/>

# Adicionar Serviços Móveis a um aplicativo existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data-EC.md)]

##Visão geral

Este tópico mostra como usar os Serviços Móveis do Azure como uma fonte de dados de back-end para um aplicativo Android. Neste tutorial, você criará um novo serviço móvel, baixará um projeto do Eclipse Android para um aplicativo que armazena dados na memória, integrará o serviço móvel ao aplicativo e exibirá as alterações de dados feitas durante a execução do aplicativo.

O serviço móvel que você criará neste tutorial dará suporte ao tempo de execução do .NET no Serviço Móvel. Isso permitirá que você use linguagens .NET e o Visual Studio para a lógica dos negócios do lado do servidor no serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript](mobile-services-android-get-started-data-EC.md) neste tópico.

Para concluir este tutorial, você precisará do seguinte:

+ <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a> (Atualização 3, ou uma versão posterior). 

+ Uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-android-get-started-data-EC%2F).

##<a name="create-service"></a>Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]


##<a name="download-the-service"></a>Baixar o serviço em seu computador local

[AZURE.INCLUDE [mobile-services-download-service-locally](../../includes/mobile-services-download-service-locally.md)]

##<a name="test-the-service"></a>Testar o serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-the-service"></a>Publicar o serviço móvel no Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="download-app"></a>Baixar o projeto GetStartedWithData

###Obter o código de amostra

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/download-android-sample-code-EC.md)]

###Verificar Versão de SDK do Android

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version-EC.md)]


###Inspecionar e executar o código de amostra

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code-EC.md)]

##<a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data-EC.md)]

##<a name="test-app"></a>Testar o aplicativo no serviço móvel publicado

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, você pode testá-lo nos Serviços Móveis usando o emulador do Android ou um telefone Android.

1. No menu **Executar**, clique em **Executar** para iniciar o projeto.

	Isso executa seu aplicativo criado com o SDK do Android que usa a biblioteca cliente para enviar uma consulta que retorna os itens de seu serviço móvel.

5. Como antes, digite texto significativo e clique em **Adicionar**.

   	Isso envia um novo item como uma inserção no serviço móvel.

    Você pode reiniciar o aplicativo para ver se as alterações foram persistidas no banco de dados no Azure. Você também pode examinar o banco de dados usando o portal de gerenciamento do Azure: as próximas duas etapas fazem isso para exibir as alterações no banco de dados.


4. No Portal de Gerenciamento do Azure, clique em gerenciar para o banco de dados associado a seu serviço móvel.

    ![](./media/mobile-services-dotnet-backend-android-get-started-data-EC/manage-sql-azure-database.png)

5. No Portal de Gerenciamento, execute um consulta para exibir as mudanças feitas pelo aplicativo da Windows Store. Sua consulta será semelhante à consulta a seguir, mas usará o nome de seu banco de dados em vez de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-android-get-started-data-EC/sql-azure-query.png)

Isso conclui o tutorial **Introdução aos dados** para Android.


## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo Android para trabalhar com dados nos Serviços Móveis.

Tente um destes outros tutoriais:

* [Introdução à autenticação] <br/>Saiba como autenticar usuários de seu aplicativo.

* [Introdução às notificações por push] <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

* [Referência conceitual do tutorial do Android para os Serviços Móveis](mobile-services-android-how-to-use-client-library.md) <br/>Saiba mais sobre como usar os Serviços Móveis com Android.
  
<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Introdução à autenticação]: mobile-services-dotnet-backend-android-get-started-users.md
[Introdução às notificações por push]: mobile-services-dotnet-backend-android-get-started-push-EC.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030

<!---HONumber=August15_HO7-->