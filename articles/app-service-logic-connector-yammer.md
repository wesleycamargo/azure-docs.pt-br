<properties 
   pageTitle="Aplicativo de API do conector do Yammer" 
   description="Como usar o conector do Yammer" 
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


# Usando o conector do Yammer em seu aplicativo lógico #

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. 

O conector do Yammer permite conectar-se ao Yammer e executar a ação Postar Mensagem e um gatilho para recuperar a nova mensagem.

## Criando um conector do Yammer para seu aplicativo lógico ##
Para usar o conector do Yammer, primeiro você precisa criar uma instância do aplicativo de API do conector do Yammer. Isso pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace usando a opção +NOVO na parte inferior esquerda do Portal do Azure.
2.	Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise "Conector do Yammer".
3.	Configure o conector do Yammer da seguinte maneira:
 
	![][1]
	- **Local** - escolha a região geográfica em que você deseja que o conector seja implantado
	- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
	- **Grupo de recursos** - selecione ou crie um grupo de recursos em que o conector deve residir
	- **Plano de hospedagem na Web** - selecione ou crie uma plano de hospedagem na Web
	- **Camada de preços** - escolha uma camada de preços para o conector
	- **Nome** - dê um nome ao conector do Yammer 

4.	Clique em Criar. Será criado um novo conector do Yammer.
5.	Após criar a instância do aplicativo de API, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o conector do Yammer. 

## Usando o conector do Yammer em seu aplicativo lógico ##
Depois de criar seu aplicativo de API, você pode usar o conector do Yammer como gatilho/ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Criar um novo aplicativo lógico e escolher o mesmo grupo de recursos que tem o conector do Yammer.
 
	![][2]
2.	Abra "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo. 
 
	![][3]
3.	O conector do Yammer seria exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito.
 
	![][4]
4. Você pode soltar o aplicativo de API do conector do Yammer no editor clicando em "Conector do Yammer". Clique no botão Autorizar. Forneça suas credenciais do Yammer. Clique em "Permitir"
 
	![][5]
	![][6]
	![][7]
5.	Agora você pode usar o conector do Yammer no fluxo. Você pode usar a nova mensagem recuperada do gatilho do Yammer ("Nova Mensagem") em outras ações no fluxo. Configure as propriedades de entrada do gatilho do Yammer da seguinte maneira:

	- **ID do Grupo** - a ID do grupo do qual a nova mensagem deve ser recuperada. Se a ID do Grupo não for fornecida, a mensagem será recuperada do feed a seguir. A ID do Grupo pode ser recuperada da URL do Grupo no Yammer.
		Exemplo: A ID do Grupo na URL abaixo é "5453203"
		https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
 
	![][8]
	![][9]

	

6.	De maneira semelhante, você pode usar a ação do Yammer no fluxo para postar uma mensagem, selecionando a ação "Postar Mensagem". Configure as propriedades de entrada para a ação "Postar Mensagem" da seguinte maneira:
	- **Texto da Mensagem** - Conteúdo de texto da mensagem a ser postada
	- **ID do Grupo** - Especifique a ID do grupo no qual a nova mensagem deve ser postada. Se a ID do Grupo não for fornecida, a mensagem será postada no feed Toda a Empresa. A ID do Grupo pode ser recuperada da URL do Grupo no Yammer. 
	Exemplo: A ID do Grupo na URL abaixo é "5453203"
	https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
	- 	**Marcar Usuários** - Matriz de nomes de rede de usuários que precisam ser marcados na mensagem. 
	- 	**Anexos** - anexos a serem postados junto com a postagem. Contém os seguintes campos:
		- Nome do Arquivo
		- Conteúdo
		- Codificação de transferência de conteúdo
		- Tipo de conteúdo

	![][10]
	![][11]




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

<!--HONumber=52-->