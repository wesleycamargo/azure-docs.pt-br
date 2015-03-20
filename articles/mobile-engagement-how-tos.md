<properties 
   pageTitle="Como guiar o Azure Mobile Engagement" 
   description="Como guiar o Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement - Como guiar

## Introdução

O Como guiar a seguir para a Interface do usuário do Azure Mobile Engagement pressupõe que você esteja familiarizado com os [Conceitos] básicos[Link 6] do Azure Mobile Engagement e funcionará somente depois de você ter integrado o Azure Mobile Engagement [SDK][Link 5] em seu aplicativo. Essas instruções passo a passo se concentram em como os usuários padrão podem usar a [Interface do usuário][Link 1] em vez de como os usuários do desenvolvedor usaram o restante do HTTP com base em [APIs][Link 4]. Se você tiver dificuldade com qualquer uma dessas [Instruções passo a passo][Link 3] consulte o contrato do Azure Mobile Engagement [Guias de solução de problemas][Link 2].

## <a name="#First">Faça sua primeira campanha de notificação por push</a>
-    Confirme que o seu Reach está integrado ao seu aplicativo com o SDK. 
-    Selecione seu aplicativo
 
![First1][1]

-    Vá para a seção "Reach" e clique em "Novo comunicado"
 
![First2][2]

-    Criar uma nova campanha e nomeie-a
 
 ![First3][3]

-    Selecione como a notificação deve ser entregue, como no aplicativo somente
 
![First4][4]

-    Criar a mensagem que deseja enviar por push
 
![First5][5]

-    Você pode escrever um título nas notificações (opcional).
-    Gravar o conteúdo da mensagem por push.
-    Você pode carregar uma imagem. Lembre-se de que o tamanho do arquivo não pode exceder 32.768 bytes.
-    Você também tem a capacidade de selecionar mais opções, mas para manter o foco deste tutorial, veremos isso mais tarde.

-    Selecione o tipo de conteúdo como Notificação apenas
 
![First6][6]

-    Crie sua campanha de envio e ele aparecerá na lista de campanha.
 
![First7][7]

## <a name="#Test">Testar sua campanha de notificação por push</a>
 
![Test1][8]

-    Registre seu dispositivo.
-    Clique na caixa de seleção do dispositivo que deseja enviar por push.
-    Clique no botão "Testar" para enviar o envio por push para o dispositivo.
 
![Test2][9]

-    Ativar a campanha
 
![Test3][10]

-    Agora que você criou sua campanha basta ativá-la para a notificação ser enviada para os usuários.
 
## <a name="#Personalize">Enviar pressionamentos personalizados</a>
-    Este exemplo cria um esforço em que um código de desconto personalizado é inserido em uma notificação por push.
 
![Personalize1][11]

A personalização funciona substituindo um marcador por uma marca de informações do aplicativo, então, você precisará certificar-se de que o usuário tem as informações do aplicativo apropriado e definido primeiro. Neste exemplo, os usuários de destino terão uma marca definida de informações de aplicativo chamada rebate_code.
Como você pode ver acima, o conteúdo da notificação por push inclui o ${rebate_code} de marcador que indicará que ela deve ser substituída pelo conteúdo real da marca de informações do aplicativo.

> Aviso: Se a marca de informações de aplicativo não estiver definida para o usuário, o usuário não receberá o envio por push.

-    Resultado
 
![Personalize2][12]

### Você pode personalizar ainda mais o texto da notificação
 
![Personalize3][13]

-    Incluindo o título da notificação,
-    E o conteúdo da mensagem.
-    Escolha o tipo de comunicado (exibição de texto ou da Web)
 
![Personalize4][14]

### O corpo de um comunicado também pode ser personalizado com:
-    A URL da ação, caso você deseje personalizar a página de aterrissagem
-    O título
-    O corpo da mensagem.
 
 
## <a name="#Differentiate">Diferenciar a sua notificação por push (estando ou não no aplicativo)</a>

