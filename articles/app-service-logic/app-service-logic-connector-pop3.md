<properties
   pageTitle="Usando o Conector POP3 em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
   description="Como criar e configurar o Conector POP3 ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
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


# Introdução ao Conector POP3 e à adição dele a seu Aplicativo Lógico
>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview de aplicativos lógicos.

Conecte-se a um servidor POP3 para recuperar emails, incluindo emails com anexos.

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. Você pode adicionar o Conector POP3 a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico.


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

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-pop3/img1.PNG
[2]: ./media/app-service-logic-connector-pop3/img2.PNG
[3]: ./media/app-service-logic-connector-pop3/img3.png
[4]: ./media/app-service-logic-connector-pop3/img4.PNG
[5]: ./media/app-service-logic-connector-pop3/img5.PNG
[6]: ./media/app-service-logic-connector-pop3/img6.PNG

<!---HONumber=AcomDC_0224_2016-->