<properties
   pageTitle="Usando o Conector do Chatter em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
   description="Como criar e configurar o Conector do Chatter ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/10/2016"
   ms.author="sameerch"/>


# Introdução ao Conector do Chatter e à adição dele ao seu Aplicativo Lógico 
>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview de aplicativos lógicos.

Conecte-se ao Chatter e poste uma mensagem ou pesquise um feed. Por exemplo, você pode pesquisar um feed do Chatter e ao encontrar algo específico, é possível postar a mensagem do Chatter em um grupo de Vendas.

Você pode adicionar o conector do Chatter a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um aplicativo lógico.

## Gatilhos e Ações

Um Gatilho inicia uma nova instância com base em um evento específico, como a chegada de uma nova mensagem do Chatter. Uma Ação é o resultado, como ocorre após receber uma nova mensagem do Chatter e depois postá-la em outro grupo do Chatter ou em outro site de mídia social, como o Facebook ou o Twitter.

O Conector do Chatter pode ser usado como gatilho ou ação em um aplicativo lógico e dá suporte a dados nos formatos JSON e XML. O conector do Chatter tem os seguintes Gatilhos e Ações disponíveis:

Gatilhos | Ações
--- | ---
Nova Mensagem | <ul><li>Postar Mensagem</li><li>Pesquisar</li></ul>


## Criar um conector do Chatter para o aplicativo lógico
Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Pesquise "Conector do Chatter", selecione-o e selecione **Criar**.
3. Insira o Nome, o Plano do Serviço de Aplicativo e outras propriedades: 
	![][1]  
	- **Local** - escolha a região geográfica onde você quer que o conector seja implantado
	- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
	- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
	- **Plano de hospedagem na Web** - selecione ou crie uma plano de hospedagem na Web
	- **Camada de preços** - escolha uma camada de preços para o conector
	- **Nome** - dê um nome ao Conector do Chatter

4. Selecione **Criar**.


## Usando o Conector do Chatter em seu aplicativo lógico
Depois de criar seu aplicativo de API, você pode usar o conector do Chatter como gatilho ou ação em seu aplicativo lógico. Para fazer isso:

1. No aplicativo lógico, abra **Gatilhos e Ações** para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo.

2. O conector do Chatter está listado na galeria:  
	![][4]
3. Selecione o conector do Chatter para adicionar automaticamente no designer. Selecione **Autorizar**, insira as suas credenciais e selecione **Permitir**: 
	![][5] 
	![][6] 
	![][7]

Agora você pode usar o conector do Chatter no fluxo. Você pode usar a nova mensagem recuperada do gatilho do Chatter ("Nova Mensagem") em outras ações no fluxo. Configure as propriedades de entrada de gatilho do Chatter da seguinte maneira:

**ID do Grupo** - insira a ID do grupo do qual a nova mensagem deve ser recuperada. Se a ID do Grupo não for fornecida, a nova mensagem será recuperada do Feed do Usuário: 
	![][8] 
	![][9]


De maneira semelhante, você pode usar a ação do Chatter no fluxo para postar uma mensagem, selecionando a ação "Postar Mensagem". Configure as propriedades de entrada para a ação "Postar Mensagem" da seguinte maneira:
	- **Texto da Mensagem** - conteúdo de texto da mensagem a ser postada
	- **ID do Grupo** - especifique a ID do grupo no qual a nova mensagem deve ser postada. Se a ID do grupo não for fornecida, a mensagem será postada no feed do usuário.
	- 	**Nome do Arquivo** - nome do arquivo a ser anexado a essa mensagem
	- 	**Dados de Conteúdo** - dados de conteúdo do anexo
	- 	**Tipo de Conteúdo** - tipo de conteúdo do anexo
	- 	**Codificação de Transferência de Conteúdo** - codificação de transferência de conteúdo do anexo ("nenhuma"|"base64")
	- 	**Menções** - matriz de nomes de usuário a serem marcados nessa mensagem
	- 	**Hashtags** - matriz de hashtags a serem postadas juntamente com a mensagem  

![][10] 
![][11]

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-chatter/img1.PNG
[2]: ./media/app-service-logic-connector-chatter/img2.PNG
[3]: ./media/app-service-logic-connector-chatter/img3.png
[4]: ./media/app-service-logic-connector-chatter/img4.png
[5]: ./media/app-service-logic-connector-chatter/img5.PNG
[6]: ./media/app-service-logic-connector-chatter/img6.PNG
[7]: ./media/app-service-logic-connector-chatter/img7.PNG
[8]: ./media/app-service-logic-connector-chatter/img8.PNG
[9]: ./media/app-service-logic-connector-chatter/img9.PNG
[10]: ./media/app-service-logic-connector-chatter/img10.PNG
[11]: ./media/app-service-logic-connector-chatter/img11.PNG

<!---HONumber=AcomDC_0224_2016-->