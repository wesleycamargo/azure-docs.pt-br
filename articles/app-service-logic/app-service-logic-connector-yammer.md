<properties
   pageTitle="Usando o Conector do Yammer em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
	description="Como criar e configurar o Conector do Yammer ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
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


# Usando o conector do Yammer em seu aplicativo lógico #
Conecte-se ao Yammer e à ação Postar Mensagens e um gatilho para recuperar novas mensagens.

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo.

## Criando um conector do Yammer para seu aplicativo lógico ##
Para usar o conector do Yammer, primeiro você precisa criar uma instância do aplicativo de API do conector do Yammer. Isso pode ser feito da seguinte maneira:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Pesquise "Conector do Yammer", selecione-o e selecione **Criar**.
3. Configure o conector do Yammer da seguinte maneira: ![][1]

	- **Local** - escolha a região geográfica onde você quer que o conector seja implantado
	- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
	- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
	- **Plano do Serviço de Aplicativo** - selecione ou crie um plano de hospedagem na Web
	- **Camada de preços** - escolha uma camada de preços para o conector
	- **Nome** - dê um nome para o conector do Yammer

4. Clique em Criar. Será criado um novo Conector do Yammer.
5. Após criar a instância do aplicativo de API, você pode criar um aplicativo lógico para usar o conector do Yammer.

## Usando o conector do Yammer em seu aplicativo lógico ##
Depois de criar seu aplicativo de API, você pode usar o conector do Yammer como gatilho/ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Crie um novo Aplicativo Lógico: ![][2]

2.	Abra "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo: ![][3]

3.	O conector do Yammer será exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito: ![][4]

4. Você pode soltar o aplicativo de API do conector do Yammer no editor clicando em "Conector do Yammer". Clique no botão Autorizar. Forneça suas credenciais do Yammer. Clique em "Permitir": ![][5] ![][6] ![][7]

Agora você pode usar o conector do Yammer no fluxo.

## Usar o conector do Yammer como um gatilho

Você pode usar a nova mensagem recuperada do gatilho do Yammer ("Nova Mensagem") em outras ações no fluxo. Configure as propriedades de entrada do gatilho do Yammer da seguinte maneira:

- **ID do Grupo** - a ID do grupo do qual a nova mensagem deve ser recuperada. Se a ID do Grupo não for fornecida, a mensagem será recuperada do feed a seguir. A ID do Grupo pode ser recuperada da URL do Grupo no Yammer.
		
	Exemplo: a ID do Grupo na URL abaixo é "5453203": https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203 ![][8] ![][9]

## Usar o conector do Yammer para postar uma mensagem

Você também pode usar o conector do Yammer como uma ação em seus aplicativos lógicos. Primeiro, especifique um gatilho para o seu aplicativo lógico ou marque 'executar esta lógica manualmente' (como mostrado abaixo). Adicione o conector do yammer, conceda a autorização apropriada e escolha a ação "Postar Mensagem". Configure as propriedades de entrada para a ação "Postar Mensagem" da seguinte maneira:

- **Texto da Mensagem** - conteúdo do texto da mensagem a ser postada
- **ID do Grupo** - especifique a ID do grupo no qual a nova mensagem deve ser postada. Se a ID do Grupo não for fornecida, a mensagem será postada no feed Toda a Empresa. A ID do Grupo pode ser recuperada da URL do Grupo no Yammer.  

	Exemplo: a ID do Grupo na URL abaixo é "5453203": https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
- 	**Marcar Usuários** - Matriz de nomes de rede de usuários que precisam ser marcados na mensagem: ![][10] ![][11]

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-yammer/img1.PNG
[2]: ./media/app-service-logic-connector-yammer/img2.PNG
[3]: ./media/app-service-logic-connector-yammer/img3.png
[4]: ./media/app-service-logic-connector-yammer/img4.png
[5]: ./media/app-service-logic-connector-yammer/img5.PNG
[6]: ./media/app-service-logic-connector-yammer/img6.PNG
[7]: ./media/app-service-logic-connector-yammer/img7.png
[8]: ./media/app-service-logic-connector-yammer/img8.PNG
[9]: ./media/app-service-logic-connector-yammer/img9.PNG
[10]: ./media/app-service-logic-connector-yammer/img10.PNG
[11]: ./media/app-service-logic-connector-yammer/img11.PNG

<!---HONumber=August15_HO9-->