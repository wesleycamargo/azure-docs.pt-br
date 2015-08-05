<properties
   pageTitle="Aplicativo de API do Conector do Twilio"
   description="Como usar o Conector do Twilio"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/02/2015"
   ms.author="sameerch"/>


# Usando o conector do Twilio em seu aplicativo lógico #

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo.

O conector do Twilio permite enviar e receber SMS de sua conta do Twilio. Também permite recuperar os números de telefone e dados de uso.

## Criando um conector do Twilio para seu aplicativo lógico ##
Para usar o conector do Twilio, primeiro você precisa criar uma instância do aplicativo de API do conector do Twilio. Isso pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace usando a opção +NOVO na parte inferior esquerda do Portal do Azure.
2.	Navegue até "Aplicativos de API" e pesquise "Conector do Twilio".
3.	Configure o conector do Twilio da seguinte maneira:

	![][1]
	- **Local** - escolha o local geográfico onde deseja que o conector seja implantado
	- **Assinatura** - escolha uma assinatura na qual deseja que esse conector seja criado
	- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
	- **Plano de hospedagem na Web** - selecione ou crie um plano de hospedagem na Web
	- **Camada de preços** - escolha uma camada de preços para o conector
	- **Nome** - dê um nome para o conector do Twilio
	- **Configurações do pacote**
		- **SID da conta** -O identificador exclusivo da conta. O SID da sua conta pode ser recuperado em <https://www.twilio.com/user/account/settings>
		- **Token de autorização** - Token de autorização associado à conta. O token de autorização da sua conta pode ser recuperado em <https://www.twilio.com/user/account/settings>


4.	Clique em Criar. Será criado um novo conector do Twilio.
5.	Após criar a instância do aplicativo de API, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o conector do Twilio.

## Usando o conector do Twilio em seu aplicativo lógico ##
Depois de criar seu aplicativo de API, você pode usar o conector do Twilio como ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Criar um novo aplicativo lógico e escolher o mesmo grupo de recursos que tem o conector do Twilio.

	![][2]
2.	Abrir "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo.

	![][3]
3.	O conector do Twilio seria exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito.

	![][4]
4. Você pode soltar o aplicativo de API do conector do Twilio no editor clicando em "Conector do Twilio".

5.	Agora você pode usar o conector do Twilio no fluxo. Você pode usar a ação "Enviar mensagem" no fluxo para enviar uma mensagem. Configure as propriedades de entrada para a ação "Enviar Mensagem" da seguinte maneira:
	- **Do Número de Telefone** - Insira um número de telefone do Twilio habilitado para o tipo de mensagem que você deseja enviar. Somente números de telefone ou códigos curtos adquiridos do Twilio funcionarão com esse conector.
	- **Para o Número de Telefone** - O número de telefone de destino. O formato aceito é: +, seguido do código do país e do número de telefone. Por exemplo, +16175551212 Se você omitir o +, o Twilio usará o código do país que você inseriu no número ‘De’.
	- **Texto** - O texto da mensagem que você deseja enviar.
 
	![][5]
	![][6]



	<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG

<!----HONumber=July15_HO4-->