<properties
   pageTitle="Aplicativo de API do Conector de POP3"
   description="Como usar o Conector de POP3"
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


# Usando o Conector de POP3 em seu aplicativo lógico #

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo.

O Conector de POP3 permite que você se conecte a um servidor POP3 e fornece um gatilho para recuperar emails com anexos.

## Criando um conector de POP3 para seu aplicativo lógico ##
Para usar o conector de POP3, primeiro você precisa criar uma instância do aplicativo de API do conector de POP3. Isso pode ser feito diretamente do designer do aplicativo lógico ou de fora dele. Criar uma instância fora do designer pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace na home page do Portal do Azure.
2.	Em "Tudo", pesquise "Conector de POP3".
3.	Configure o Conector de POP3 da seguinte maneira:

	![][1] - **Local** - escolha o local geográfico onde deseja que o conector seja implantado - **Assinatura** - escolha uma assinatura na qual deseja que esse conector seja criado - **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir - **Plano de hospedagem na Web** - selecione ou crie um plano de hospedagem na web - **Camada de preços** - escolha uma camada de preços para o conector - **Nome** - dê um nome para o conector de POP3 - **Configurações do pacote** - **Nome de usuário** Especifique o nome de usuário para se conectar ao servidor POP3 - **Senha** Especifique a senha para se conectar ao servidor POP3 - **Endereço do servidor** Especifique o nome ou endereço IP do servidor POP3 - **Porta do servidor** Especifique o número da porta do servidor POP3 - **Habilitar SSL** Especifique true para usar o POP3 por meio de um canal SSL/TLS seguro
4.	Clique em Criar. Será criado um novo Conector de POP3.
5.	Após criar a instância do aplicativo de API, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o conector de POP3.

## Usando o Conector de POP3 em seu aplicativo lógico ##
Depois de criar seu aplicativo de API, você pode usar o conector de POP3 como gatilho para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Crie um novo aplicativo lógico e escolha o mesmo grupo de recursos que tem o conector de POP3.

	![][2]
2.	Abrir "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo.

	![][3]
3.	O conector de POP3 será exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito. Selecione-o.

	![][4]
4.	Você pode soltar o aplicativo de POP3 do Conector de SMTP no editor clicando em "Conector de POP3".

5.	Agora você pode usar o conector de POP3 no fluxo. Selecione o gatilho "Obter Email" e configure a frequência e o intervalo. Você pode usar o email recuperado do gatilho de POP3 em outras ações no fluxo.
		 

	![][5] ![][6]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-pop3/img1.PNG
[2]: ./media/app-service-logic-connector-pop3/img2.PNG
[3]: ./media/app-service-logic-connector-pop3/img3.png
[4]: ./media/app-service-logic-connector-pop3/img4.PNG
[5]: ./media/app-service-logic-connector-pop3/img5.PNG
[6]: ./media/app-service-logic-connector-pop3/img6.PNG

<!---HONumber=July15_HO4-->