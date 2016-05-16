<properties
	pageTitle="Aplicativo de Demonstração do Azure Mobile Engagement"
	description="Descreve onde baixar, como usar e encontrar benefícios ao utilizar o aplicativo de demonstração do Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2016"
	ms.author="piyushjo" />

# Aplicativo de Demonstração do Azure Mobile Engagement

Publicamos um aplicativo de demonstração do Azure Mobile Engagement para as plataformas **iOS**, **Android** e **Windows** para ajudar você a localizar recursos úteis e saber mais sobre o Azure Mobile Engagement.

O aplicativo ajuda você a:

1. Encontrar facilmente links úteis para recursos específicos do Azure Mobile Engagement, como vídeos, documentação, fórum de suporte, onde ir para gerar solicitações de recursos, etc. 
2. Experimente notificações de exemplo com suporte pelo Azure Mobile Engagement para ter ideias para seus próprios aplicativos móveis. 
3. Fornece uma implementação de referência que você pode usar para estudar como implementar o Mobile Engagement em seu próprio aplicativo para: 

	- coletar dados de análise 
	- implementar cenários de notificação avançados de tipos, como a *Tela inteira intersticial* ou *Pop-up*
	- implementar Pesquisas/Votações
	- implementar envio por push silencioso/cenários de push de dados   

## Instalação do aplicativo
Cada aplicativo está disponível em sua respectiva loja de aplicativos:

