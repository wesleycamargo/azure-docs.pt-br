<properties 
	pageTitle="Usando o Conector do Slack em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
	description="Como criar e configurar o Conector do Slack ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
	authors="rajeshramabathiran" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="rajram"/>

# Introdução ao Conector do Slack e à adição dele ao seu Aplicativo Lógico
Conecte-se a canais de margem de atraso e poste mensagens para sua equipe. OS conectores podem ser usados em aplicativos lógicos como parte de um "fluxo de trabalho" para executar diferentes tarefas. Ao usar o conector de margem de atraso no fluxo de trabalho, você pode obter uma diversos cenários usando outros conectores. Por exemplo, você pode usar o [conector do Facebook](app-service-logic-connector-facebook.md) no fluxo de trabalho para postar uma mensagem em seu canal de margem de atraso.

## Gatilhos e Ações
*Gatilhos* são eventos que ocorrem. Por exemplo, quando um pedido é atualizado ou quando um novo cliente é adicionado. Uma *Ação* é o resultado do gatilho. Por exemplo, quando uma ordem é atualizada, enviar um alerta para o vendedor. Ou, quando um novo cliente for adicionado, enviar um email de boas-vindas para ele.

O conector de margem de atraso pode ser usado como uma ação em um aplicativo lógico e dá suporte a dados nos formatos JSON e XML. Atualmente, não há gatilhos disponíveis para o conector de margem de atraso.

O conector de margem de atrasos tem os seguintes gatilhos e ações disponíveis:

Gatilhos | Ações
--- | ---
Nenhum | Postar mensagem

## Criar o conector de margem de atraso
Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Selecione **Aplicativos de API** e pesquise “Conector de margem de atraso”.
3. Digite o Nome, o Plano do Serviço de Aplicativo e outras propriedades: ![][1] 

4. Clique em **Criar**.

## Usando o conector como uma ação em seu aplicativo lógico

> [AZURE.IMPORTANT]Os aplicativos lógicos e conectores sempre devem ser criados no mesmo grupo de recursos.

Após criar o conector de margem de atraso, você poderá adicioná-lo como uma ação ao seu aplicativo lógico:

1.	No Aplicativo Lógico, abra **Gatilhos e ações**. [Criar um novo aplicativo lógico](app-service-logic-create-a-logic-app.md)

2.	O conector do Slack é listado na galeria do lado direito: ![][2]

3.	Selecione o conector de margem de atraso criado para adicioná-lo automaticamente ao aplicativo lógico.
4.	Selecione **Autorizar**. Entre em sua conta de margem de atraso. No final, será solicitado que você dê permissão a seu conector para acessar sua conta de margem de atraso. Selecione **Authorizify**: ![][3] ![][4] ![][5] ![][6]  
	
5.	Agora você pode usar o conector de margem de atraso no fluxo. A ação Postar Mensagem está disponível: ![][7]


Percorramos a experiência “Postagem”. Você pode usar essa ação para postar uma mensagem em qualquer Canal do Slack: ![][8]

Configure as propriedades de entrada da ação "Postar mensagem":

Propriedade | Descrição
--- | ---
Texto | Especifique o texto da mensagem a ser postada.
Nome do canal | Informe o canal de margem de atraso no qual a mensagem é postada. Se o canal não for especificado, a mensagem será postada em #general.
Propriedades avançadas | **Nome de usuário do Bot**: nome do bot a ser usado para esta mensagem. A mensagem será postada como "Bot" se essa propriedade não for especificada.<p><p>**URL do Ícone**: A URL da imagem a ser usada como o ícone da mensagem.<p><p>**Emoji do Ícone**: O Emoji a ser usado como o ícone da mensagem. Essa propriedade substitui a propriedade Ícone URL.


O conector de atraso tem APIs REST disponíveis para que você possa usar o conector fora de um aplicativo lógico. Abra o Conector do Slack de atraso e selecione **Definição de API**: ![][9]


## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-slack/img1.PNG
[2]: ./media/app-service-logic-connector-slack/img2.PNG
[3]: ./media/app-service-logic-connector-slack/img3.PNG
[4]: ./media/app-service-logic-connector-slack/img4.PNG
[5]: ./media/app-service-logic-connector-slack/img5.PNG
[6]: ./media/app-service-logic-connector-slack/img6.PNG
[7]: ./media/app-service-logic-connector-slack/img7.PNG
[8]: ./media/app-service-logic-connector-slack/img8.PNG
[9]: ./media/app-service-logic-connector-slack/img9.PNG

<!---HONumber=Nov15_HO3-->