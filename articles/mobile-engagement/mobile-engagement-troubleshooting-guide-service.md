<properties 
   pageTitle="Guia de solução de problemas do Mobile Engagement do Azure - serviço" 
   description="Guias de solução de problemas para o Mobile Engagement do Azure" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>


# Guia de solução de problemas de serviço

A seguir, possíveis problemas que podem ser encontrados na execução do Mobile Engagement do Azure.

## Interrupções de serviço

### Problema
- Problemas que parecem ser provocados por interrupções de serviços do Mobile Engagement do Azure.

### Causas
- Os problemas que parecem ser provocados por interrupções de serviços do Mobile Engagement do Azure podem ser causados por vários problemas diferentes:
    - Problemas isolados que originalmente parecem sistêmicos para o Mobile Engagement do Azure
    - Problemas conhecidos causados por interrupções de servidor (nem sempre são mostrados no status do servidor):
	- Atrasos de agendamento, erros de direcionamento, problemas de atualização de notificação, interrupção da coleta de estatísticas, envio por push para de funcionar, interrupção do funcionamento das APIs, não é possível criar novos aplicativos ou usuários, erros de DNS e erros de tempo limite na interface do usuário, na API ou em aplicativos em um dispositivo.
    - Interrupções de dependência da nuvem [Status do serviço do Azure](http://status.azure.com/), [Status do Amazon Web Services (AWS)](http://status.aws.amazon.com/) 
    - Interrupções de dependências dos Serviços de Notificação por Push (PNS) [Google - serviço](http://www.google.com/appsstatus#hl=en&v=status), [Apple - serviço](http://www.apple.com/support/systemstatus/), [Android - GCM do Google](http://developer.android.com/google/gcm/index.html), [Android - ADM da Amazon](https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple - APNS](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), [Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone - MPNS][LinkMPNS], [Windows - WNS](https://developer.windows.com/)
    - Interrupções de loja de aplicativos [GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Windows Phone Store](http://www.windowsphone.com/), [Windows Store](http://windows.microsoft.com/)

*Para testar para ver se o problema é sistêmico, você pode testar a mesma função de um serviço diferente:*

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
*Para testar se o problema afeta somente a interface do usuário ou a API:*

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[Documentação da API][Link 4], [Documentação da interface do usuário][Link 1]

*Para testar se o problema é com a rede de telefonia celular:*

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*Para testar se o problema é com o dispositivo:*

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[Documentação da interface do usuário - configurações][Link 1]

*Para testar se o problema é com seu aplicativo:*

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

Para testar se o problema é com as atualizações do sistema operacional para dispositivos do usuário final, que exigem uma atualização do SDK para resolver:

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[Guia de solução de problemas - SDK][Link 2]
 
## Problemas de conectividade e informações incorretas

### Problema
- Problemas de logon na interface do usuário do Mobile Engagement do Azure.
- Erros de conexão com as APIs do Mobile Engagement do Azure.
- Problemas ao carregar marcas de informações de aplicativo por meio da API do dispositivo.
- Problemas de download de logs ou de dados exportados do Mobile Engagement do Azure.
- Informações incorretas mostradas na interface do usuário do Mobile Engagement do Azure.
- Informações incorretas mostradas nos logs do Mobile Engagement do Azure.

### Causas
- Para problemas de conectividade com o Mobile Engagement do Azure:
    - Confirme se sua conta de usuário tem permissões suficientes para executar a tarefa.
    - Confirme se o problema não está restrito a um computador ou à sua rede local.
    - Confirme se o serviço do Mobile Engagement do Azure não tem nenhuma interrupção relatada.
    - Confirme se os arquivos de marca de informações do aplicativo seguem todas estas regras:
        - Usam somente o conjunto de caracteres UTF8 (não há suporte para o conjunto de caracteres ANSI).
        - Usam uma vírgula "," como o caractere separador (você pode abrir uma solicitação de serviço para pedir a alteração do caractere separador do .csv de uma vírgula "," para um outro caractere, como um ponto e vírgula ";").
        - Usam letras maiúsculas para valores boolianos “verdadeiro” e “falso”.
        - Usam um arquivo menor do que o tamanho máximo de 35 MB.

### Consulte também

[Documentação da API][Link 4], [Documentação da interface do usuário - página inicial][Link 1]
 
## Solicitações de recursos

### Problema
- Parece que o recurso que você deseja usar ainda não existe no Mobile Engagement do Azure.

### Causas

Para sugerir um novo recurso para o Mobile Engagement do Azure que ainda não existe: - abra uma solicitação de serviço do Mobile Engagement do Azure com o máximo de detalhes possível sobre o novo recurso que você gostaria de ver no Mobile Engagement do Azure.

### Consulte também

[Comentários/solicitações de recurso do Mobile Engagement](http://feedback.azure.com/forums/285737-mobile-engagement)

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
[LinkMPNS]: http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx

<!--HONumber=54--> 