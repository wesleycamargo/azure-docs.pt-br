<properties
   pageTitle="Usando o Conector do Twilio em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
   description="Como criar e configurar o Conector do Twilio ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
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
   ms.date="08/23/2015"
   ms.author="sameerch"/>


# Introdução ao Conector do Twilio e à adição dele ao seu Aplicativo Lógico
Conecte-se à sua conta do Twilio para enviar e receber mensagens SMS. Você também pode recuperar os números de telefone e os dados de uso. Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. Você pode adicionar o conector do Twilio a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico.

## Criando um conector do Twilio para seu aplicativo lógico ##
Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Procure "Conector do Twilio", selecione-o e selecione **Criar**.
3. Configure o conector do Twilio da seguinte maneira: ![][1]  
	- **Local** - escolha a região geográfica onde você quer que o conector seja implantado
	- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
	- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
	- **Plano de hospedagem na Web** - selecione ou crie uma plano de hospedagem na Web
	- **Camada de preços** - escolha uma camada de preços para o conector
	- **Nome** - dê um nome ao conector do Twilio
	- **Configurações do pacote**
		- **SID da Conta** - o identificador exclusivo da conta. O SID da sua conta pode ser recuperado de <https://www.twilio.com/user/account/settings>
		- **Token de Autorização** - o token de autorização associado à conta. O token de autorização da sua conta pode ser recuperado em <https://www.twilio.com/user/account/settings>


4.	Clique em Criar. Será criado um novo Conector do Twilio.
5.	Depois de criar a instância do aplicativo de API, você pode criar um Aplicativo Lógico no mesmo grupo de recursos para usar o Conector do Twilio.

## Usando o conector do Twilio em seu aplicativo lógico ##
Depois de criar seu aplicativo de API, você pode usar o conector do Twilio como ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Criar um novo Aplicativo Lógico e escolher o mesmo grupo de recursos que tem o conector do Twilio: ![][2]
2.	Abra "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo: ![][3]
3.	O conector do Twilio seria exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito: ![][4]
4. Você pode soltar o aplicativo de API do conector do Twilio no editor clicando em "Conector do Twilio".

5.	Agora você pode usar o conector do Twilio no fluxo. Você pode usar a ação "Enviar mensagem" no fluxo para enviar uma mensagem. Configure as propriedades de entrada para a ação "Enviar Mensagem" da seguinte maneira:
	- **Do Número de Telefone** - Insira um número de telefone do Twilio habilitado para o tipo de mensagem que você deseja enviar. Somente números de telefone ou códigos curtos adquiridos do Twilio funcionarão com esse conector.
	- **Para o Número de Telefone** - O número de telefone de destino. O formato aceito é: +, seguido do código do país e do número de telefone. Por exemplo, +16175551212 Se você omitir o +, o Twilio usará o código do país que você inseriu no número ‘De’.
	- **Texto** - O texto da mensagem que você deseja enviar.

	![][5] ![][6]

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST do Swagger em [Conectores e referência dos Aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG

<!---HONumber=Oct15_HO3-->