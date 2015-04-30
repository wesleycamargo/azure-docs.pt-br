<properties 
   pageTitle="Aplicativo de API do Conector do Barramento de Serviço do Azure" 
   description="Como usar o Conector do Barramento de Serviço do Azure" 
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


# Usando o Conector do Barramento de Serviço do Azure em seu aplicativo lógico #

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. 

O Conector do Barramento de Serviço do Azure permite receber e enviar mensagens para uma fila/tópico/assinatura.

## Criando um Conector do Barramento de Serviço do Azure para seu aplicativo lógico ##
Para usar o Conector do Barramento de Serviço do Azure, primeiro você precisa criar uma instância do aplicativo de API do Conector do Barramento de Serviço do Azure. Isso pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace usando a opção +NOVO na parte inferior esquerda do Portal do Azure.
2.	Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise "Conector do Barramento de Serviço do Azure".
3.	Configure o Conector do Barramento de Serviço do Azure da seguinte maneira:
 
	![][1]
	- **Local** - escolha a região geográfica onde você quer que o conector seja implantado
	- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
	- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
	- **Plano de hospedagem na Web** - selecione ou crie um plano de hospedagem na Web
	- **Camada de preços** - escolha uma camada de preços para o conector
	- **Nome** - dê um nome ao Conector do Barramento de Serviço do Azure
	- **Configurações do pacote**
		- **Cadeia de Conexão** - a cadeia de conexão para o Barramento de Serviço do Azure. Exemplo: Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=[name];SharedAccessKey=[key]
		- **Nome da Entidade** - o nome da fila ou tópico
		- **Nome da Assinatura** - o nome da assinatura da qual receber mensagens. 

4.	Clique em Criar. Será criado um novo Conector do Barramento de Serviço do Azure.
5.	Após criar a instância do aplicativo de API, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o Conector do Barramento de Serviço do Azure. 

## Usando o Conector do Barramento de Serviço do Azure em seu aplicativo lógico ##
Depois de criar seu aplicativo de API, você pode usar o Conector do Barramento de Serviço do Azure como gatilho/ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Crie um novo aplicativo lógico e escolha o mesmo grupo de recursos que tem o Conector do Barramento de Serviço do Azure.
 
	![][2]
2.	Abra "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo. 
 
	![][3]
3.	O Conector do Barramento de Serviço do Azure será exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito.
 
	![][4]
4. Você pode soltar o aplicativo de API do Conector do Barramento de Serviço do Azure no editor clicando em "Conector do Barramento de Serviço do Azure".
 
5.	Agora você pode usar o Conector do Barramento de Serviço do Azure no fluxo. Você pode usar a mensagem recuperada do gatilho do Barramento de Serviço do Azure ("Mensagem Disponível") em outras ações no fluxo.
 
	![][5]
	![][6] 
6.	De maneira semelhante, você pode usar a ação do Barramento de Serviço do Azure "Enviar Mensagem" para enviar uma mensagem

	![][7]
	![][8]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG

<!--HONumber=52-->