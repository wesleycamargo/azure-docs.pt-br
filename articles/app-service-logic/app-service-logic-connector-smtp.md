<properties
   pageTitle="Aplicativo de API do Conector de SMTP"
   description="Como usar o Conector de SMTP"
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
   ms.date="07/01/2015"
   ms.author="andalmia"/>


# Conector de SMTP

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte de um fluxo de trabalho.

O Conector de SMTP permite que você se conecte a um servidor SMTP e execute uma ação para enviar emails com anexos. A ação "Enviar Email" do Conector de SMTP permite que você envie um email ao(s) endereço(s) de email especificado(s).

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

	Property | Description
	--- | ---
	To | Enter the email address of recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Enter the email address of the carbon copy recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Subject | Enter the subject of the email.
	Body | Enter body of the email.
	Is HTML | When this property is set to true, the contents of the body are sent as HTML.
	Bcc | Enter the email address of recipient(s) for blind carbon copy. Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Importance | Enter the Importance of the email. The options are Normal, Low, and High.
	Attachments | Attachments to be sent along with the email. It contains the following fields: <ul><li>Content (String)</li><li>Content transfer Encoding (Enum) (“none”|”base64”)</li><li>Content Type (String)</li><li>Content ID (String)</li><li>File Name (String)</li></ul>

	![][5]
	![][6]

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Crie aplicativos de API usando APIs REST. Consulte [Referência a aplicativos de API e conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

	<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=July15_HO5-->