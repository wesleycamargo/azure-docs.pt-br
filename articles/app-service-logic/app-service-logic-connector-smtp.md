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
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# Usando o Conector de SMTP em seu aplicativo lógico #

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo.

O Conector de SMTP permite que você se conecte a um servidor SMTP e execute uma ação para enviar emails com anexos. A ação "Enviar Email" do Conector de SMTP permite que você envie um email ao(s) endereço(s) de email especificado(s).

## Criando um conector de SMTP para seu aplicativo lógico ##
Para usar o conector de SMTP, primeiro você precisa criar uma instância do aplicativo de API do conector de SMTP. Isso pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace usando a opção +NOVO na parte inferior esquerda do Portal do Azure.
2.	Navegue até "Web e Dispositivos Móveis > Azure Marketplace" e pesquise "Conector SMTP".
3.	Configure o Conector de SMTP da seguinte maneira:
 
	![][1] - **Local** - escolha o local geográfico onde deseja que o conector seja implantado - **Assinatura** - escolha uma assinatura na qual deseja que esse conector seja criado - **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir - **Plano de hospedagem na Web** - selecione ou crie um plano de hospedagem na web - **Camada de preços** - escolha uma camada de preços para o conector - **Nome** - dê um nome para o conector SMTP - **Configurações do pacote** - **Nome de usuário** Especifique o nome de usuário para se conectar ao servidor SMTP - **Senha** Especifique a senha para se conectar ao servidor SMTP - **Endereço do servidor** Especifique o nome ou endereço IP do servidor SMTP - **Porta do servidor** Especifique o número da porta do servidor SMTP - **Habilitar SSL** Especifique true para usar o SMTP por meio de um canal SSL/TLS seguro
4.	Clique em Criar. Será criado um novo Conector de SMTP.
5.	Após criar a instância do aplicativo de API, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o conector do SMTP. 

## Usando o Conector de SMTP em seu aplicativo lógico ##
Depois que o aplicativo de API for criado, você poderá usar o Conector de SMTP como uma ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Crie um novo aplicativo lógico e escolha o mesmo grupo de recursos que tem o conector de SMTP.
 
	![][2]
2.	Abrir "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo. 
 
	![][3]
3.	O conector de SMTP será exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito. Selecione-o.
 
	![][4]
4.	Você pode soltar o aplicativo de API do Conector de SMTP no editor clicando em "Conector de SMTP". 
	
7.	Agora você pode usar o conector de SMTP no fluxo. Selecione a ação "Enviar Email" e configure as propriedades de entrada da seguinte maneira:
	- **Para** - Esse é o endereço de email do(s) destinatário(s). Separe vários endereços de email usando um ponto e vírgula (;). Por exemplo: recipient1@domain.com;recipient2@domain.com.
	- **Cc** - Esse é o endereço de email do(s) destinatário(s) com cópia oculta. Separe vários endereços de email usando um ponto e vírgula (;). Por exemplo: recipient1@domain.com;recipient2@domain.com.
	- **Assunto** - Esse é o assunto do email.
	- **Corpo** - Esse é o corpo do email.
	- **É HTML** - Quando essa propriedade for definida como true, o conteúdo do corpo será enviado como HTML.
	- **Cco** - Esse é o endereço de email do(s) destinatário(s) com cópia oculta. Separe vários endereços de email usando um ponto e vírgula (;). Por exemplo: recipient1@domain.com;recipient2@domain.com.
	- **Importância** - Essa é a importância do email. As opções são Normal, Baixa, Alta.
	- **Anexos** - Anexos a serem enviados junto com o email. Contém os seguintes campos:
		- Conteúdo (Cadeia de Caracteres)
		- Codificação de transferência de conteúdo (Enum) (“nenhuma”|”base64”)
		- Tipo de conteúdo (Cadeia de Caracteres)
		- ID de Conteúdo (Cadeia de Caracteres)
		- Nome do Arquivo (Cadeia de Caracteres)
	 
	
	![][5] ![][6]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG
 

<!---HONumber=62-->