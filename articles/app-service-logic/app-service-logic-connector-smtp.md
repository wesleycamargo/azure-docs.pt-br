<properties
   pageTitle="Usando o Conector SMTP em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
	description="Como criar e configurar o Conector SMTP ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
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
	ms.author="andalmia"/>


# Introdução ao Conector SMTP e à adição dele ao seu Aplicativo Lógico
Conecte-se a um servidor SMTP e envie emails, incluindo emails com anexos. A ação "Enviar Email" do Conector de SMTP permite que você envie um email ao(s) endereço(s) de email especificado(s).

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte de um fluxo de trabalho. Você pode adicionar o Conector SMTP a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico.


## Gatilhos e Ações
*Gatilhos* são eventos que ocorrem. Por exemplo, quando um pedido é atualizado ou quando um novo cliente é adicionado. Uma *Ação* é o resultado do gatilho. Por exemplo, quando um pedido é atualizado ou um novo cliente é adicionado, enviar um email para o novo cliente.

O conector de SMTP pode ser usado como uma ação em um aplicativo lógico e dá suporte a dados nos formatos JSON e XML. Atualmente, não há gatilhos disponíveis para esse conector.

O conector de SMTP tem os seguintes gatilhos e ações disponíveis:

Gatilhos | Ações
--- | ---
Nenhum | Enviar email


## Criar o conector de SMTP
Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Selecione **Aplicativos de API** e pesquise “Conector de SMTP”.
3. **Crie** o conector.
4. Digite o Nome, o Plano do Serviço de Aplicativo e outras propriedades.
5. Insira as seguintes configurações de pacote:

	Nome | Obrigatório | Descrição
	--- | --- | ---
	Nome de usuário | Sim | Insira um nome de usuário que pode se conectar ao servidor SMTP.
	Senha | Sim | Digite a senha do nome de usuário.
	Endereço do servidor | Sim | Digite o nome ou endereço IP do servidor SMTP.
	Porta do servidor | Sim | Insira o número da porta do servidor SMTP.
	Habilitar SSL | Não | Digite *true* para usar SMTP através de um canal SSL/TLS seguro.

6. Selecione **Criar**.

## Usando o Conector de SMTP em seu aplicativo lógico
Após a criação do conector, você poderá usar o conector de SMTP como uma ação para seu aplicativo lógico. Para fazer isso:

1.	Crie um novo aplicativo lógico:

	![][2]
2.	Abra **Gatilhos e ações** para abrir o designer de aplicativos lógicos e configurar seu fluxo de trabalho:

	![][3]
3.	O conector de SMTP é listado na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito. Selecione-o:

	![][4]
4.	Selecione o conector de SMTP para adicioná-lo automaticamente ao designer de fluxo de trabalho.

Agora você pode configurar o conector de SMTP para usar em seu fluxo de trabalho. Selecione a ação **Enviar email** e configure as propriedades de entrada:

	Propriedade | Descrição
	--- | ---
	To | Insira o endereço de email do(s) destinatário(s). Separe vários endereços de email com um ponto e vírgula (;). Por exemplo, insira: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Insira o endereço de email do(s) destinatário(s) de cópia carbono.  Separe vários endereços de email com um ponto e vírgula (;). Por exemplo, insira: *recipient1@domain.com;recipient2@domain.com*.
	Subject | Insira o assunto do email.
	Body | Insira o corpo do email.
	Is HTML | Quando essa propriedade está definida como true, o conteúdo do corpo é enviado como HTML.
	Bcc | Insira o endereço de email do(s) destinatário(s) de cópia oculta. Separe vários endereços de email com um ponto e vírgula (;). Por exemplo, insira: *recipient1@domain.com;recipient2@domain.com*.
	Importance | Insira a Prioridade do email. As opções são Normal, Baixa e Alta.
	Attachments | Os anexos a serem enviados com o email. A propriedade contém os seguintes campos: <ul><li>Content (String)</li><li>Content transfer Encoding (Enum) (“none”|”base64”)</li><li>Content Type (String)</li><li>Content ID (String)</li><li>File Name (String)</li></ul>

	![][5]
	![][6]

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=August15_HO9-->