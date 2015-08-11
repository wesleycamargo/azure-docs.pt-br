<properties 
	pageTitle="Use o emoticon Emoji em notificações por Push" 
	description="Como usar emoticons Emoji dentro de suas notificações de push"					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="piyushjo" />

#Use o emoticon Emoji em notificações por Push

Você pode incluir emoticons Emoji em notificações por push. Atualmente, o Mobile Engagement do Azure oferece suporte apenas a emoticons Emoji de 3 bytes definidos para dentro e fora de notificações de texto do aplicativo. Siga as etapas abaixo:

1.  Primeiro, você precisa encontrar uma biblioteca de emoticons Emoji de 3 bytes. Você pode localizar todos os emoticons Emoji que pode usar no seguinte [link](http://stackoverflow.com/questions/10153529/emoji-on-mysql-and-php-why-some-symbol-yes-other-not).

	![][1]

2. Vá até a guia Reach em seu portal do Mobile Engagement do Azure.

3. Selecione o tipo de sua notificação por push (sonda lançamento, etc). Para este exemplo, escolhemos um envio de anúncio.

4. Especifique os campos diferentes da notificação até alcançar o texto da notificação. Este é o lugar onde você irá adicionar seu emoticom Emoji. Você pode optar por colocá-lo no título, na mensagem ou em ambos.

	![][2]

5. Recorte o emoticon Emoji que você deseja usar no link anterior. Cole-o diretamente no título e/ou nmensagem no local escolhido.

6. Preencha os outros campos para a notificação e salve-o.

7. Quando você executar um teste ou ativar o anúncio, verá uma notificação com o emoticon conforme especificado.

	![][3] 
	![][4]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/emoji.png
[2]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/notification_android.png
[4]: ./media/mobile-engagement-use-emoji-with-push/notification_ios.png
 

<!-----HONumber=July15_HO4-->