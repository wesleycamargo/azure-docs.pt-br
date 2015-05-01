<properties 
   pageTitle="Aplicativo de API de conector do Chatter" 
   description="Como usar o conector do Chatter" 
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
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# Usando o Conector do Chatter em seu aplicativo lógico #

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. 

O Conector do Chatter permite que você se conecte ao Chatter e executar várias ações, como Postar Mensagem e Pesquisar, e fornece um gatilho para recuperar novas mensagens.

## Criando um conector do Chatter para seu aplicativo lógico ##
Para usar o conector do Chatter, primeiro você precisa criar uma instância do aplicativo de API do conector do Chatter. Isso pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace usando a opção +NOVO na parte inferior esquerda do Portal do Azure.
2.	Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise "Conector do Chatter".
3.	Configure o Conector do Chatter da seguinte maneira:
 
	![][1]
	- **Local** - escolha a região geográfica onde você quer que o conector seja implantado
	- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
	- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
	- **Plano de hospedagem na Web** - selecione ou crie uma plano de hospedagem na Web
	- **Camada de preços** - escolha uma camada de preços para o conector
	- **Nome** - dê um nome ao Conector do Chatter 

4.	Clique em Criar. Será criado um novo Conector do Chatter.
5.	Após criar a instância do aplicativo de API, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o conector do Chatter. 

## Usando o Conector do Chatter em seu aplicativo lógico ##
Depois de criar seu aplicativo de API, você pode usar o conector do Chatter como gatilho/ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Crie um novo aplicativo lógico e escolha o mesmo grupo de recursos que tem o Conector do Chatter.
 
	![][2]
2.	Abra "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo. 
 
	![][3]
3.	O conector do Chatter será exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito.
 
	![][4]
4. Você pode soltar o aplicativo de API do Conector do Chatter no editor clicando em "Conector do Chatter". Clique no botão Autorizar. Forneça suas credenciais. Clique em "Permitir"
 
	![][5]
	![][6]
	![][7]
5.	Agora você pode usar o conector do Chatter no fluxo. Você pode usar a nova mensagem recuperada do gatilho do Chatter ("Nova Mensagem") em outras ações no fluxo. Configure as propriedades de entrada de gatilho do Chatter da seguinte maneira:
	- **ID do Grupo** - especifique a ID do grupo do qual a nova mensagem deve ser recuperada. Se não for fornecida uma ID de grupo, a nova mensagem será recuperada do Feed do Usuário. 

 
	![][8]
	![][9]


6. De maneira semelhante, você pode usar a ação do Chatter no fluxo para postar uma mensagem, selecionando a ação "Postar Mensagem". Configure as propriedades de entrada para a ação "Postar Mensagem" da seguinte maneira:
	- **Texto da Mensagem** - conteúdo do texto da mensagem a ser postada
	- **ID do Grupo** - especifique a ID do grupo no qual a nova mensagem deve ser postada. Se a ID do grupo não for fornecida, a mensagem será postada no feed do usuário.
	- 	**Nome do Arquivo** - nome do arquivo a ser anexado a essa mensagem
	- 	**Dados de Conteúdo** - dados de conteúdo do anexo
	- 	**Tipo de Conteúdo** - tipo de conteúdo do anexo
	- 	**Codificação de Transferência de Conteúdo** - codificação de transferência de conteúdo do anexo ("none"|"Base64")
	- 	**Menções** - matriz de nomes de usuário a serem marcados nessa mensagem
	- 	**Hashtags** - matriz de hashtags a serem postadas juntamente com a mensagem

	![][10]
	![][11] 




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


<!--HONumber=52-->