<properties
   pageTitle="Usando o conector POP3 em Aplicativos lógicos | Serviço de Aplicativo do Microsoft Azure"
   description="Como criar e configurar o conector POP3 ou o aplicativo de API e usá-lo em um Aplicativo lógico no Serviço de Aplicativo do Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="sameerch"/>


# Introdução ao conector POP3 e adição dele ao seu Aplicativo lógico
>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview dos Aplicativos Lógicos.

Conecte-se a um servidor POP3 para recuperar emails, incluindo emails com anexos.

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. Você pode adicionar o conector POP3 a seu fluxo de trabalho de negócios e processar os dados como parte desse fluxo de trabalho dentro de um Aplicativo lógico.


## Criando um conector POP3 para seu Aplicativo lógico ##
Para usar o conector de POP3, primeiro você precisa criar uma instância do aplicativo de API do conector de POP3. Isso pode ser feito diretamente a partir do designer do Aplicativo lógico ou fora dele. Criar uma instância fora do designer pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace na home page do Portal do Azure.
2.	Em "Tudo", pesquise "Conector de POP3".
3.	Configure o Conector de POP3 da seguinte maneira:

	![][1]
	- **Local** - escolha a região geográfica onde você quer que o conector seja implantado
	- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
	- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
	- **Plano de hospedagem na Web** - selecione ou crie uma plano de hospedagem na Web
	- **Camada de preços** - escolha uma camada de preços para o conector
	- **Nome** - dar um nome para o conector POP3
	- **Configurações do pacote**
		- **Nome de usuário** Especificar o nome de usuário para conectar o Servidor POP3
		- **Senha** Especificar a senha para conectar o Servidor POP3
		- **Endereço do Servidor** Especificar o nome do Servidor POP3 ou o endereço IP
		- **Porta do servidor** Especificar o número da porta do Servidor POP3
		- **Habilitar SSL** Especificar true para usar POP3 em um canal SSL/TLS seguro
4.	Clique em Criar. Será criado um novo conector POP3.
5.	Após criar a instância do aplicativo de API, você poderá criar um Aplicativo Lógico no mesmo grupo de recursos para usar o conector POP3.

## Usando o conector POP3 em seu Aplicativo lógico ##
Depois de criar seu aplicativo de API, você poderá usar o conector POP3 como gatilho para seu Aplicativo lógico. Para fazer isso, você precisa:

1.	Crie um novo Aplicativo lógico e escolha o mesmo grupo de recursos que tem o conector POP3.

	![][2]
2.	Abra "Gatilhos e Ações" para abrir o Designer de Aplicativos lógicos e configure seu fluxo.

	![][3]
3.	O conector de POP3 será exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito. Selecione-o.

	![][4]
4.	Você pode soltar o aplicativo de API do conector POP3 no editor clicando em "Conector POP3".

5.	Agora você pode usar o conector de POP3 no fluxo. Selecione o gatilho "Obter Email" e configure a frequência e o intervalo. Você pode usar o email recuperado do gatilho de POP3 em outras ações no fluxo.
		 

	![][5] 
	![][6]

## Fazer mais com seu conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se você quiser começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar Aplicativo lógico](https://tryappservice.azure.com/?appservice=logic), onde poderá criar imediatamente um Aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Confira [Gerenciar e Monitorar seus Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-pop3/img1.PNG
[2]: ./media/app-service-logic-connector-pop3/img2.PNG
[3]: ./media/app-service-logic-connector-pop3/img3.png
[4]: ./media/app-service-logic-connector-pop3/img4.PNG
[5]: ./media/app-service-logic-connector-pop3/img5.PNG
[6]: ./media/app-service-logic-connector-pop3/img6.PNG

<!---HONumber=AcomDC_0727_2016-->