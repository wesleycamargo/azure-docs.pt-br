<properties 
   pageTitle="Aplicativo de API do Conector do Twitter" 
   description="Como usar o Conector do Twitter" 
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
   ms.date="03/20/2015"
   ms.author="adgoda"/>


# Usando o conector do Twitter em seu aplicativo lógico #

Aplicativos lógicos podem ser disparados com base em uma variedade de fontes de dados e oferecem conectores para obter e processar dados como uma parte do fluxo.

O Conector do Twitter permite que você publique tweets e obtenha tweets da sua linha do tempo, dos amigos e seguidores de sua conta do Twitter.

- O gatilho do Conector do Twitter recupera novos tweets associados a uma determinada palavra-chave. Quando um novo tweet é recuperado, ele dispara uma nova instância do fluxo e transmite os dados recebidos na solicitação para o fluxo de processamento. 
- As ações do Conector do Twitter permitem que você publique tweets, pesquise tweets, retweet, obtenha a linha do tempo dos usuários e assim por diante. Essas ações obtêm uma resposta e a disponibilizam para consumo pelas ações no fluxo.

## Criando um Conector do Twitter para seu aplicativo lógico ##
Para usar o Conector do Twitter, você precisa primeiro criar uma instância do aplicativo de API do Conector do Twitter. Isso pode ser feito da seguinte maneira:

1. Abra o Azure Marketplace usando a opção +NOVO na parte inferior esquerda do Portal do Azure.
1. Navegue até "Aplicativos de API" e pesquise "Conector do Twitter".
1. Configure o Conector do Twitter da seguinte maneira:

	![][1]
4.	Clique em OK para criar.
5.	Depois de criar a instância do aplicativo de API, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o Conector do Twitter. 
	- A instância do aplicativo de API do Conector do Twitter também pode ser criada no aplicativo lógico. 
	- Abra o editor do aplicativo lógico e clique no Conector do Twitter disponível na galeria do lado direito
	- Isso criará uma instância do aplicativo de API do Conector do Twitter no mesmo grupo de recursos no qual o aplicativo lógico foi criado.


## Usando o Conector do Twitter em seu aplicativo lógico ##
Depois de criar seu aplicativo de API, você pode usar o Conector do Twitter como ação/gatilho para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Criar um novo aplicativo lógico e escolher o mesmo grupo de recursos com o Conector do Twitter.
 	
	![][2]
2.	Abrir "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo. 
 	
	![][3]
3.	O Conector do Twitter apareceria na seção “Usados Recentemente" na galeria à direita. Selecione-o.
 
	![][4]
4.	Você pode soltar o aplicativo de API do Conector do Twitter no editor clicando em “Conector do Twitter”, em "Usados Recentemente”, na galeria à sua direita. Clique no botão Autorizar. Forneça suas credenciais do Twitter. Clique em "Autorizar Aplicativo"
 
	![][5]
6.	Agora você pode usar o Conector do Twitter no fluxo. Agora você pode usar os tweets recuperados do gatilho do Twitter em outras ações no fluxo.
 
	![][6]
7.	De maneira semelhante, você pode usar as ações do Twitter no fluxo. Selecione uma ação do Twitter e configure as entradas para a respectiva ação.

	![][7] ![][8]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/img6.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/img8.png
 

<!---HONumber=62-->