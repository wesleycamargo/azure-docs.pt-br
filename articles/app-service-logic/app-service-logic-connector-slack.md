<properties 
	pageTitle="Conector de margem de atraso"
	description="Introdução ao conector de margem de atraso"
	authors="anuragdalmia" 
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
	ms.date="04/21/2015"
	ms.author="andalmia"/>

# Usando o conector de margem de atraso em seu aplicativo lógico #

Aplicativos lógicos podem ser disparados com base em uma variedade de fontes de dados e oferecem conectores para obter e processar dados como uma parte do fluxo.

O conector de margem de atraso permite que você envie mensagens para canais de margem de atraso.

## Criando um conector de margem de atraso para seu aplicativo lógico ##
Para usar o conector de margem de atraso, você precisa primeiro criar uma instância do aplicativo de API do conector de margem de atraso. Isso pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace usando a opção "+ NOVO" na parte inferior esquerda do Portal do Azure.
2.	Navegue até "Web e Móvel > Azure Marketplace" e procure "Conector de margem de atraso".
3.	Configure o conector de margem de atraso da seguinte maneira:
 
	![][1] - **Nome** - dê um nome para o conector de margem de atraso - **Plano de serviço do aplicativo** - selecione ou crie um plano de serviço de aplicativo - **Camada de preços** - escolha uma camada de preços para o conector - **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir - **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado - **Local** -escolha o local geográfico onde quer que o conector seja implantado

4. Clique em Criar. Será criado um novo conector de margem de atraso.
5. Depois de criar a instância de aplicativo de API, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o conector de margem de atraso.

## Usando o conector de margem de atraso em seu Aplicativo Lógico ##
Após seu aplicativo de API ser criado, você pode usar o conector de margem de atraso como uma ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Criar um novo aplicativo lógico e escolher o mesmo grupo de recursos que contém o conector de margem de atraso. Siga as instruções para [Criar um novo aplicativo lógico].  	
	
2.	Abra "Gatilhos e ações" no aplicativo lógico criado para abrir o Designer de aplicativos lógicos e configurar seu fluxo.
	
3.	O conector de margem de atraso seria exibido na seção "Aplicativos de API neste grupo de recursos", na galeria do lado direito.
 
	![][2]
4.	Você pode colocar o aplicativo de API do conector de margem de atraso no editor clicando em "Conector de margem de atraso". Clique no botão Autorizar. Forneça suas credenciais da Microsoft (se não tiver entrado automaticamente). Entre na sua conta de margem de atraso, seguindo as etapas. No final, será solicitado que você dê permissão a seu conector para acessar sua conta de margem de atraso. Clique em "Authorizify"
 
	![][3] ![][4] ![][5] ![][6]
	
5.	Agora você pode usar o conector de margem de atraso no fluxo. Atualmente, os gatilhos não estão disponíveis no conector de margem de atraso. As ações disponíveis são - Postagem
 
	![][7]

6.	Percorramos a experiência “Postagem”. Você pode usar essa ação para postar uma mensagem em qualquer canal de margem de atraso.
 
	![][8]

	Configure as propriedades de entrada para a ação "Postagem" da seguinte maneira:

 - **Texto** - especifique o texto da mensagem a ser postada
 - **Nome do canal** - especifique o canal de margem de atraso para o qual esta mensagem será carregada. Se não for fornecido, em seguida, mensagem será publicada em #geral

 	**Propriedades avançadas** - **Nome de usuário de Bot** - nome do bot a ser usado para esta mensagem. A mensagem será lançada como "Bot" se essa propriedade não for especificada. - **Ícone URL** - URL a ser usada por uma imagem como o ícone para esta mensagem - **Emoji ícone** - Emoji a ser usado como o ícone para esta mensagem. Substitui a URL do ícone
 

7. Para usar o conector fora de um aplicativo lógico, as APIs REST exibidas pelo conector podem ser utilizadas. Você pode exibir essas definições da API usando Procurar -> Aplicativo de API -> Conector de margem de atraso. Agora clique na lente Definição de API sob a seção Resumo para exibir todas as APIs exibidas por esse conector.

	![][9]

9. Detalhes das APIs também podem ser encontrados em [Definição de API de margem de atraso].

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

<!-- Links -->
[Criar um novo aplicativo lógico]: app-service-logic-create-a-logic-app.md
[Definição de API de margem de atraso]: https://msdn.microsoft.com/pt-br/library/dn708020.aspx

<!---HONumber=62-->