-    Escolha o tipo de notificação você enviará por push, selecione seu aplicativo, vá para a seção "Alcance", selecione ou crie uma campanha de envio por push e vá para a seção "Notificação".
 
-    Clique no "modo de entrega" desejado.
-    Clique na caixa de seleção "Restringir atividades" quando desejar que a notificação ocorra em atividades específicas (telas).

![Differentiate1][15]

### Modo de entrega "Somente fora do aplicativo"
 
![Differentiate2][16]

O modo de entrega "Somente fora do aplicativo" fornece notificação por push quando o aplicativo é fechado. Esta é a notificação por push padrão.
Ao selecionar "Somente fora do aplicativo", você deve ter fornecido os certificados da plataforma que o seu aplicativo está compilando (APNS ou GCM).

**Consulte também:** 

-  [Serviço de Notificação por Push da Apple - Certificados](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), Google Cloud Messaging - Certificado](http://developer.android.com/google/gcm/index.html) 

### Modo de entrega "Apenas no aplicativo"
 
![Differentiate3][17]

O modo de entrega "Apenas no aplicativo" fornece notificação por push quando o aplicativo é executado.
Para esta notificação, você não precisa passar pelo sistema APNS e GCM.
Você pode usar o sistema de entrega no aplicativo para alcançar os seus usuários finais.
Você pode personalizar completamente a notificação e decidir em qual atividade (tela) a notificação será exibida.

### Modo de entrega a "Qualquer hora"
Você pode escolher um modo de entrega a "Qualquer hora", e isso garante alcançar o seu usuário final se o aplicativo estiver em execução ou não.
Ao selecionar a "Qualquer hora", você já deve ter fornecido os certificados da plataforma em que o seu aplicativo está complementando (APNS ou GCM). 
 
## <a name="#Schedule">Agendar uma campanha de envio por push</a>
 
### Planejar iniciar uma campanha
 
![Shedule1][18]

É 21 de março e você tem um comunicado para fazer e ele está planejado para o dia 22 de março à meia-noite. 
Você não precisa estar na frente da interface para realizar um envio por push! Você pode planejar com antecedência o exato minuto em que as notificações serão enviadas.
-    Desmarque a caixa de seleção "Nenhum" e selecione uma hora de início 
-    Escolha a data e a hora em que você deseja iniciar a campanha de envio por push.
### Planejar terminar uma campanha
 
![Shedule2][19]

Você deseja que a sua campanha seja encerrada no dia 25 de março às 15h, mas sabe que não estará para fazê-lo.
Você não precisa estar na frente da interface para realizar um envio por push! Você pode planejar com antecedência o exato minuto que sua campanha será interrompida.
-    Clique na caixa de seleção "Nenhum" ou selecione uma hora de término
-    Escolha a data e a hora em que você deseja terminar a campanha de envio por push.
### Terminar uma campanha manualmente
 
![Shedule3][20]

Por padrão, as caixas de seleção "Nenhum" estão marcadas.
Isso significa que a campanha começará assim que você ativá-la na seção de alcance e terminará quando você a terminar na seção de alcance.
 
> Observação: As campanhas criadas sem uma data de término armazenam o envio por push localmente no dispositivo e o mostram na próxima vez que o aplicativo é aberto, mesmo que a campanha seja encerrada manualmente.

## <a name="#TextView">Aprimorar uma notificação por push com uma exibição de texto</a>

### O que é uma exibição de texto?
 
![TextView1][21]

Uma exibição de texto é um pop-up com conteúdo de texto. Essa janela pop-up aparece depois que o usuário final tiver clicado em uma notificação por push.
Uma exibição de texto permite apresentar mais conteúdo ao usuário final. Essa também é a oportunidade de apresentar um plano de ação, como saltar para uma página do seu aplicativo, redirecionando para um Repositório, abrindo uma página da Web, enviando um email, iniciando uma pesquisa localizada geograficamente, etc...

### Exemplo: Exibição de texto
-    Crie a sua campanha de notificação por push na seção "Alcance" e atribua um nome à sua campanha
 
![TextView2][22]

-    Escreva a mensagem que será exibida na notificação.
-    Selecione o tipo de conteúdo do comunicado de "texto"
 
![TextView3][23]

> Observação: quando você envia por push uma exibição de texto, ela sempre vem com uma notificação pela primeira vez. 

- Definir o texto (depois de ter selecionado o conteúdo do comunicado de texto, a subseção será exibida, permitindo que você defina o texto a ser exibido.)
 
![TextView4][24]

-    Escreva o título que aparece na parte superior da mensagem.
-    Escreva o conteúdo principal da exibição de texto.
-    Escreva o conteúdo que será exibido no botão de ação (um botão de ação habilita o aplicativo a fazer uma ação específica, como abrir uma página do aplicativo, redirecionar para um repositório de aplicativos ou qualquer tipo de fonte que você possa fornecer).
-    Escreva o conteúdo que será exibido no botão Sair (clicando no botão Sair, o modo de texto desaparecerá).
 
-    Crie sua campanha de notificação por push e ela aparecerá na lista de campanha.
 
![TextView5][25]

-    Ative sua campanha de notificação por push para enviar a exibição de texto para seus usuários.
 
![TextView6][26]

-    Resultado
 
![TextView7][27]

-    O usuário recebe a notificação e clica nela.
-    A exibição de texto aparece como um pop-up permitindo que o usuário interaja com ele.

## <a name="#WebView">Aprimorar uma notificação por push com uma exibição da web</a>

### O que é uma exibição da Web?
 
![WebView1][28]

Uma exibição da web é um pop-up com conteúdo da web. Essa janela pop-up aparece quando o usuário final tiver clicado em uma notificação por push.
Uma exibição da web permite que você tenha mais interação com o usuário final.
Essa também é a oportunidade de apresentar um plano de ação, como redirecionamento para a Loja de Aplicativos, abrir uma página da Web, enviar um email, iniciar uma pesquisa localizada geograficamente, etc...

### Exemplo: Modo de exibição da Web

-    Crie a sua campanha de envio por push na seção "Alcance" e atribua um nome à sua campanha.
 
![WebView2][29]

-    Escreva a mensagem que será exibida na notificação.
-    Selecione o tipo de conteúdo do comunicado como "web"
 
![WebView3][30]

**Sobre os tipos de comunicado:**

- Somente notificação: É uma notificação padrão simples. Isso significa que se um usuário clica nele, nenhum modo de exibição adicional será exibido, mas apenas a ação associada a ele ocorrerá.
- Comunicado de texto: É uma notificação que envolve o usuário para observar uma exibição de texto.
- Comunicado na Web: É uma notificação que envolve o usuário para observar uma exibição da web.
Selecione o conteúdo de "Comunicado da Web".

> Observação: quando você envia por push uma exibição da web, ela sempre vem com uma notificação pela primeira vez.

- Defina o conteúdo da web (depois de ter selecionado o conteúdo de comunicado da web, será exibida a subseção, permitindo que você defina o conteúdo da exibição da web a ser exibido.)

 
![WebView4][31]

-    Escreva o título que aparecerá na parte superior da mensagem (opcional).
-    Escreva o código HTML aqui.
-    Clique no botão de modo da fonte de edição para alternar a edição e ver como ele parece.
-    Escreva o conteúdo que será exibido no botão de ação (um botão de ação habilita o aplicativo a fazer uma ação específica, como abrir uma página do aplicativo, redirecionar para um Repositório ou qualquer tipo de fonte que você possa fornecer).
-    Escreva o conteúdo que será exibido no botão Sair (clicando no botão Sair, a exibição da web desaparecerá).
 
-    Resultado
 
![WebView5][32]

-    O usuário recebe a notificação e clica nela.
-    A exibição de texto aparece como um pop-up permitindo que o usuário interaja com ele.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
