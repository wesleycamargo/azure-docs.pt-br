<properties 
	pageTitle="Enviar notificação personalizada com o Azure Mobile Engagement" 
	description="Como enviar notificações personalizadas incluindo informações de perfil do usuário nas notificações, como seus nomes"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="all" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/19/2016" 
	ms.author="piyushjo" />

#Personalizar as notificações ao incluir o nome de usuário

Em sua busca por tornar as notificações mais atraentes aos usuários de seu aplicativo, você deve considerar a personalização. Uma abordagem eficiente é formada pelo uso seletivo dos nomes de usuários do aplicativo a fim de endereçar as notificações e torná-las mais pessoais. Porém, um aviso: adicione os nomes de usuário às notificações com cuidado, pois se você exagerar no uso dessa estratégia, ela poderá parecer assustadora para alguns usuários do aplicativo. Você também deve assegurar a escolha do usuário em fornecer, com permissão total no aplicativo móvel, esses detalhes pessoais antes de começar a usá-los.

Tecnicamente, com o Azure Mobile Engagement, você pode personalizar as notificações executando as etapas abaixo, nas quais usaremos o cenário de inclusão de nome de usuário nas notificações. Você usará o conceito de Informações do Aplicativo ou Marcas, cujos valores podem ser passados pelos SDKs integrados ao Aplicativo Móvel ou por meio de APIs. Estas Informações do Aplicativo ou Marcas podem ser usadas:

1. para direcionar notificações a usuários específicos com base nos valores das Informações do Aplicativo ou
2. como espaços reservados nas notificações, que serão substituídos por valores específicos ao usuário/dispositivo durante o envio das notificações para esse dispositivo.

> [AZURE.IMPORTANT] Observe que a velocidade de envio das notificações diminuirá devido ao processamento adicional de substituição de valores de informações do aplicativo para cada notificação.

##Registrar as Informações do Aplicativo no Portal do Mobile Engagement

1) Comece registrando as Informações do Aplicativo ou Marcas no Portal do Azure. Acesse **Configurações** -> **Marca (Informações do Aplicativo)** para isso.

![][1]

2) Clique em **Nova marca (informações do aplicativo)** e forneça o nome como *nome\_de\_usuário* e o tipo como *cadeia de caracteres* e clique em **Enviar**.

![][2]

3) Por fim, você verá as informações do aplicativo registradas da seguinte forma:

![][3]

##Enviar Informações do Aplicativo do SDK do cliente

Estamos usando o exemplo de Aplicativo Universal do Windows, mas também há métodos equivalentes para nossos outros SDKs.

Supondo que você tenha um método no aplicativo móvel em que obtém as informações de perfil do usuário, como seu nome após a autenticação, chame o método `SendAppInfo` e preencha o valor das informações do aplicativo `user_name` registradas anteriormente no back-end do serviço Mobile Engagement.

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

##Enviar notificações personalizadas

Agora você está pronto para enviar notificações usando este **nome\_de\_usuário**.

1) Acesse o Portal do Mobile Engagement na guia **Alcance** para criar uma notificação e use esse espaço reservado no formato a seguir em qualquer lugar do título ou do corpo da notificação.

![][4]

> [AZURE.NOTE] Os usuários para os quais as informações do aplicativo nome\_de\_usuário não estejam definidas não receberão qualquer notificação. Se você executar a campanha de notificação no modo de teste e não tiver as informações do aplicativo definidas, enviaremos o caractere '?' para substituir o espaço reservado.

2) Quando o Mobile Engagement selecionar um dispositivo para envio dessa notificação, ele examinará essas informações do aplicativo e substituirá o valor no espaço reservado. Por exemplo, se tivermos definido `str = "Scott"` para um usuário, então o registro do dispositivo será associado às informações do aplicativo de **nome\_de\_usuário = PEDRO** desse usuário e esse usuário verá uma notificação por push fora do aplicativo no formato a seguir.

![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png

<!---HONumber=AcomDC_0824_2016-->