1. **Aplicativo Universal de Demonstração do Windows**

	- [Baixe o link na Windows Store](https://www.microsoft.com/pt-BR/store/apps/azure-mobile-engagement/9nblggh4qmh2) 
	- O aplicativo foi desenvolvido como um aplicativo universal do Windows 10 e o código-fonte está disponível no [Github](https://github.com/Azure/azure-mobile-engagement-app-ios)

2. **Aplicativo de Demonstração do iOS**

	- [Baixe o link na Apple Store](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090) 
	- O aplicativo foi desenvolvido no iOS Swift e o código-fonte está disponível no [Github](https://github.com/Azure/azure-mobile-engagement-app-ios)

3. **Aplicativo de Demonstração do Android**

	- [Baixe o link na Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement)
	- O código-fonte está disponível no [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android)

![][1]

![][2] ![][3]


## Uso

Você pode usar esses aplicativos das seguintes formas:

**1) Baixe os aplicativos em seu dispositivo dos links da loja de aplicativos fornecidos acima.**

>[AZURE.IMPORTANT] Você não precisa ter uma conta do Azure ou conectar o aplicativo a um back-end. O aplicativo funcionará de forma independente.

- Uma vez que o aplicativo estiver em seu dispositivo, você poderá, então, percorrer os links no menu à esquerda para localizar todos os recursos úteis sobre o Azure Mobile Engagement. 
- Também adicionamos nosso [feed RSS do serviço](https://aka.ms/azmerssfeed) diretamente no aplicativo para que você sempre saiba sobre as últimas atualizações do produto.
- Você também pode percorrer os cenários de notificação de exemplo para experimentar quais tipos de notificações têm suporte pelo Azure Mobile Engagement para cada plataforma. Essas notificações podem ser experimentadas localmente, ou seja, você pode clicar nos botões nas telas para mostrar a experiência de notificações que é idêntica a quando você envia as notificações da plataforma do Azure Mobile Engagement. 

![][4]
    
![][5] ![][6]

**2) Baixe o código-fonte dos links do Github fornecidos acima.**

- Depois de baixar o código-fonte, abra-o no respectivo ambiente de desenvolvimento, ou seja, XCode para o iOS, Android Studio para Android e o Visual Studio para Windows. 
- Em seguida, siga nossas [etapas de integração do SDK básico](mobile-engagement-windows-store-dotnet-get-started.md) para que você possa se conectar a esse aplicativo em sua própria instância de back-end do Mobile Engagement. 
	- Você precisará configurar uma cadeia de conexão no aplicativo.  
	- Você também precisará configurar a plataforma de notificação por push no seu aplicativo. 
- Você observará que esse aplicativo em si é instrumentado com o Azure Mobile Engagement e, portanto, conforme você abre o aplicativo depois de se conectar a ele no back-end, você será capaz de ver a sessão do usuário, as atividades, os eventos, etc., na guia Monitorar. 
- Você também poderá enviar notificações para este aplicativo de sua própria instância do Mobile Engagement em vez de usar as notificações locais. 
	- Aqui você pode adicionar seu dispositivo como um dispositivo de teste usando o item de menu **obter a ID do dispositivo** no aplicativo que lhe dará uma identificação de dispositivo que você, então, registrará como um dispositivo de teste com sua instância de back-end de plataforma. 

	![][7]
	    
	![][8] ![][9]

## Principais recursos do Aplicativo de Demonstração

1. Conforme mencionado acima, com este aplicativo, você tem todos os recursos principais do Azure Mobile Engagement em suas mãos. Você pode percorrer os links no menu à esquerda. 

2. Experimente as notificações fora do aplicativo para cada plataforma. Essas notificações podem ser entregues como **somente notificação**, nas quais um clique na notificação fará com que uma tela nativa do aplicativo seja aberta (usando a **vinculação profunda**), ou como um **comunicado da Web**, no qual você pode entregar o conteúdo HTML de back-end adicional do Mobile Engagement a ser exibido quando a notificação é clicada.

	![][29]

	
	- No iOS, você terá que fechar o aplicativo para ver as notificações por push fora do aplicativo ou do sistema. Você pode examinar a implementação aqui para adicionar os **Botões de ação**, como aqueles adicionados nesta notificação de fora do aplicativo para *Comentários* e *Compartilhamento*, de forma que o usuário possa tomar providências desde a notificação em si. 
	    
	![][11] ![][14]
	
	
	- No Android, você verá as opções com suporte do Android na forma de adicionar texto de várias linhas (**Texto grande**) ou de adicionar uma imagem na notificação (**Panorama**) para a notificação junto com os **Botões de ação**, conforme suporte do iOS. 
	
	![][12] ![][15]
	
	
	- No Windows 10, você pode ver a aparência das notificações no PC. Essa notificação também será exibida na **Central de notificação** do Windows 10. Não há suporte para a adição de **Botões de ação** com o SDK do Windows no momento. 
	
	![][10] ![][13]

3. Experimente as notificações no aplicativo padrão para cada plataforma. Esta é uma experiência de duas etapas, na qual uma janela de **Notificação** é exibida pela primeira vez e que, quando clicada, abre uma tela inteira de **Comunicado**, conforme mostrado abaixo. O conteúdo deste comunicado vem da sua instância de back-end do Mobile Engagement. O SDK tem os modelos de notificação e comunicado que podem ser facilmente personalizados, como neste aplicativo de demonstração, com a adição do nosso logotipo e cores.

	![][16]
	
	![][17] **iOS** ![][18] **Android**

4. Você também pode usar o recurso **Categoria** do Azure Mobile Engagement para personalizar essa experiência padrão. No aplicativo de demonstração, demonstramos duas maneiras de alterar a experiência das notificações. Observe que ainda não há suporte para o recurso Categoria no Windows SDK.

	**Tela inteira intersticial**
	
	![][30]

	![][21] ![][22]

	**Notificação de pop-up**

	![][31]
	
	![][19] ![][20]

5. O Azure Mobile Engagement também dá suporte a um tipo especializado de notificação no aplicativo chamado **Votações**, que permite que você envie pesquisas rápidas para usuários de seu aplicativo segmentado. Você pode adicionar perguntas e opções para cada pergunta semelhante à seguinte, que será, então, exibida como uma notificação no aplicativo para o usuário do aplicativo.

	![][32]

	![][26]
	    
	![][27] **iOS** ![][28] **Android**

5. O Azure Mobile Engagement também dá suporte para o envio de notificações silenciosas de **Push de Dados**, nas quais você pode enviar alguns dados do seu serviço, como os dados JSON no exemplo de notificação a seguir, que você pode manipular no seu aplicativo e executar alguma ação. Por exemplo, de que modo estamos alterando o preço de um item usando a notificação por Push de Dados de forma seletiva.

	![][33]

	![][23]
	    
	![][24] **iOS** ![][25] **Android**

> [AZURE.NOTE] Observe que você pode exibir instruções detalhadas de qualquer uma dessas notificações clicando em *Clique aqui para obter instruções sobre como enviar essas notificações de plataforma do Mobile Engagement* em qualquer tela de notificação de exemplo.


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png

<!---HONumber=AcomDC_0504_2016-->