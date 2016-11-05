---
title: Introdução aos Serviços Móveis para Xamarin.Android | Microsoft Docs
writer: craigd
description: Saiba como usar serviços móveis do Azure com seu aplicativo Android Xamarin.
documentationcenter: xamarin
author: lindydonna
manager: dwrede
editor: ''
services: mobile-services

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/21/2016
ms.author: donnam

---
# <a name="getting-started"></a>Introdução aos Serviços Móveis
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Para obter a versão de Aplicativos Móveis equivalente a este tópico, veja [Criar um Aplicativo Xamarin.Android](../app-service-mobile/app-service-mobile-xamarin-android-get-started.md).
> 
> 

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Xamarin.Android usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel.

Se você preferir assistir a um vídeo, o clipe abaixo segue as mesmas etapas deste tutorial.

Vídeo: “Introdução aos Serviços Móveis do Xamarin e do Azure" com Craig Dunn, desenvolvedor evangelista para Xamarin (duração: 10:05 min)

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services/player]
> 
> 

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial exige o XCode e o Xamarin Studio para OS X ou o Visual Studio no Windows com um Mac em rede. Encontre instruções de instalação completas em [Instalar e configurar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx),

O projeto quickstart baixado contém o componente dos Serviços Móveis do Azure para o Xamarin.Android. Embora esse projeto tenha como alvo o Android 4.2 ou uma versão posterior, o SDK dos Serviços Móveis exige apenas o Android 2.2 ou uma versão posterior.

> [!IMPORTANT]
> Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5).
> 
> 

## <a name="create-new-service"> </a>Criar um novo serviço móvel
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar um novo aplicativo Xamarin.Android
Depois de criar seu serviço móvel, você poderá seguir um início rápido fácil no portal clássico do Azure para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção, você criará um novo aplicativo Xamarin.Android que está conectado ao seu serviço móvel.

1. No [portal clássico do Azure], clique em **Serviços Móveis** e clique no serviço móvel que você acabou de criar.
2. Na guia de início rápido, clique em **Xamarin.Android** sob **Escolher plataforma** e expanda **Criar um novo aplicativo Android**.
   
    ![][6]
   
    Isso exibe as três etapas fáceis de criar um aplicativo Xamarin.Android conectado a seu serviço móvel.
   
    ![][7]
3. Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.
4. Em **Baixe e execute o aplicativo**, clique em **Baixar**.
   
    Isso baixa o projeto para o aplicativo de exemplo *To do list* que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo Android
O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o local onde você salvou os arquivos do projeto compactados e expanda os arquivos em seu computador.
2. No Xamarin Studio ou no Visual Studio, clique em **Arquivo** e, em seguida, em **Abrir**, navegue até os arquivos de exemplo não compactados e selecione **XamarinTodoQuickStart.Android.sln** para abri-lo.
3. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo. Você será solicitado a selecionar um emulador ou um dispositivo USB conectado.
   
   > [!NOTE]
   > Para que seja possível executar o projeto no emulador do Android, você deverá definir no mínimo um AVD (Dispositivo Virtual Android). Use o Gerenciador do AVD para criar e gerenciar esses dispositivos.
   > 
   > 
4. No aplicativo, digite um texto significativo, como *Concluir o tutorial* e clique em **Adicionar**.
   
    ![][10]
   
    Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.
   
   > [!NOTE]
   > Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados que estão localizados no arquivo ToDoActivity.cs C#.
   > 
   > 
5. Novamente no [portal clássico do Azure], clique na guia **Dados** e clique na tabela **TodoItems**.
   
    ![][11]
   
    Isso permite que você procure os dados inseridos pelo aplicativo na tabela.
   
    ![][12]

## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

* [Introdução à sincronização de dados offline] Saiba como o início rápido usa dados offline para tornar o seu aplicativo responsivo e robusto.
* [Introdução à autenticação] Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.
* [Introdução às notificações por push] Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introdução à sincronização de dados offline]: mobile-services-xamarin-android-get-started-offline-data.md
[Introdução à autenticação]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introdução às notificações por push]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure]: http://azure.microsoft.com/
[portal clássico do Azure]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0727_2016-->